# Interview Notes - game-api-provider-white-ip-control-plane

日期: 2026-05-27

## Step 4 正式面試稿

這份是 Step 4 的正式面試素材。主口說稿放在 `career-interview.md`；本檔保留追問題庫、回答要點、誇大陷阱與基本功檢查。

## 面試主軸

一句話:

> 這條是後台 control plane 影響 runtime access-control 的 case，重點是 DB 設定、Redis / in-memory cache、權限與 audit 之間的一致性邊界。

## 30 秒版

我參與過 UGSoft 後台 Game API / provider IP 白名單控制面。Game API 白名單是後台寫 DB 後同步 Redis set，connector runtime filter 用 agentId + source IP 判斷是否放行；provider 白名單 latest behavior 則是後台寫 DB 後 fanout 通知 connector reload provider IP cache。這條 case 我會重點講 DB source of truth、runtime cache stale、刪除 IP 的安全風險、權限 gate 與 operation log，而不把它誇大成完整 access-control platform。

## 90 秒版

UGSoft 的 Game API / provider white IP 是後台 control plane 影響 runtime access-control 的例子。Game API 白名單以 agentId 維度維護，後台新增時查 `agent_id + ip` 是否重複，寫 `game_api_login_ip_white`，再同步 Redis set `ugSoft:GameApiWhiteIp:{agentId}`。connector 的 `WhiteIpFilter` 會在 login / public / connect client 等 runtime path 取 agentId 和來源 IP 查 Redis，未命中就拒絕。

Provider 白名單是 provider callback 的入口控制。Nick direct evidence 是 provider white IP 表與 CRUD 初版；latest code 由主管後續改成 provider 全站共用，新增 / 刪除後 publish fanout，connector 收到後 reload in-memory provider IP cache。

這條 case 的 Senior 判斷是: DB 是設定 source of truth，但 Redis / in-memory cache 是 runtime view；DB + Redis 或 DB + fanout 不是同一個 transaction，所以一定有 partial failure。新增 IP cache stale 會造成新來源暫時被拒；刪除 IP cache stale 更危險，因為舊來源可能繼續被放行。因此要有 cache rebuild、reload observability、manual repair、權限 gate 和 operation log。

## 3 分鐘版

我會把這條 case 定位成後台 control plane 到 runtime access-control 的白名單設定。它不是一般 CRUD，因為後台新增或刪除一筆 IP，會直接影響 connector runtime 是否放行 API request 或 provider callback。

Game API 的流程是，後台 `/game_api_white_ip/add` 收到 agentId、IP、desc 後，先檢查 limited admin 權限，再查 `game_api_login_ip_white` 是否已有同 agent + IP。不存在就寫 DB，接著把 IP 加到 Redis set，key 是 `ugSoft:GameApiWhiteIp:{agentId}`。刪除時先查 ipId 對應 IP，刪 DB，再從 Redis set remove。connector-api 的 `WhiteIpFilter` 在受限 runtime path 會取 request 裡的 agentId 和來源 IP，去 Redis set 查是否命中。

Provider 白名單是另一條 callback access-control。Nick 的 direct evidence 是 provider white IP 表與 CRUD 初版；latest 行為由主管後續改成 provider 全站共用，新增或刪除後 publish fanout message，connector-api 每個節點收到後 reload provider -> allowed IP 的 in-memory cache，callback endpoint 再用 provider + source IP 判斷。

這裡我會主動講 owner decision。第一，DB 是 source of truth，cache 是 runtime view；只要跨 DB + Redis / MQ，就不是強一致。第二，刪除 IP 後 cache stale 比新增 IP 後 cache stale 更危險，因為它可能讓已撤銷的 IP 繼續通過。第三，provider white IP 從 agent-scoped 改成 global-scoped 是 access-control scope decision，管理變簡單但放行範圍變大；而這段是主管 current behavior，我會說我能分析，不會說成我主導。第四，白名單控制面必須有 RoleFilter、limited admin、operation log、reload log / metric，否則出事時很難追。

所以履歷或面試我會保守說: 我參與過 Game API / provider white IP 後台控制面與 DB / Redis 同步維護，能說清楚它如何影響 runtime access-control，以及 DB / cache 不一致、fanout reload、權限與 audit 的風險。

## STAR 版

Situation: 後台 Game API / provider IP 白名單設定會直接影響 connector runtime 是否允許 API request 或 callback 進入。

Task: 維護白名單控制面時，要確保 DB 設定、runtime cache、權限與操作紀錄的邊界清楚，不能只把它當成 CRUD。

Action: 參與 Game API white IP 後台功能與 provider white IP CRUD 初版，處理 duplicate check、DB insert / delete、Game API Redis set 同步、RoleFilter / limited admin gate 與 operation log；並從 latest code 追到 connector runtime filter、provider fanout reload 與 in-memory cache 的 current behavior。

Result: 這條 case 可用來說明 control plane 到 runtime enforcement、DB / cache eventual consistency、刪除白名單的安全 failure window，以及如何保守切開自己 direct evidence 與 team current behavior。

## 可被追問的點

| 追問 | 回答要點 |
| --- | --- |
| 為什麼不是 CRUD? | 因為後台新增 / 刪除會影響 connector runtime 是否放行 API request / provider callback。 |
| DB 和 Redis 不一致怎麼辦? | DB 是 source of truth，Redis 是 runtime view；要有重建 cache、manual repair、operation log 和監控。 |
| DB 成功但 fanout 失敗怎麼辦? | provider cache 可能 stale；目前 code 是 best-effort publish，不是 outbox，需補 retry / reload / metric。 |
| 刪除 IP cache stale 為什麼更危險? | 刪除後 runtime 若仍放行舊 IP，是 access-control 風險；新增後 cache stale 多半只是新來源暫時進不來。 |
| duplicate 怎麼處理? | Game API 是 `agent_id + ip`，provider latest 是 `provider + ip`；但未驗證 DB unique，不能說完全防 race。 |
| provider 為什麼改全站共用? | 這是 latest current behavior，可說 scope decision，但 `arnold` 改動不能當 Nick direct evidence。 |
| 這條能放履歷嗎? | Step 4 先不直接放；Step 5 或 contribution refresh 後可作後台 control plane / DB Redis 同步 supporting evidence。 |

## Lead / Architect 追問

| 追問 | 回答方向 |
| --- | --- |
| 如果要升級這套白名單控制面? | 用 DB 作 source of truth，補 cache rebuild / manual reload / reload metric；更高可靠可用 outbox 或 retry worker 發 reload event。 |
| cache reload 要 fail-open 還是 fail-closed? | access-control 預設偏 fail-closed，但 provider callback 有 SLA，要設計 emergency rollback / temporary allowlist 流程。 |
| fanout anonymous queue 有什麼限制? | 線上節點會收到 reload，但離線節點可能錯過；需要 startup reload、定期 reload 或 health check 補齊。 |
| 如何避免誤操作? | 操作權限、limited admin、provider allowlist、IP 格式校驗、operation log，必要時加審核或二次確認。 |
| 如何驗證設定生效? | 檢查 DB、Redis / connector cache、runtime 放行 / 拒絕結果、reload log、operation log 與 last refresh time。 |

## 反問面試官

- 你們後台設定變更會如何同步到 runtime 節點?
- 安全類設定的刪除操作，有沒有更高等級的告警或審核?
- runtime cache reload 失敗時，會 fail-open、fail-closed，還是有人工 emergency 流程?
- 白名單 / 黑名單這類 access-control 設定，有沒有 audit log、last refresh metric 或重建工具?

## 誇大陷阱

- 面試官問「你設計 connector reload 嗎?」
  保守答: 我有 code-backed 理解 latest reload path，但 fanout reload 是主管後續改動，我可以分析它的 failure window，不把它說成我主導。

- 面試官問「這是否強一致?」
  保守答: 不是。DB + Redis / MQ fanout 不是單一交易，這是 eventual consistency，需要補 reload / repair / monitoring。

- 面試官問「這是不是完整資安系統?」
  保守答: 不是完整資安平台，是 runtime access-control 的白名單控制面 case。

## Case-specific 基本功

- Redis set 適合做 membership check，但不能取代 DB source of truth。
- DB + Redis / MQ 不能同一個 local transaction 原子提交；需要接受 eventual consistency 或引入 outbox / retry。
- access-control 的 stale delete 通常比 stale add 危險。
- application duplicate check 不等於 DB unique constraint。
- fanout 適合廣播 reload，但要搭配 startup reload / manual reload / observability。

## Step 5 結論

本 flow 已完成 Step 5，正式面試 case 與 claim gate 都已收斂。面試口徑維持「後台 control plane / runtime access-control / DB cache consistency」，履歷口徑維持 project-level supporting evidence，不單獨誇大成完整 access-control platform。
