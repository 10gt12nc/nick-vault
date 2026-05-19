# Claim Boundary: partition-table-creation

## Step 4 判定

目前判定：不更新正式履歷 / 自傳。

證據層級：專案存在 / code-backed。

原因：

- 已深讀 `CreateTableJob`、`InitTableConfig`、Quartz 註冊與 SQL templates，確認它是 table rollover support flow。
- direct path history 沒有看到 Nick / `10gt12nc` direct commit 可以證明 Nick 真實開發這條 flow。
- main / `origin/k3s` config 目前都 `createTableEnable=false`，production 實際啟用未確認。
- 這條本身是支撐性 flow，不適合單獨包裝成履歷主成果。
- Step 4 已整理成正式面試 case，但沒有新增本人確認、MR、ticket 或 direct commit evidence。

## 可說

- 我分析過一條每日 / 每月分表建立 flow。
- 這條 flow 用 SQL template 替 `bi_log` 與各 channel DB 建下一期分表。
- 我能說明 table rollover、`CREATE TABLE IF NOT EXISTS` idempotency、schema drift、partial success 與 observability 風險。

## 不可說

- 我開發這條 flow。
- 我主導 schema migration / table rollover platform。
- 我負責完整 BI / game log schema 管理。
- production 實際啟用狀態已確認。
- 我改善了建表成功率、事故數或耗時 X%。

## Step 5 前需要補的 evidence

- Nick 本人確認是否實際參與過 create table / schema rollover。
- `10gt12nc` 在 direct path 的 commit / branch / MR / ticket。
- production 是否使用外部 config 啟用 create table job。
- 缺表 incident 或跨日 / 跨月補表 issue。

## Step 5 初步預期

Step 5 應做 claim gate。若沒有新增 evidence，大概率不更新正式履歷 / 自傳，只把此 flow 保留為「code-backed 面試補充素材」。
