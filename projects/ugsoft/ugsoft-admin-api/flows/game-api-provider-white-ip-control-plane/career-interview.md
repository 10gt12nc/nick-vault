# game-api-provider-white-ip-control-plane Career Interview

日期: 2026-05-27

## 定位

- Flow: `game-api-provider-white-ip-control-plane`
- Step: Step 3
- 證據層級: `真實開發過 + code-backed` 混合下游 `code-backed context`。
- 是否直接更新 05 / 08: 否。本輪是單條 flow Step 3，不是 Step 5 claim gate，也不是 project contribution refresh。

## 30 秒版

我在 UGSoft 後台 API 參與過 Game API / provider IP 白名單控制面。這條 flow 的重點不是一般 CRUD，而是後台設定會影響 runtime access-control：Game API 白名單寫 DB 後同步 Redis set，connector runtime filter 依 agentId 和來源 IP 判斷是否放行；provider 白名單最新版本則是後台寫 DB 後用 fanout 通知 connector reload provider IP cache。這裡我會特別看 DB / Redis / cache reload 不一致、權限範圍與操作紀錄。

## 90 秒版

UGSoft 有一類後台控制面是白名單設定。Game API 白名單是以 agentId 維度維護，後台新增時會先查 `game_api_login_ip_white` 是否已有同 agent + IP，再寫 DB，並同步加到 Redis set。connector-api 的 `WhiteIpFilter` 在 login / public / connect client 類路徑會用 agentId + source IP 查 Redis set，決定是否放行。

Provider 白名單則是另一個 runtime callback 入口。Nick direct evidence 是 provider white IP 後台表和 CRUD 初版；最新 code 由主管後續改成全站 provider 維度，新增 / 刪除後 publish fanout，connector reload in-memory provider IP cache，callback endpoint 再檢查 provider + source IP。

我會把這條 case 拿來講 control plane 的風險：DB 是 source of truth，但 Redis 或 in-memory cache 是 runtime view；DB 成功但 Redis / fanout 失敗時，後台看到的狀態和 runtime 放行狀態可能不同。這種地方要有重建 cache、操作紀錄、權限 gate、reload 監控或人工 repair 流程，不能只把它當 CRUD。

## 3 分鐘版草稿

這條 case 是 UGSoft 後台 API 的 Game API / provider IP 白名單控制面。我會把它定位成 control plane 影響 runtime access-control，而不是一般 CRUD。

Game API 白名單的流程是，後台 `/game_api_white_ip/add` 收到 agentId、IP 和備註後，先檢查 limited admin 權限，再查 `game_api_login_ip_white` 是否已有同 agent + IP。不存在就寫 DB，接著同步把 IP 加進 Redis set，key 是 `ugSoft:GameApiWhiteIp:{agentId}`。刪除時會先查 IP，再刪 DB，最後從 Redis set 移除，成功時寫 admin operation log。下游 connector-api 的 `WhiteIpFilter` 會在 login / public / connect client 等 runtime path 根據 request 裡的 agentId 和來源 IP 查 Redis set，決定是否放行。

Provider 白名單是 provider callback 的另一個入口控制。Nick 的 direct evidence 是 provider white IP 表與後台 CRUD 初版；最新 `origin/main` 由主管後續改成 provider 全站共用，並在 add / delete 後 publish fanout message，connector-api 收到後 reload provider -> IP 的 in-memory cache，callback endpoint 再用 provider + source IP 檢查。

這裡的 owner 判斷有幾個。第一，DB 是設定 source of truth，但 runtime 看的其實是 Redis 或 in-memory cache，所以 DB 與 cache 不一致一定要能被發現與修復。第二，DB + Redis 或 DB + RabbitMQ fanout 不是同一個 transaction，DB 成功但 cache 更新失敗時，可能導致新增 IP 仍被拒絕，或刪除 IP 仍被放行。第三，provider 白名單從 agent-scoped 改成 global-scoped 是 access-control scope 決策，不能混著講。第四，這種後台操作要有 RoleFilter、limited admin gate 和 operation log，避免一般帳號誤改 runtime access。

這條我不會誇大成完整 access-control platform owner，而是保守說我參與過後台白名單控制面與 DB / Redis 同步維護，並能說清楚它如何影響 runtime access control、cache freshness 與 failure window。

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

## 待 Step 4 補強

- 正式 STAR 版本。
- Senior / Lead 追問。
- 反問面試官。
- 誇大陷阱與答法。
