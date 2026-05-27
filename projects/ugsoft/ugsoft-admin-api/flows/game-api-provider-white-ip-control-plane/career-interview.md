# game-api-provider-white-ip-control-plane Career Interview

日期: 2026-05-27

## 定位

- Flow: `game-api-provider-white-ip-control-plane`
- Step: Step 5
- 證據層級: `真實開發過 + code-backed` 混合下游 `code-backed context`。
- 是否直接更新 05 / 08: 否。本輪是單條 flow Step 5 claim gate；後續 project contribution refresh 已完成，正式履歷輸出版仍要等 rolling resume package。

## 30 秒版

我在 UGSoft 後台 API 參與過 Game API / provider IP 白名單控制面。這條 flow 的重點不是一般 CRUD，而是後台設定會影響 runtime access-control: Game API 白名單寫 DB 後同步 Redis set，connector runtime filter 依 agentId 和來源 IP 判斷是否放行；provider 白名單 latest behavior 則是後台寫 DB 後用 fanout 通知 connector reload provider IP cache。這裡我會特別看 DB / Redis / cache reload 不一致、刪除 IP 的安全風險、權限範圍與操作紀錄。

## 90 秒版

UGSoft 有一類後台控制面是白名單設定。Game API 白名單是以 agentId 維度維護，後台新增時會先查 `game_api_login_ip_white` 是否已有同 agent + IP，再寫 DB，並同步加到 Redis set。connector-api 的 `WhiteIpFilter` 在 login / public / connect client 類路徑會用 agentId + source IP 查 Redis set，決定是否放行。

Provider 白名單則是另一個 runtime callback 入口。Nick direct evidence 是 provider white IP 後台表和 CRUD 初版；最新 code 由主管後續改成全站 provider 維度，新增 / 刪除後 publish fanout，connector reload in-memory provider IP cache，callback endpoint 再檢查 provider + source IP。

我會把這條 case 拿來講 control plane 的風險: DB 是 source of truth，但 Redis 或 in-memory cache 是 runtime view；DB 成功但 Redis / fanout 失敗時，後台看到的狀態和 runtime 放行狀態可能不同。新增 IP cache stale 多半是新來源暫時被拒，刪除 IP cache stale 則可能繼續放行已撤銷來源，所以要有重建 cache、操作紀錄、權限 gate、reload 監控或人工 repair 流程，不能只把它當 CRUD。

## 3 分鐘版

這條 case 是 UGSoft 後台 API 的 Game API / provider IP 白名單控制面。我會把它定位成 control plane 影響 runtime access-control，而不是一般 CRUD。

Game API 白名單的流程是，後台 `/game_api_white_ip/add` 收到 agentId、IP 和備註後，先檢查 limited admin 權限，再查 `game_api_login_ip_white` 是否已有同 agent + IP。不存在就寫 DB，接著同步把 IP 加進 Redis set，key 是 `ugSoft:GameApiWhiteIp:{agentId}`。刪除時會先查 IP，再刪 DB，最後從 Redis set 移除，成功時寫 admin operation log。下游 connector-api 的 `WhiteIpFilter` 會在 login / public / connect client 等 runtime path 根據 request 裡的 agentId 和來源 IP 查 Redis set，決定是否放行。

Provider 白名單是 provider callback 的另一個入口控制。Nick 的 direct evidence 是 provider white IP 表與後台 CRUD 初版；最新 `origin/main` 由主管後續改成 provider 全站共用，並在 add / delete 後 publish fanout message，connector-api 收到後 reload provider -> IP 的 in-memory cache，callback endpoint 再用 provider + source IP 檢查。

這裡的 owner 判斷有幾個。第一，DB 是設定 source of truth，但 runtime 看的其實是 Redis 或 in-memory cache，所以 DB 與 cache 不一致一定要能被發現與修復。第二，DB + Redis 或 DB + RabbitMQ fanout 不是同一個 transaction，DB 成功但 cache 更新失敗時，可能導致新增 IP 仍被拒絕，或刪除 IP 仍被放行。第三，刪除 IP 後 cache stale 比新增 IP 後 cache stale 更危險，因為它可能讓已撤銷的來源繼續通過。第四，provider 白名單從 agent-scoped 改成 global-scoped 是 access-control scope 決策，不能混著講。第五，這種後台操作要有 RoleFilter、limited admin gate 和 operation log，避免一般帳號誤改 runtime access。

這條我不會誇大成完整 access-control platform owner，而是保守說我參與過後台白名單控制面與 DB / Redis 同步維護，並能說清楚它如何影響 runtime access control、cache freshness 與 failure window。

## STAR 版

Situation: UGSoft 後台有 Game API / provider IP 白名單設定，這些設定會直接影響 connector runtime 是否允許外部 API request 或 provider callback 進來。

Task: 維護白名單控制面時，不能只完成新增 / 刪除 CRUD，還要確認 DB、Redis / cache、權限與操作紀錄之間的邊界，避免後台顯示與 runtime 放行狀態不一致。

Action: 參與 Game API white IP 後台功能與 provider white IP CRUD 初版，處理 `agentId + ip` / `provider + ip` 的 duplicate check、DB insert / delete、Game API Redis set 同步、RoleFilter / limited admin gate 與 operation log；並能從 latest code 追到 connector runtime filter、provider fanout reload 與 in-memory cache 的 current behavior。

Result: 這條 flow 可作為後台 control plane 影響 runtime access-control 的面試 case。它能延伸討論 DB source of truth、Redis / in-memory cache runtime view、DB + cache 非原子一致性、fanout reload failure、權限與 audit；履歷上仍採保守「參與後台白名單控制面與 DB / Redis 同步維護」口徑。

## 可說

- 參與 UGSoft 後台 Game API / provider IP 白名單控制面開發維護。
- 處理後台新增 / 查詢 / 刪除、DB duplicate check、Redis set 同步、權限範圍與操作紀錄。
- 能說明 control plane 設定如何影響 connector runtime access-control。
- 能分析 DB / Redis / fanout cache reload 的 eventual consistency 與 partial failure。

## 不可說

- 不說主導完整 access-control platform。
- 不說完整 connector reload owner。
- 不說 provider fanout reload 是 Nick direct evidence。
- 不說 DB + Redis / RabbitMQ fanout 是強一致或 exactly-once。
- 不說這條是 money correctness / wallet / ledger flow。

## Senior 追問回答

| 追問 | 回答 |
| --- | --- |
| 為什麼這不是 CRUD? | 因為新增 / 刪除白名單會改變 connector runtime 是否放行 API request 或 provider callback。後台 DB 只是設定面，runtime 真正看的可能是 Redis 或 in-memory cache。 |
| DB 和 Redis 不一致怎麼辦? | DB 是 source of truth，Redis 是 runtime view；DB 成功但 Redis 更新失敗時，要靠 cache rebuild、manual repair、操作紀錄、監控或重試補回。不能說它是強一致。 |
| 刪除 IP 和新增 IP 哪個 failure 更危險? | 刪除 IP 後 cache stale 更危險，因為 runtime 可能繼續放行已被後台刪除的來源；新增 IP cache stale 多半是新來源暫時被拒。 |
| Provider fanout publish 失敗怎麼辦? | latest code 是 DB 更新後 best-effort publish；publish 失敗只 log，不 rollback DB，所以 connector cache 可能 stale。要補 reload metric、manual reload、retry 或 outbox 才能更穩。 |
| provider 為什麼從 agent-scoped 改 global-scoped? | 這是 latest current behavior 的 access-control scope decision，管理較簡化，但放行範圍變大；而且這段是主管後續改動，我可以分析，不會說成我主導。 |
| duplicate check 夠嗎? | application 先查再 insert 能擋一般重複，但若 DB 沒 unique constraint，併發下仍可能 race；本輪未驗證 DDL，所以不宣稱完全防重。 |
| 這條能放履歷嗎? | Step 4 先作正式面試 case；Step 5 或 project contribution refresh 後，才判斷是否回填成 project-level supporting evidence。 |

## Lead / Architect 追問

| 追問 | 回答方向 |
| --- | --- |
| 如果要把白名單控制面設計得更穩，你會怎麼做? | DB 作 source of truth；cache refresh 可用 outbox / retry worker / manual rebuild；刪除類操作要有更高優先級告警；runtime 節點要暴露 last refresh time。 |
| Redis / in-memory cache 要 fail-open 還是 fail-closed? | 白名單屬 access-control，預設應偏 fail-closed；但要評估 provider callback SLA，並準備 emergency bypass / rollback 流程。 |
| fanout anonymous queue 有什麼風險? | 節點在線時能收到 reload；節點離線期間錯過 message 需靠 startup reload 或定期 reload 補齊，不能只靠 fanout。 |
| 你怎麼驗證後台設定真的生效? | 除了 DB 查詢，也要驗證 Redis key / connector cache、runtime request 被放行或拒絕、operation log、reload log / metric。 |
| 怎麼避免誤操作? | 嚴格 RoleFilter / limited admin、provider allowlist、IP 格式校驗、操作紀錄，必要時加審核或二次確認。 |

## 反問面試官

- 你們的後台設定如果會影響 runtime access-control，DB 和 cache 之間怎麼同步?
- 安全類設定的刪除操作，是否有更嚴格的審核、告警或 rollback?
- runtime 節點 cache reload 失敗時，有沒有 last refresh time、metric、manual reload 或自動修復?
- IP 白名單這類控制面，你們偏向 fail-open 還是 fail-closed? 不同 provider / merchant 會不會有例外?

## Step 5 Claim Gate

本 flow 可作 `ugsoft-admin-api` project-level 後台 control plane / runtime access-control supporting evidence。它可以支撐「參與後台白名單控制面、DB / Redis 同步、權限與操作紀錄」這種保守履歷口徑。

不建議單獨寫成獨立履歷主 bullet，因為它比較適合作為 `ugsoft-admin-api` 後台 API / control plane 經驗的一部分，和 BetRecord MQ、RequestLog MQ 一起支撐 project-level claim。

### 可放履歷的保守素材

- 參與 UGSoft 後台 Game API / provider IP 白名單控制面開發維護，處理後台 CRUD、DB / Redis 同步、權限範圍與操作紀錄。

### 可面試講

- 後台 control plane 設定如何影響 connector runtime access-control。
- DB source of truth 與 Redis / in-memory cache runtime view 的一致性邊界。
- DB + Redis / fanout 無法原子提交時的 partial failure。
- provider white IP agent-scoped vs global-scoped 的 scope decision。
- Nick direct evidence 與 `arnold` latest behavior 的界線。

### 不可誇大

- 不說主導完整 access-control platform。
- 不說完整 connector reload owner。
- 不把 provider fanout reload / global scope refactor 寫成 Nick direct evidence。
- 不說強一致、exactly-once、完整資安平台或完整 provider gateway owner。

## Step 5 結論

Step 5 已完成。本 flow 已有 30 秒、90 秒、3 分鐘、STAR、Senior / Lead 追問、反問、誇大陷阱與單條 flow claim gate。後續 `ugsoft-admin-api` project-level contribution claim consolidation refresh 也已完成，已回填成 project-level supporting evidence。
