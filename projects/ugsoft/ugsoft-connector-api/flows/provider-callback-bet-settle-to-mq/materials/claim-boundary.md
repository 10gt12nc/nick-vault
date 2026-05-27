# provider-callback-bet-settle-to-mq Claim Boundary

## Step 4 判定

Step 4 已完成正式面試 case。本 flow 可作 `ugsoft-connector-api` provider callback / MQ / bet record pipeline 的強面試素材，但目前尚未完成 Step 5 claim gate，因此不直接更新正式履歷 / 自傳。

## 可以說

- 參與 UGSoft provider connector callback / bet-settle / bet record MQ pipeline。
- 參與 AntPlay / DerPlay callback 後的 MQ payload normalization。
- 參與 connector-api producer 與 admin-api consumer 對接分析 / 維護。
- 能面試講清楚 callback 驗簽、adapter callback 成功後送 MQ、downstream consumer duplicate check 與 bet record 入庫。
- 能說 Nick / `10gt12nc` 有 callback 寫 MQ、BetRecord MQ 入庫、currency / pt_day 修正 direct commits。

## 只能說 code-backed / 團隊 context

- Provider IP whitelist。
- 二級代理 subAgent / response rewrite。
- 最新 amount scaling 行為。
- betSettle error code propagation。
- request log 改 connectorAgentId。
- 完整 callback service refactor。

這些主要是 `arnold` / 團隊 context；Nick 已確認 `arnold` 是主管帳號，不得當成 Nick direct evidence。

## 不能說

- 主導完整 UGSoft callback / MQ / bet record architecture。
- 設計完整 exactly-once / outbox / DLQ。
- 完整 owner admin-api consumer、quota update、bet record reconciliation。
- 完整解決 callback 重送、MQ missing、amount scaling、currency、duplicate 所有問題。
- 有量化改善或 production incident owner。

## Step 5 前待補

- 若要升級履歷 claim，Step 5 需再確認 direct commits 和 current behavior 的邊界。
- 若要更強 claim，需補 Rabbit listener ack / retry / DLQ、monitoring、incident / ticket evidence。
