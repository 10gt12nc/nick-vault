# game-api-whitelist-sync Interview Notes

日期: 2026-05-28

## Step 4 定位

本檔是 Step 4 正式面試素材。素材只使用 Step 3 已確認的 code / commit evidence，不新增未驗證 claim。這條 flow 的定位是 `admin control plane / Redis runtime cache / Game API IP whitelist`，不是完整 security platform。

## 30 秒摘要

這條 flow 是 AntPlay Game API 白名單控制面。admin-api 負責新增、查詢、刪除白名單，資料寫入 `game_api_login_ip_white`，同時更新 Redis set `antplay:GameApiWhiteIp:{agentId}`。game-api 的 `WhiteIpFilter` 讀 Redis 判斷來源 IP 是否允許呼叫 login / public API。面試重點是後台 control plane、DB / Redis 雙寫一致性、runtime allow / reject、operation log 與 access-control 邊界。

## 90 秒版本

這個 case 的價值在於它不是純 CRUD，而是後台設定會影響 runtime access-control。admin-api 的 `GameApiWhiteIpController` 提供新增、查詢、刪除和特定 merchant sync。新增時先做 role / limited admin 檢查，再查同 agent / IP 是否重複，不重複才 insert DB，接著寫 Redis set。刪除時先用 `ipId` 查出 IP，刪 DB 後從 Redis remove。查詢則會依登入者 role 和 agent scope 限制可見資料。

下游 game-api 的 `WhiteIpFilter` 會從 request query 或 body 取 `agentId`，再拿 client IP 去 Redis set 檢查。如果不是 member，就回 `NOT_IN_API_WHITE_LIST`。所以這條 flow 可以講清楚 DB 是設定保存來源，Redis 是 runtime 判斷來源。

我會保守講它不是強一致設計。新增 / 刪除都是 DB 和 Redis 雙寫；sync 裡 Redis 操作不會跟 DB transaction rollback。正式 owner 要補 DB unique key、Redis rebuild / reconciliation、sync 部分成功記錄、reject metrics 和 Redis operation failure alert。

## 3 分鐘版本

這條 flow 的背景是 Game API 對商戶或代理開放，入口不能讓任意來源 IP 都能呼叫。後台需要提供白名單控制面，讓管理者針對 agent 維護允許來源 IP，runtime 再根據這份白名單判斷 request 是否放行。

admin-api 端的主入口是 `GameApiWhiteIpController`。新增白名單時，API 會先經過 `RoleFilter`，限制 ADMIN、SUPER_AGENT、LEVEL_ONE_AGENT，並確認 limited admin。接著檢查 `ip` 不能空，並用 `queryExistIp(agentId, ip)` 確認同 agent 下沒有重複 IP。通過後寫入 DB table `game_api_login_ip_white`，再把 IP 加進 Redis set `antplay:GameApiWhiteIp:{agentId}`，最後寫 admin operation log。

刪除時會先用 `ipId` 查出 IP，再刪 DB，並從 Redis set remove。查詢則會從 `SecurityContextHolder` 取得登入者資訊，用 `AgentRoleUtil#processAgentIds` 決定可查範圍，避免不同 role 查到不該看的 agent 白名單。

runtime 端在 `antplay-slot-game-api` 的 `WhiteIpFilter`。它會判斷 login / public API 這類受限路徑，從 GET query 或 POST body 取得 `agentId`，再取得 client IP，最後用 Redis `SISMEMBER` 檢查 `antplay:GameApiWhiteIp:{agentId}` 是否包含這個 IP。不包含時就 reject。

我會主動講這條 flow 的取捨。用 Redis 當 runtime cache 是合理的，因為入口 filter 不適合每次查 DB；但代價是 DB / Redis 雙寫一致性。DB 是後台管理與查詢來源，Redis 是 runtime 判斷來源。新增 DB 成功但 Redis 失敗會讓合法商戶仍被拒絕；刪除 DB 成功但 Redis 失敗會讓舊 IP 可能繼續被放行。sync 還有 batch 部分成功和 hard-coded merchant / prefix 的維運風險。

如果我是 owner，我會補 `(agent_id, ip)` unique key 或 upsert、防止 query-then-insert race；補從 DB rebuild Redis 的工具；補 DB / Redis diff check；新增 / 刪除 / sync 都要有明確成功失敗結果和告警；runtime filter 要有 reject count、agentId missing count、Redis operation failure metrics。這樣才像 production-grade access-control support。

這條 flow 的面試主軸不是「我做了一個 CRUD」，而是:

```text
後台 control plane 如何透過 DB + Redis 影響 game-api runtime access-control。
```

## STAR 版本

- Situation: Game API 對商戶 / 代理提供 login 與 public API，需要限制來源 IP，降低未授權來源呼叫風險。
- Task: 建立後台白名單控制面，讓 admin-api 管理 DB 設定並同步 Redis，供 game-api runtime filter 做快速 allow / reject。
- Action: 參與白名單新增 / 刪除 / 查詢、Redis key 同步、operation log 與 runtime `WhiteIpFilter` 對接邊界的開發維護；並能分析 DB / Redis 雙寫與 batch sync failure window。
- Result: 後台可維護 Game API 來源 IP 白名單，runtime 可用 Redis 快速檢查 request source；同時保留一致性、reconciliation、observability 的改善邊界。

## 常見追問

| 追問 | 回答方向 |
| --- | --- |
| DB 和 Redis 哪個是 source of truth? | DB 是設定保存與後台查詢來源；Redis 是 runtime filter 實際判斷來源。 |
| DB 成功、Redis 失敗怎麼辦? | 現有 flow 有 failure window；正式 owner 會補 retry、rebuild cache、reconciliation 與告警。 |
| query-then-insert 能防重嗎? | 只能擋一般重複操作；併發下仍建議 DB unique key `(agent_id, ip)`。 |
| sync 為什麼危險? | 逐 agent batch，Redis 不跟 DB transaction rollback，可能部分 agent 成功、部分失敗。 |
| 這算 security platform 嗎? | 不能誇大。這是 Game API IP whitelist control plane / runtime filter support，不是完整 security platform。 |

## Senior 追問

### Q1: DB 和 Redis 哪個才是 source of truth?

DB 是設定保存與後台查詢來源，Redis 是 runtime 判斷來源。使用者真正被 allow / reject 是看 Redis，所以只寫 DB 不代表 runtime 生效。這也是這條 flow 的一致性風險。

### Q2: 這是不是強一致?

不是。新增 / 刪除都是 DB 和 Redis 雙寫；`WhitelistSync` 雖然有 DB transaction，但 Redis 不會跟著 rollback。正確做法是承認 eventual consistency，並補 retry、rebuild cache、DB / Redis diff check 和告警。

### Q3: 如果新增白名單成功，但商戶還是被擋，你怎麼排查?

我會先確認 request 的 agentId 和來源 IP，再查 DB 是否有該 row、Redis set 是否有該 IP、operation log 是否真的成功、game-api filter 是否讀到同一個 Redis key，最後看 body wrapper / filter order / 環境開關是否影響 agentId extraction。

### Q4: 刪除白名單時最危險的 failure 是什麼?

DB delete 成功但 Redis remove 失敗。後台看起來已刪，但 runtime 可能仍放行舊 IP。這類 failure 的安全風險高於新增失敗，應該有 retry / alert / reconcile。

### Q5: query-then-insert 有什麼問題?

它可以擋一般重複提交，但如果兩個 request 同時新增同 agent / IP，query 和 insert 之間可能 race。正式設計要用 DB unique key `(agent_id, ip)` 或 upsert 保底。

### Q6: sync API 的 production 風險?

它是逐 agent batch，且目前看到 hard-coded merchant / prefix。Redis update 和 DB update 的順序也不完全一致，失敗可能造成部分 agent 已同步、部分沒同步。正式設計應回傳影響 agent、成功 / 失敗清單，並提供重跑或 rollback 策略。

## Lead / Architect 追問

### Q1: 為什麼不用 DB 每次查白名單?

因為這是入口 filter，高頻且在 request 前段。每次查 DB 會把 DB latency 和 availability 放到所有登入 / public API request 上。用 Redis set 查 membership 比較適合，但要補 cache consistency。

### Q2: 你會怎麼設計 rebuild?

以 DB `game_api_login_ip_white` 為設定來源，提供 per-agent 和 all-agent rebuild，把 Redis key 刪掉後重建 set。rebuild 要有操作權限、operation log、影響 agent 數、成功 / 失敗清單，並避免在高峰期一次重建過多造成 Redis 壓力。

### Q3: 這條 flow 怎麼做 observability?

我會看新增 / 刪除成功率、Redis operation failure、runtime reject count by agentId / IP、agentId missing count、sync duration、sync failed agent count、DB / Redis diff count。這些能定位是後台設定錯、Redis 同步錯，還是 runtime filter 取值錯。

### Q4: 你會怎麼避免敏感資料風險?

白名單本身是安全設定，operation log 要記誰改了哪個 agent / IP，但正式輸出或告警要避免暴露不必要的內部資訊。面試時我只會講「來源 IP 管控」與 audit，不會貼 production IP 或內網細節。

## 回答框架

```text
定位：admin control plane -> DB + Redis -> game-api runtime filter。
流程：add/delete/query/sync -> DB game_api_login_ip_white -> Redis GAME_API_WHITE_IP -> WhiteIpFilter allow/reject。
取捨：Redis 快速判斷，但 DB / Redis 不是強一致。
風險：雙寫失敗、刪除 Redis 失敗、duplicate race、sync 部分成功、agentId 取值錯。
改善：unique key / upsert、cache rebuild、DB/Redis reconcile、reject metrics、Redis failure alert、operation audit。
邊界：不是完整 security platform / API gateway / WAF / IAM owner。
```

## 常見陷阱

- 只講 CRUD，不講 runtime filter。
- 宣稱 DB / Redis 強一致。
- 把白名單說成完整 security platform。
- 忽略刪除 Redis 失敗造成的放行風險。
- 把 hard-coded sync 說成通用平台。
- 沒有 evidence 卻說已有 metrics、rebuild 或 reconciliation。

## 反問面試官

- 你們的後台設定如何同步到 runtime cache?
- cache 和 DB 不一致時，你們有 rebuild / reconciliation 嗎?
- 白名單或 gateway rule 的變更會有 operation audit 和 rollback 嗎?
- runtime reject 的 observability 會看哪些指標?
