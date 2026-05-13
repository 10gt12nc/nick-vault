# iwin app_bi Step 2：候選 Flow 技術點與風險比較

## 範圍

本文件承接 `README.md` 的 Step 1 候選 flow 盤點。

規則：

- 不深挖單一 flow。
- 不建立 `flows/{flow-name}/`。
- 不更新履歷與自傳。
- 不把 `app_bi` 包裝成 Nick 主開發成果。
- 只比較 Senior / Platform Backend / System Owner 價值。

## Flow Ranking

### 1. `point-control-admin-operation`

中文名稱：單點控制 / 營運控制 flow

推薦程度：最高，建議第一條 Step 3。

理由：

- 後台操作會直接影響玩家 runtime 行為，不只是查詢或報表。
- 已確認有 route、controller、DB、Redis、GM command、Mongo operation log。
- 具備明確 state transition：新增 / 編輯控制、啟用 / 取消控制、完成狀態、剩餘額度。
- failure window 清楚：DB、Redis、GM command、Mongo log 任一環節失敗都可能造成狀態不一致。
- 最適合練 Senior / Owner 的「後台控制面如何安全影響 production runtime」。

核心風險：

- DB 寫入成功但 Redis 或 GM command 失敗。
- Redis 更新成功但 runtime 沒收到 GM command。
- GM command 成功但 Mongo operation log 失敗，造成稽核不完整。
- 批次新增 / 編輯時部分資料成功、部分失敗。
- 後台人員誤操作造成玩家控制狀態錯誤。

只能算推測：

- GM command 具體送到哪個後端服務。
- runtime consumer 如何讀 Redis control hash。
- 這條 flow 是否為 Nick 實際參與過。

下一步 evidence：

- `sendGmCommand()` 定義與通訊方式。
- `DbConfig::getGameServerRedis()` 實際用途。
- game server / GM command 接收端 repo。
- Nick 相關 MR / ticket / commit / 本人確認。

### 2. `admin-config-redis-sync`

中文名稱：後台設定同步到 Redis

推薦程度：高，適合第二條或與 point-control 串成 control plane 主題。

理由：

- 這是典型 control plane -> Redis projection -> runtime consumer flow。
- 已確認 `RedisSynchronize.php` 集中處理支付、商戶、VIP、活動、遊戲列表、渠道等設定同步。
- 適合討論 cache consistency、設定發布、回滾、操作 log、同步失敗與 runtime 設定不一致。
- 比純報表更接近 Platform Backend / System Owner。

核心風險：

- DB 設定已改，但 Redis 未同步。
- Redis 同步成功，但 runtime service 尚未讀到或讀到舊設定。
- 多 channel 設定同步時部分 channel 成功、部分失敗。
- 缺少版本號、審核、回滾或同步結果追蹤時，production 問題難定位。

只能算推測：

- 每個 Redis key 的實際 consumer。
- 是否有 wait sync / retry / 補償機制。
- 是否有完整操作審核或 rollback。

下一步 evidence：

- 各同步方法寫入的 Redis key 與 field。
- runtime service 讀取這些 key 的 repo / class。
- 操作權限與操作紀錄位置。

### 3. `daily-game-record-summary`

中文名稱：每日遊戲紀錄 / RTP 彙總

推薦程度：中高，適合第三條，或在讀 `game_job` 後再做。

理由：

- 遊戲投注、派彩、RTP、局紀錄是博弈平台核心資料。
- 已確認 `GameData.php` 會同時讀 Redis 即時資料與歷史 daily table。
- 很適合討論 projection、資料延遲、跨日查詢、今日即時資料與歷史資料合併。
- 但如果只讀 `app_bi`，容易停在報表展示層；需要串 `game_job` 或資料產生端才完整。

核心風險：

- Redis 今日資料與歷史表資料重複或漏算。
- daily table 產生延遲，造成後台看到的數字與交易真相不一致。
- RTP / bet / win 金額單位轉換錯誤。
- 跨 channel / game / cps 篩選條件不一致。

只能算推測：

- Redis `gameData` 寫入來源。
- `daily_rtp_total` 產生排程。
- 報表是否用於正式對帳或只是營運觀察。

下一步 evidence：

- `game_job` 或其他 aggregation job。
- Redis `gameData` producer。
- `daily_rtp_total` 寫入端。

### 4. `game-round-record-query`

中文名稱：遊戲局紀錄查詢

推薦程度：中，適合排查故事，但不建議第一條。

理由：

- 已確認大量後台頁面會查 `GameRound` controller。
- `_getGameInfo()` 會依玩家、局號、serial id、時間區間查 daily log table。
- `_getRecord()` 解析 `common_data`，轉成投注、派彩、餘額、離線狀態等展示欄位。
- 適合講 production troubleshooting：玩家申訴、局號查詢、派彩查核。

核心風險：

- 查詢條件不足或時間區間過大造成查詢壓力。
- daily table 分表與跨日查詢造成漏資料。
- `common_data` 解析邏輯與遊戲實際資料格式不一致。
- 第三方遊戲與自研遊戲的局資料格式不同。

只能算推測：

- `log_reel_{Y_n_j}` 寫入端。
- 第三方 ANTPLAY / PG 資料是否與本地遊戲同一套 source of truth。
- Nick 是否曾用這條 flow 排查 production issue。

下一步 evidence：

- `slotsLogDBV()` 連線來源。
- `log_reel_*` 寫入服務。
- 第三方遊戲局資料同步或落地流程。

### 5. `app-bi-report-export`

中文名稱：BI 報表查詢與匯出

推薦程度：中，價值高但不宜第一條。

理由：

- BI 報表對營運、對帳、觀察趨勢有價值。
- 已確認有支付、提現、遊戲資料、Excel 匯出等入口。
- 但只讀 `app_bi` 容易變成「查詢頁整理」，Senior / Owner 深度要靠資料產生端、匯出壓力與對帳邊界補足。

核心風險：

- 報表資料被誤認為交易真相。
- 大範圍匯出拖垮 PHP / DB / Mongo。
- 分頁與匯出結果不一致。
- 報表延遲造成營運誤判。

只能算推測：

- 匯出是否有背景任務或限流。
- 報表資料是否作為正式對帳依據。
- BI log 由哪個服務產生。

下一步 evidence：

- Mongo BI log producer。
- Excel 匯出限制與生成方式。
- 報表與正式交易表的對帳規則。

### 6. `admin-rbac-permission-check`

中文名稱：後台 RBAC / 權限判斷

推薦程度：中低，重要但不適合作為第一條 Senior case。

理由：

- 權限是後台控制面的安全邊界。
- 已確認有 `auth/permission`、role、role_menu、auth_role、members、Redis / DB 權限資料。
- 但目前 evidence 比較偏後台管理功能，面試價值不如直接影響 runtime 的 point-control。

核心風險：

- 權限 cache 與 DB 不一致。
- 前端只做 menu 隱藏，後端未完整阻擋。
- 角色刪除、menu 更新、user role 更新後 cache 未失效。
- 高風險操作沒有二次確認或稽核。

只能算推測：

- 所有後台操作是否都經過 `auth/permission`。
- 權限 cache invalidation 是否完整。
- Nick 是否實際維護過 RBAC。

下一步 evidence：

- Login / session / middleware。
- 前端權限檢查呼叫點。
- 每個 controller 是否有後端權限防護。

## 維度比較

| Flow | Data Consistency | Transaction Boundary | State Transition | Idempotency | Retry / Compensation | Cache Consistency | MQ / Async | Downstream / Callback | Observability | Manual Recovery | Interview Value | Resume Value |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `point-control-admin-operation` | 高 | 高 | 高 | 中 | 高 | 高 | 低 | 高 | 中 | 高 | 高 | 中高 |
| `admin-config-redis-sync` | 中高 | 中 | 中 | 中 | 中高 | 高 | 低 | 中 | 中 | 高 | 高 | 中 |
| `daily-game-record-summary` | 高 | 中 | 中 | 低 | 中 | 中高 | 中 | 中 | 中 | 中 | 中高 | 中 |
| `game-round-record-query` | 中高 | 低 | 中 | 低 | 低 | 低 | 低 | 中 | 中 | 高 | 中 | 中 |
| `app-bi-report-export` | 中 | 低 | 低 | 低 | 低 | 低 | 低 | 低 | 低中 | 中 | 中 | 低中 |
| `admin-rbac-permission-check` | 中 | 中 | 中 | 中 | 低 | 中高 | 低 | 低 | 中 | 中 | 中 | 中 |

## 最適合先做成 Case Study

第一選擇：

```text
point-control-admin-operation
```

原因：

- 它是目前 `app_bi` 裡最像 production owner 的 flow。
- 不是單純查詢，而是「後台操作 -> DB / Redis -> GM command -> runtime effect -> operation log」。
- 可以自然追問一致性、失敗補償、人工恢復、權限、操作稽核。
- 即使最後確認 Nick 只是對接 / 分析，也能保守包裝成「理解與分析後台控制面如何影響 runtime」。

第二選擇：

```text
admin-config-redis-sync
```

原因：

- 它可以訓練 Platform Backend 的 cache projection 與 runtime 設定一致性。
- 但要補 Redis consumer evidence 才能完整。

第三選擇：

```text
daily-game-record-summary
```

原因：

- 它有報表與遊戲數據價值。
- 但要先找到資料產生端，否則會停在 BI 展示層。

## 不建議先做的 Flow

暫不建議先做：

- `app-bi-report-export`：容易變成報表 CRUD / Excel 匯出整理。
- `game-round-record-query`：很適合排查，但需要先串 log 寫入端。
- `admin-rbac-permission-check`：重要，但單獨當第一個 case 的 production impact 不如 point-control 直接。

## 下一步要補的 Evidence

如果做 `point-control-admin-operation`，下一步先補：

1. `sendGmCommand()` 定義、參數、錯誤處理。
2. GM command 接收端 repo / service。
3. Redis player control hash 的 consumer。
4. `point_control` DB schema 與狀態欄位意義。
5. `log_point_control` Mongo operation log 是否有查詢 / 補償入口。
6. batch add / edit 的部分成功處理方式。
7. Nick 實際參與 evidence：MR / ticket / commit / 本人確認。

如果做 `admin-config-redis-sync`，下一步先補：

1. 各同步方法寫入的 Redis key / field。
2. 對應 runtime service consumer。
3. 同步失敗時是否進 wait sync / retry。
4. 設定更新是否有審核 / 回滾 / 操作 log。

如果做 `daily-game-record-summary`，下一步先補：

1. Redis `gameData` producer。
2. `daily_rtp_total` producer。
3. 今日 Redis 與歷史 daily table 的切換 / 合併規則。
4. 是否作為正式對帳或只是營運觀察。

## Claim Boundary

可以說：

- Step 2 已比較 `app_bi` 候選 flow，確認第一優先是 `point-control-admin-operation`。
- `app_bi` 可作為後台控制面、BI 查詢與 production 排查入口來讀。
- 後續若深挖，應以後端資料流與 runtime 影響為主，而不是 PHP 後台頁面本身。

只能說分析 / 參與：

- 在沒有 Nick 本人確認或 MR / ticket 前，只能說「分析 app_bi 後台入口與 production flow 候選」。

不能說：

- 不能寫 Nick 主導 `app_bi`。
- 不能寫 Nick 主導 point-control、Redis sync、BI 報表。
- 不能把 Step 2 ranking 寫進履歷。

