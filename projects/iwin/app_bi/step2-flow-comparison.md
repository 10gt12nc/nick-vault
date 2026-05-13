# iwin app_bi Step 2：候選 Flow 技術點與風險比較

更新時間: 2026-05-13
掃描等級: Level 1 Flow 掃描 / 候選 flow 比較
狀態: 已依新版 KB 重整

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
- 目前分支: `main`
- 遠端分支清單
- 近期主線 log
- path-specific log
- route / controller / service / model / public admin page / migration / crontab 入口

未重讀:

- 未 checkout 每個遠端分支。
- 未逐 commit diff。
- 未掃 `game_api`、`game_job`、`iwin_gameserver`、`payment`、`third_games_api`。

## 掃描等級判斷

本次 Step 2 使用 Level 1。

原因:

- Step 2 是比較 Step 1 candidate flows，不是深挖單一 flow。
- 不建立新 flow folder。
- 不逐檔逐行，不逐 commit diff。
- `app_bi` 多數是後台 / BI / control plane 入口；強 evidence 必須補下游 repo。

## 比較前提

本文件只比較 Senior / Platform Backend / System Owner 價值。

不做:

- 不更新履歷。
- 不把 `app_bi` 包裝成 Nick 主開發成果。
- 不把後台入口寫成完整後端 owner。

## Flow Ranking

### 1. `point-control-admin-operation`

中文名稱: 單點控制 / 營運控制操作
狀態: 已完成 Step 1-5，不更新履歷

推薦程度: 最高，先重整 Step 3

理由:

- `app_bi` 內 evidence 最完整。
- 會跨 MySQL、Redis、GM command、Mongo log。
- 已有 Step 3 舊版文件，現在應先補新版 KB 邊界。
- 最符合 control plane 影響 runtime 的 Senior / Owner 練習題。

核心風險:

- MySQL transaction 不覆蓋 Redis、GM command、Mongo log。
- Redis 先寫但 GM command 失敗，可能留下髒狀態。
- DB commit 後 Mongo log 失敗，可能造成 audit 不完整。
- 批量 GM command retry 後沒有明確中止 / partial failure 處理。
- 下游 receiver / runtime consumer 未掃。

只能算推測:

- 下游 GM command handler。
- runtime 是否直接讀 Redis。
- GM command idempotency。
- Nick 實際參與程度。

下一步 evidence:

- 重整 `flow.md` 與 `evidence.md`。
- 明確標記「只確認到 app_bi 發送端」。
- 下游後續再去 `game_api` / `game_job` / `iwin_gameserver` 定位。

履歷適合度:

- 目前: 中。
- 可作面試分析素材。
- 不可直接寫履歷主導。

### 2. `payment-order-status-repair`

中文名稱: 充值 / 提現訂單狀態修正

推薦程度: 高，但不建議只在 `app_bi` 深挖

理由:

- 金流訂單修正是 money correctness 高價值題。
- 但 `app_bi` 只確認到人工修正入口。
- 真正強 case 應轉去 `/Users/nick/Git/iwin/payment`。

核心風險:

- 人工修正錯狀態造成入帳 / 出款錯誤。
- 修正入口與 payment source of truth 不一致。
- approval / audit / reconciliation 不足。
- redis lock 若使用不當，可能卡單或重複處理。

下一步 evidence:

- `payment` repo Step 1 / Step 2。
- callback、order state machine、wallet update、manual repair、audit log。

履歷適合度:

- 潛力高。
- 目前不可寫，需後端 repo evidence。

### 3. `admin-config-redis-sync`

中文名稱: 後台設定同步 Redis
狀態: 已完成 Step 3，下一步 Step 4

推薦程度: 中高，適合之後作 control plane 第二條

理由:

- 後台設定同步 Redis 是典型 runtime projection。
- 能練 cache consistency、config rollout、stale data、rollback。

核心風險:

- DB 改了但 Redis 沒同步。
- Redis 同步成功但 runtime 讀舊值。
- 多 channel / 多設定 partial success。
- 無版本、無回滾、無 reconcile 時難排查。

下一步 evidence:

- 列 `RedisSynchronize.php` 寫入的 key / setting。
- 找 runtime consumer。

履歷適合度:

- 中，需要 consumer evidence。

### 4. `daily-game-record-summary`

中文名稱: 每日遊戲資料彙總 / RTP 查詢

推薦程度: 中高，但需 producer repo

理由:

- 可練 projection、報表延遲、跨日統計、truth source 分離。

核心風險:

- 今日 Redis 與 history table 重複 / 漏算。
- daily job 延遲。
- 金額倍率錯誤。
- 報表被誤認為交易真相。

下一步 evidence:

- `game_job` 或資料 producer。
- `daily_rtp_total` / `log_game_daily_record` 寫入端。

履歷適合度:

- 中，只讀 `app_bi` 不夠。

### 5. `game-round-record-query`

中文名稱: 遊戲局紀錄查詢 / production troubleshooting 入口

推薦程度: 中

理由:

- 適合面試排查故事。
- 可用於玩家申訴、派彩爭議、局號查詢。

核心風險:

- 跨日分表漏查。
- 查詢範圍過大壓 DB。
- game log schema 不一致。
- 第三方 provider record 與自研遊戲資料格式不同。

下一步 evidence:

- `iwin_gameserver` / `third_games_api` / `game_api` 的 log writer。

履歷適合度:

- 中低到中，偏 troubleshooting 素材。

### 6. `app-bi-report-export`

中文名稱: BI 報表查詢與匯出

推薦程度: 中

理由:

- 對營運重要，但只讀 `app_bi` 容易停在查詢 / Excel 匯出。

核心風險:

- 報表資料被當交易真相。
- 大範圍匯出拖垮 DB / PHP / Mongo。
- 匯出與分頁結果不一致。

下一步 evidence:

- producer、row limit、background job、reconciliation 規則。

履歷適合度:

- 低到中。

### 7. `admin-rbac-permission-check`

中文名稱: 後台 RBAC / 權限判斷

推薦程度: 中低

理由:

- 是所有高風險後台操作的安全邊界。
- 但單獨作 case 容易不夠 production。

核心風險:

- 前端 hide menu 但後端未攔。
- 權限 cache stale。
- 高風險操作沒有二次確認 / audit。

下一步 evidence:

- `Base::_initialize()`、`Auth::permission()`、各高風險 controller 的 permission path。

履歷適合度:

- 中低，除非有實際修權限或安全事故 evidence。

## 維度比較

| Flow | 一致性 | 狀態轉換 | 失敗補償 | Cache / Redis | 下游風險 | 面試價值 | 履歷價值 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `point-control-admin-operation` | 高 | 高 | 高 | 高 | 高 | 高 | 中 |
| `payment-order-status-repair` | 高 | 高 | 高 | 中 | 中高 | 高 | 高待補 |
| `admin-config-redis-sync` | 中高 | 中 | 中高 | 高 | 中 | 高 | 中 |
| `daily-game-record-summary` | 高 | 中 | 中 | 中高 | 中 | 中高 | 中 |
| `game-round-record-query` | 中 | 中 | 低 | 低 | 中 | 中 | 中低 |
| `app-bi-report-export` | 中 | 低 | 低 | 低 | 低 | 中 | 低中 |
| `admin-rbac-permission-check` | 中 | 中 | 低 | 中 | 低 | 中 | 中低 |

## 哪些 Flow 只看到後台 / BI 入口

目前不適合直接寫履歷:

- `point-control-admin-operation`: 只確認到 `app_bi` 發送端，未確認 GM receiver / runtime consumer。
- `payment-order-status-repair`: 只確認到人工修正入口，未確認 payment source of truth。
- `admin-config-redis-sync`: 只確認到 Redis 寫入端，未確認 consumer。
- `daily-game-record-summary`: 只確認到查詢 / 展示端，未確認 producer。
- `game-round-record-query`: 只確認到查詢端，未確認 log writer。
- `app-bi-report-export`: 只確認到報表 consumer，未確認 BI log producer。
- `admin-rbac-permission-check`: 只確認到權限框架，未確認所有高風險操作 enforcement。

## Step 2 結論

目前已完成:

```text
point-control-admin-operation Step 1-5
admin-config-redis-sync Step 3
```

下一步應該做:

```text
app_bi admin-config-redis-sync Step 4
```

要更新:

- `flows/admin-config-redis-sync/interview.md`

不做:

- 不更新履歷。
- 不把 `app_bi` 當成 Nick 主開發成果。
- 不說完整後端 flow 已掃完。
