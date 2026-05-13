# iwin app_bi Step 2：候選 Flow 技術點與風險比較

更新時間: 2026-05-13
掃描等級: Level 1 Flow 掃描 / 候選 flow 比較

## 自動重讀紀錄

已重讀 KB:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault:

- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/flow.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/evidence.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/interview.md`
- `projects/iwin/app_bi/flows/point-control-admin-operation/claim-boundary.md`

已重讀 code repo:

- `/Users/nick/Git/iwin/app_bi`
- 目前 branch: `main`
- 遠端 branch 清單
- 近期 git log
- keyword git log
- route / controller / service / admin page 相關入口

未重讀:

- 沒有 checkout 每個遠端分支。
- 沒有逐 commit diff。
- 沒有掃 `game_api`、`game_job`、`iwin_gameserver`、`payment`、`third_games_api`。

## 本次掃描等級判斷

建議等級: Level 1

原因:

- Step 2 的任務是比較 Step 1 candidate flows，不是深挖單一 flow。
- 不應建立新的 `flows/{flow-name}/`。
- 不應逐檔逐行或逐 commit diff。
- `app_bi` 多數線索是後台 / BI / control plane 入口，若要強 evidence，要到下游後端 repo 補。

## 範圍與規則

本文件承接 `README.md` 的 Step 1 候選 flow 盤點。

規則:

- 不深挖單一 flow。
- 不建立新的 flow folder。
- 不更新履歷與自傳。
- 不把 `app_bi` 包裝成 Nick 主開發成果。
- 只比較 Senior / Platform Backend / System Owner 價值。
- 後台 / BI / admin 入口只能作入口參考，沒有下游 evidence 前不得寫履歷 claim。

## Flow Ranking

### 1. `point-control-admin-operation`

中文名稱: 單點控制 / 營運控制操作

推薦程度: 最高，建議繼續補同一條 flow

理由:

- 已有完整 Step 3 初版學習包，延續成本最低。
- 後台操作會寫 DB、Redis，並送 GM command，具備 runtime 影響。
- 已確認有 MySQL `point_control`、Redis `playerControl:playerControls:data`、Mongo `log_point_control`、`sendGmCommand()`。
- 最符合 Senior / Owner 要看的 control plane 風險: state transition、consistency、retry、compensation、audit。

核心風險:

- DB transaction 管不到 Redis、GM command、Mongo log。
- Redis 已寫但 GM command 失敗，可能出現 DB rollback 但 Redis 留存。
- GM command 成功但 audit log 失敗，可能操作成功但稽核不足。
- 批量新增 / 修改可能 partial success。
- 下游 runtime consumer 未確認，不能說完整後端 flow 已掃完。

只能算推測:

- GM command 具體接收端。
- runtime consumer 如何讀 Redis。
- Nick 是否實際維護或主導此 flow。

下一步 evidence:

- 補 `evidence.md` 的掃描範圍與未確認邊界。
- 定位 GM command receiver / runtime consumer。
- 後續再決定是否需要 Level 2 / Level 3。

履歷適合度:

- 目前中等。可作學習與面試素材。
- 未補 Nick 實際參與 evidence 前，不更新履歷。

### 2. `payment-order-status-repair`

中文名稱: 充值 / 提現訂單狀態修正

推薦程度: 高，但不建議只在 `app_bi` 深挖

理由:

- 涉及充值、提現、訂單狀態修正，是 money correctness 高風險入口。
- git log 有 `RD-142`、`payment_order_search`、`RD-371` 這類訂單修復、跨月查詢、redis lock 線索。
- Senior / Owner 價值很高，但真正核心應在 `payment` 或錢包 / 訂單後端，不該停在 PHP 後台。

核心風險:

- 後台誤修訂單狀態造成入帳 / 出款錯誤。
- 跨月查詢或修復錯表。
- 修正與 payment source of truth 不一致。
- 人工修正缺少 audit、approval、reconciliation。
- redis lock 或審核 lock 若使用不當，可能重複處理或卡單。

只能算推測:

- 修正後是否觸發錢包、通知、對帳或補償。
- `payment` repo 是否有真正狀態機與 callback flow。
- Nick 是否參與相關修復。

下一步 evidence:

- `/Users/nick/Git/iwin/payment` Step 1 / Step 2。
- 查 payment callback、order state machine、wallet update、manual repair、audit log。
- 查 `RD-142` / `RD-371` 對應 diff 與後續收斂。

履歷適合度:

- 潛力高，但目前不能寫。
- 需先轉去後端 repo 補 evidence。

### 3. `admin-config-redis-sync`

中文名稱: 後台設定同步 Redis

推薦程度: 高，適合做 control plane 第二條

理由:

- `RedisSynchronize.php` 集中處理大量 `save*ToRedis()`。
- 後台設定同步到 Redis 是典型 control plane -> runtime projection。
- 適合練 cache consistency、設定發布、回滾、版本、同步失敗、runtime consumer。

核心風險:

- DB 設定已改，但 Redis 未同步。
- Redis 同步成功，但 runtime service 讀到舊值。
- 多 channel / 多設定同步 partial success。
- 缺少同步結果、版本、審核、rollback，會讓 production 問題難排。

只能算推測:

- 每個 Redis key 的實際 consumer。
- 是否有 retry / wait sync / compensation。
- 是否有完整操作審核。

下一步 evidence:

- 列出各 `save*ToRedis()` 寫入 key / field。
- 找對應 runtime service consumer。
- 查是否有 operation log、retry、rollback。

履歷適合度:

- 中等。需要 consumer evidence 才能轉成強 case。

### 4. `daily-game-record-summary`

中文名稱: 每日遊戲資料彙總 / RTP 查詢

推薦程度: 中高，但需搭配 producer repo

理由:

- 遊戲投注、派彩、RTP、留存與 daily summary 是博弈平台核心資料。
- `GameData.php` 讀 `daily_rtp_total`、今日資料與歷史資料；git log 有多筆 `daily_game_record_summary`。
- 很適合討論 projection、資料延遲、跨日、Redis 今日資料與歷史表合併。

核心風險:

- Redis 今日資料與 daily table 重複或漏算。
- daily summary job 延遲，後台數字與交易真相不一致。
- 金額倍率轉換錯誤。
- report 被誤用成 source of truth。

只能算推測:

- Redis 今日資料 producer。
- `daily_rtp_total` / `log_game_daily_record` 寫入端。
- 是否有正式 reconciliation。

下一步 evidence:

- `/Users/nick/Git/iwin/game_job`
- `daily_rtp_total` producer。
- `log_game_daily_record` producer。
- 今日 Redis 與歷史 table 合併規則。

履歷適合度:

- 中等。只讀 `app_bi` 不夠。

### 5. `game-round-record-query`

中文名稱: 遊戲局紀錄查詢 / production troubleshooting 入口

推薦程度: 中，適合排查故事

理由:

- 大量 `public/admin/gameRound/*.html` 與 `GameRound.php` 形成遊戲局查詢入口。
- 可用於玩家申訴、派彩爭議、局號查詢、第三方遊戲排查。
- git log 有 PG / GSC / ANTPLAY / RD-146 等 provider 查詢修正線索。

核心風險:

- 查詢時間區間過大造成 DB 壓力。
- `log_reel_*` daily table 跨日漏查。
- `common_data` 解析與實際 game log schema 不一致。
- 第三方 provider 與自研遊戲資料格式不同。

只能算推測:

- `log_reel_*` 寫入端。
- third-party provider record sync 的 truth source。
- Nick 是否用這條 flow 排查過 production issue。

下一步 evidence:

- `iwin_gameserver` / `third_games_api` / `game_api` 的 log writer。
- provider record sync / callback / settle path。

履歷適合度:

- 中低到中。適合面試排查故事，不適合單獨當強履歷 bullet。

### 6. `app-bi-report-export`

中文名稱: BI 報表查詢與匯出

推薦程度: 中

理由:

- 報表與匯出對營運有價值。
- `PayData.php`、`WithdrawalData.php`、`DataReportService.php`、多個 admin page 有查詢 / 匯出入口。
- 但只讀 `app_bi` 容易變成報表 CRUD / Excel 匯出整理。

核心風險:

- 報表資料被誤認為交易真相。
- 大範圍匯出拖垮 PHP / DB / Mongo。
- 分頁查詢與匯出結果不一致。
- BI log producer 延遲或漏資料。

只能算推測:

- 匯出是否有限流或背景任務。
- BI log producer。
- 報表是否用於正式對帳。

下一步 evidence:

- BI / Mongo log producer。
- 匯出限制、row limit、背景任務。
- 對帳規則。

履歷適合度:

- 低到中。除非有實際優化或事故處理 evidence。

### 7. `admin-rbac-permission-check`

中文名稱: 後台 RBAC / 權限判斷

推薦程度: 中低

理由:

- RBAC 是所有 admin operation 的安全邊界。
- `Auth.php` 有 role / menu / permission 相關邏輯。
- 但本次只確認到後台權限入口，不足以形成強 production flow。

核心風險:

- 前端 hide menu 但後端未阻擋。
- 權限 DB / cache 不一致。
- 角色異動後 session / cache 未失效。
- 高風險操作沒有二次確認或 audit。

只能算推測:

- 所有 controller 是否都經過後端權限防護。
- 權限 cache invalidation 是否完整。
- Nick 是否維護過 RBAC。

下一步 evidence:

- Login / session / middleware。
- 前端 permission call path。
- 高風險 controller 是否有後端防護。

履歷適合度:

- 中低。除非有明確修權限或安全邊界 evidence。

## 維度比較

| Flow | Data Consistency | Transaction Boundary | State Transition | Idempotency | Retry / Compensation | Cache Consistency | MQ / Async | Downstream / Callback | Observability | Manual Recovery | Interview Value | Resume Value |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `point-control-admin-operation` | 高 | 高 | 高 | 中 | 高 | 高 | 低 | 高 | 中 | 高 | 高 | 中 |
| `payment-order-status-repair` | 高 | 高 | 高 | 中 | 高 | 中 | 低 | 中高 | 高 | 高 | 高 | 高待補 |
| `admin-config-redis-sync` | 中高 | 中 | 中 | 中 | 中高 | 高 | 低 | 中 | 中 | 高 | 高 | 中 |
| `daily-game-record-summary` | 高 | 中 | 中 | 低 | 中 | 中高 | 中 | 中 | 中 | 中 | 中高 | 中 |
| `game-round-record-query` | 中高 | 低 | 中 | 低 | 低 | 低 | 低 | 中 | 中 | 高 | 中 | 中低 |
| `app-bi-report-export` | 中 | 低 | 低 | 低 | 低 | 低 | 低 | 低 | 低中 | 中 | 中 | 低中 |
| `admin-rbac-permission-check` | 中 | 中 | 中 | 中 | 低 | 中高 | 低 | 低 | 中 | 中 | 中 | 中低 |

## 最適合先做成 Case Study

第一選擇:

```text
point-control-admin-operation
```

原因:

- 已經有 Step 3 文件，最符合「慢慢讀、同一條補完整」。
- 在 `app_bi` 內 evidence 最完整。
- 能練習後台控制面如何安全影響 runtime。
- 但下一步要先補「下游未掃 / 掃描邊界」，不能硬寫履歷。

第二選擇:

```text
payment-order-status-repair
```

原因:

- 金流訂單修正的 Senior 價值很高。
- 但不建議繼續只掃 `app_bi`；應轉去 `payment` repo。

第三選擇:

```text
admin-config-redis-sync
```

原因:

- 可以形成 control plane 主題。
- 需要補 Redis consumer evidence。

## 哪些 Flow 只看到後台 / BI 入口

目前不適合直接寫履歷:

- `point-control-admin-operation`: 只確認到 `app_bi` 發送端，尚未確認 GM receiver / runtime consumer。
- `payment-order-status-repair`: 只確認到人工修正入口，尚未確認 payment source of truth。
- `daily-game-record-summary`: 只確認到查詢 / 展示端，尚未確認 producer。
- `game-round-record-query`: 只確認到查詢端，尚未確認 log writer。
- `app-bi-report-export`: 只確認到報表 consumer，尚未確認 BI log producer。
- `admin-rbac-permission-check`: 只確認到權限入口，尚未確認完整 middleware / backend enforcement。

## 下一步要補的 Evidence

如果繼續 `point-control-admin-operation`，下一步只補一件事:

```text
補掃描範圍與未確認邊界
```

要更新:

- `projects/iwin/app_bi/flows/point-control-admin-operation/evidence.md`
- 必要時同步微調 `flow.md` 的「待確認 / 下游未掃」敘述。

不做:

- 不更新履歷。
- 不 commit / push，除非 Nick 要求。
- 不把後台入口包裝成完整後端 owner。

## Claim Boundary

可以說:

- Step 2 已重新比較 `app_bi` 候選 flow。
- `point-control-admin-operation` 仍是 `app_bi` 內最值得先補完整的一條。
- `payment-order-status-repair` 很有 Senior 價值，但應轉到 `payment` repo 才能深挖。

只能說分析 / 參與:

- 在沒有 Nick 本人確認或 MR / ticket 前，只能說「分析 app_bi 後台入口與 production flow 候選」。

不能說:

- 不能寫 Nick 主導 `app_bi`。
- 不能寫 Nick 主導 point-control、Redis sync、BI 報表或 payment repair。
- 不能把 Step 2 ranking 寫進履歷。

