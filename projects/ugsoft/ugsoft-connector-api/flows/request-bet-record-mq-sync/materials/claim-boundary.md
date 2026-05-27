# Claim Boundary - request-bet-record-mq-sync

## Step 5 判定

本 flow 已完成 Step 3 learning package、Step 4 interview case 與 Step 5 claim gate。它可以作為 `ugsoft-connector-api` project-level provider connector / bet record MQ / job sync claim 的強化 evidence，但不直接更新 `05 / 08 / 04 / 17`，也不代表整個 project final consolidation 已完成。

## 證據層級

| 類型 | 判定 |
| --- | --- |
| Nick / `10gt12nc` direct evidence | BetRecord MQ、job、跨日 `pt_day`、DerPlay 日期、currency default / currency 修正。 |
| code-backed / team context | Redis watermark、amount normalization、subAgent 傳遞、部分最新 provider behavior。 |
| analysis-only | MQ publish failure vs watermark、DerPlay per-currency partial success、pagination / DLQ / monitoring 風險分析。 |
| 不可當 Nick direct evidence | `arnold` commits；Nick 已確認 `arnold` 是主管。 |

## 可以說

- 參與 UGSoft provider connector / gateway 中 request / bet record MQ、job sync、跨日查重與 currency 修正的開發 / 維護。
- 看過並能說明 provider bet record sync 的 Quartz job、Redis watermark、DB 查重與 MQ 補資料路徑。
- 能指出這條 flow 的 production 風險：watermark 推進、MQ publish failure、per-currency partial success、pagination。

## 可放履歷 / project-level consolidation

- 可把本 flow 併入 `ugsoft-connector-api` 的「provider connector / gateway、request / bet record MQ、job-driven late data sync、跨日查重、currency boundary」素材。
- 建議語氣用「參與 / 維護 / 處理」，不要用「主導 / 完整 owner」。
- 若篇幅很短，可併入既有 UGSoft provider connector bullet，不建議為本 flow 單獨新增一條正式履歷 bullet。

## 只能保守說

- 可以說「補資料 / late data sync / compensation-like path」。
- 可以說「支援 callback path 以外的 bet record 補抓」。
- 可以說「有 code-backed 分析與 direct commits 支撐部分 MQ / job / currency / pt_day 維護」。

## 不可說

- 不說主導完整 reconciliation。
- 不說 exactly-once。
- 不說完整 outbox / DLQ replay 平台。
- 不說完整 bet record pipeline owner。
- 不說完整 UGSoft connector architecture owner。
- 不把 `arnold` commits 或 team context 包成 Nick 親自開發。

## 履歷回填規則

本 flow Step 5 不直接改 `05-resume-master-zh.md`、`08-application-autobiography-zh.md`、`04-interview-casebook.md`、`17-salary-negotiation.md`。

本輪已回填到 `ugsoft-connector-api` contribution consolidation，強化以下 project-level claim：

- provider connector / gateway
- request / bet record MQ
- job-driven late data sync
- cross-day duplicate check
- currency normalization / default boundary

但仍不得寫成完整 reconciliation 或 complete owner。若之後要正式改 05 / 08，必須走 rolling resume package 或 project-level consolidation refresh，不直接吃單條 flow。
