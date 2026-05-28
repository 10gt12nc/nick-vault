# game-api-whitelist-sync Career Interview

日期: 2026-05-28

## 定位

這條 flow 是 AntPlay 後台 control plane / Game API runtime access-control 的正式面試素材。完成狀態是 Step 4；證據層級是 `真實開發過 + code-backed`，但目前只到單條 flow 面試 case，不直接更新 `05 / 08`。

## 30 秒說法

我參與過 AntPlay Game API 白名單控制面。後台 admin-api 提供新增、刪除、查詢白名單，資料寫入 `game_api_login_ip_white`，同時更新 Redis set `antplay:GameApiWhiteIp:{agentId}`。game-api runtime 的 `WhiteIpFilter` 會讀 Redis 判斷來源 IP 是否可呼叫 login / public API。這個 case 的重點是 DB / Redis 雙寫一致性、角色權限、操作審計與 runtime filter 邊界。

## 90 秒說法

這個 case 我會定位成後台設定影響 runtime access-control。admin-api 端有 `/game_api_white_ip/add`、`getList`、`delete` 和特定 merchant 的 sync API。新增時先檢查同 agent / IP 是否重複，再寫 DB，接著把 IP 加到 Redis set；刪除時會先拿 ipId 查出 IP，再刪 DB 與 Redis set。查詢端會依登入者 role 和 agent scope 篩選可看資料。

下游 game-api 的 `WhiteIpFilter` 會從 request query 或 body 取 `agentId`，取得 client IP，再用 Redis key `antplay:GameApiWhiteIp:{agentId}` 檢查是否 member，不在白名單就 reject。

我會主動說這不是強一致設計。DB 是設定保存來源，Redis 是 runtime 判斷來源；如果 DB 成功但 Redis 失敗，後台與 runtime 會不一致。正式 owner 會補 DB unique key、Redis rebuild / reconciliation、sync 部分成功記錄、reject metrics 和操作告警。

## 3 分鐘說法

這個 case 的背景是 Game API 會暴露給商戶或代理整合，不能讓任意來源 IP 都能呼叫 login 或 public API。所以後台需要一個白名單控制面，讓營運或管理者能針對 agent 維護允許來源 IP。

實作上，admin-api 的 `GameApiWhiteIpController` 提供新增、查詢、刪除和特定 merchant sync。新增時會先經過 `RoleFilter`，並確認 limited admin，接著檢查同一個 agent / IP 是否重複；不重複才寫入 `game_api_login_ip_white`，再把 IP 加到 Redis set `antplay:GameApiWhiteIp:{agentId}`。刪除則是先用 `ipId` 找 IP，刪 DB 後再從 Redis set remove。查詢會透過登入者 role 和 agent scope 控制可查範圍，新增 / 刪除也會寫 operation log。

下游 game-api 的 `WhiteIpFilter` 是 runtime enforcement。它會從 GET query 或 POST body 取出 `agentId`，再用 request source IP 去 Redis set 檢查是不是 member；不在白名單就回 `NOT_IN_API_WHITE_LIST`。所以這條 flow 是後台 control plane 影響 runtime access-control，不是單純 CRUD。

我會主動講清楚它的風險邊界。DB 是管理與查詢的 source，Redis 是 runtime 判斷的 source，兩者不是天然強一致。新增時如果 DB 成功但 Redis 失敗，後台看得到資料但 runtime 還是拒絕；刪除時如果 DB 成功但 Redis 失敗，runtime 可能繼續放行舊 IP。sync 更明顯，因為 Redis 操作不會跟 DB transaction rollback，而且是逐 agent batch，可能部分成功。

如果我是 owner，我會補三件事：第一，DB 對 `(agent_id, ip)` 做 unique key，避免 query-then-insert race；第二，提供從 DB rebuild Redis 的 reconciliation / 手動修復工具；第三，補 runtime reject count、Redis update failure、sync 成功 / 失敗 agent 清單和告警。這樣才能把白名單從可用功能提升成 production-grade access-control support。

## STAR

- Situation: Game API 對外提供 login / public API，必須限制商戶或代理的來源 IP，避免非授權來源呼叫。
- Task: 建立後台白名單控制面，讓 DB 設定可以同步到 Redis，供 game-api runtime filter 即時判斷 allow / reject。
- Action: 參與 admin-api 白名單新增 / 刪除 / 查詢與 Redis key 同步，並理解 game-api `WhiteIpFilter` 讀取同一 Redis set 的 runtime 判斷邊界。
- Result: 後台可以維護 Game API 來源 IP 白名單，runtime 可以用 Redis 快速判斷 request 是否放行；同時保留 DB / Redis 雙寫、batch sync、operation log 與 observability 的改善邊界。

## 可面試講的能力點

- 後台 control plane 如何影響 runtime filter。
- DB persistent config 與 Redis runtime cache 的角色差異。
- `RoleFilter`、limited admin、agent scope 對設定 API 的保護。
- operation log 對白名單變更審計的重要性。
- DB / Redis 雙寫 failure window。
- batch sync 的部分成功與 hard-coded merchant / prefix 風險。

## Senior 追問回答

| 追問 | 回答方向 |
| --- | --- |
| DB 和 Redis 哪個是 source of truth? | DB 是管理 / 查詢來源；Redis 是 runtime allow / reject 來源。兩者不一致時，runtime 以 Redis 為準，所以要補 rebuild / reconcile。 |
| 這是不是強一致? | 不是。DB transaction 不會 rollback Redis。正確說法是 eventual consistency + 補償 / reconciliation。 |
| query-then-insert 能防重嗎? | 只能擋一般重複操作；併發新增仍建議 DB unique key `(agent_id, ip)` 或 upsert。 |
| 刪除 DB 成功但 Redis remove 失敗怎麼辦? | runtime 可能仍放行舊 IP。需要 retry、告警、手動 rebuild 或定期 diff check。 |
| sync API 的風險? | hard-coded merchant / prefix、逐 agent batch、Redis 不受 DB transaction 控制，可能部分 agent 成功、部分失敗。 |
| agentId 取不到會怎樣? | runtime filter 可能組出錯誤 Redis key 或誤判；要有 request validation、明確錯誤與 metrics。 |
| 這能不能講 security? | 可以講 access-control support / IP whitelist control plane；不能包裝成完整 security platform、WAF、IAM 或 API gateway owner。 |

## Lead / Architect 追問

### Q1: 為什麼 runtime 不直接查 DB?

白名單檢查在 login / public API 前面，屬於高頻 request filter。每次查 DB 會讓 API latency 和 DB availability 直接影響入口，所以用 Redis set 當 runtime cache 是合理取捨。代價是 DB / Redis 需要補一致性治理。

### Q2: 你會怎麼設計補償?

我會保留 DB 作設定 source，提供 rebuild Redis by agent / all agents 的工具；新增 / 刪除 Redis 失敗時記錄 error 並告警；定期 diff DB 與 Redis；sync 回傳每個 agent 的成功 / 失敗狀態。若是高安全場景，刪除失敗要比新增失敗更高優先級處理。

### Q3: 如果白名單誤擋造成商戶不能登入，你怎麼排查?

先確認 request 的 `agentId` 和來源 IP 是否正確，再查 DB 是否有白名單、Redis set 是否有該 IP、admin operation log 是否剛被改過、filter 是否有 reject log，最後看是否是 body wrapper / filter order / 環境開關造成取值錯誤。

### Q4: 你會怎麼定 observability?

我會看 white-list reject count、Redis operation failure、sync job duration、sync affected agent count、DB / Redis diff count、agentId missing count。這些比單純看 API error 更能定位是設定問題還是 runtime filter 問題。

## 可安全使用的履歷 bullet 草稿

- 參與 AntPlay Game API 白名單控制面與 runtime Redis 同步流程，處理後台白名單新增 / 刪除 / 查詢、DB / Redis 狀態同步與 game-api runtime filter 對接，支援商戶 API 來源 IP 管控。

注意: 這只是 flow-level 草稿。正式 `05 / 08` 要等 Step 5 或 project-level contribution refresh 決定是否回填。

## 不可誇大

- 不說完整 security platform owner。
- 不說完整 API gateway / WAF / IAM owner。
- 不說 DB / Redis 強一致已完整落地。
- 不說完整 Game API runtime owner。
- 不說完整 production incident owner 或量化改善。

## 常見陷阱

- 把它講成完整 security platform 或 WAF。
- 只講 CRUD，不講 runtime filter 與 Redis。
- 宣稱 DB / Redis 強一致，但沒有 evidence。
- 忽略刪除失敗的安全風險。
- 把 hard-coded sync 包裝成通用白名單平台。
- 沒有 evidence 卻說已經有 metrics、reconciliation 或自動修復。

## 反問面試官

- 你們的 control plane 設定會怎麼同步到 runtime cache?
- DB / Redis 不一致時，你們是用 rebuild、reconcile 還是事件補償?
- 入口白名單 / API gateway 類規則會怎麼做 audit log 和 rollback?
- 對商戶 API 被誤擋或誤放行，你們會看哪些 metrics?

## Step 4 結論

這條 flow 已轉成正式面試 case。它比純後台 CRUD 更有價值，因為能講「control plane 設定如何影響 runtime allow / reject」，也能延伸到 Redis cache consistency、operation audit 與 access-control failure window。

下一步應做 Step 5，完成單條 flow claim gate，判斷是否回填 project-level supporting evidence，仍不直接用單條 flow 改 `05 / 08`。

```text
antplay antplay-slot-admin-api game-api-whitelist-sync Step 5
```
