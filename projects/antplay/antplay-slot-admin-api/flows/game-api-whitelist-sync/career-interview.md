# game-api-whitelist-sync Career Interview

日期: 2026-05-28

## 定位

這條 flow 是 AntPlay 後台 control plane / Game API runtime access-control 的面試素材。完成狀態是 Step 3；證據層級是 `真實開發過 + code-backed`，但目前只到 flow 深掃，不直接更新 `05 / 08`。

## 30 秒說法

我參與過 AntPlay Game API 白名單控制面。後台 admin-api 提供新增、刪除、查詢白名單，資料寫入 `game_api_login_ip_white`，同時更新 Redis set `antplay:GameApiWhiteIp:{agentId}`。game-api runtime 的 `WhiteIpFilter` 會讀 Redis 判斷來源 IP 是否可呼叫 login / public API。這個 case 的重點是 DB / Redis 雙寫一致性、角色權限、操作審計與 runtime filter 邊界。

## 90 秒說法

這個 case 我會定位成後台設定影響 runtime access-control。admin-api 端有 `/game_api_white_ip/add`、`getList`、`delete` 和特定 merchant 的 sync API。新增時先檢查同 agent / IP 是否重複，再寫 DB，接著把 IP 加到 Redis set；刪除時會先拿 ipId 查出 IP，再刪 DB 與 Redis set。查詢端會依登入者 role 和 agent scope 篩選可看資料。

下游 game-api 的 `WhiteIpFilter` 會從 request query 或 body 取 `agentId`，取得 client IP，再用 Redis key `antplay:GameApiWhiteIp:{agentId}` 檢查是否 member，不在白名單就 reject。

我會主動說這不是強一致設計。DB 是設定保存來源，Redis 是 runtime 判斷來源；如果 DB 成功但 Redis 失敗，後台與 runtime 會不一致。正式 owner 會補 DB unique key、Redis rebuild / reconciliation、sync 部分成功記錄、reject metrics 和操作告警。

## 可面試講的能力點

- 後台 control plane 如何影響 runtime filter。
- DB persistent config 與 Redis runtime cache 的角色差異。
- `RoleFilter`、limited admin、agent scope 對設定 API 的保護。
- operation log 對白名單變更審計的重要性。
- DB / Redis 雙寫 failure window。
- batch sync 的部分成功與 hard-coded merchant / prefix 風險。

## 可安全使用的履歷 bullet 草稿

- 參與 AntPlay Game API 白名單控制面與 runtime Redis 同步流程，處理後台白名單新增 / 刪除 / 查詢、DB / Redis 狀態同步與 game-api runtime filter 對接，支援商戶 API 來源 IP 管控。

注意: 這只是 flow-level 草稿。正式 `05 / 08` 要等 Step 5 或 project-level contribution refresh 決定是否回填。

## 不可誇大

- 不說完整 security platform owner。
- 不說完整 API gateway / WAF / IAM owner。
- 不說 DB / Redis 強一致已完整落地。
- 不說完整 Game API runtime owner。
- 不說完整 production incident owner 或量化改善。

## Step 3 結論

這條 flow 已建立可讀主報告與 evidence。它比純後台 CRUD 更有價值，因為能講「control plane 設定如何影響 runtime allow / reject」，也能延伸到 Redis cache consistency、operation audit 與 access-control failure window。

下一步應做 Step 4，把它轉成正式面試 case。

```text
antplay antplay-slot-admin-api game-api-whitelist-sync Step 4
```
