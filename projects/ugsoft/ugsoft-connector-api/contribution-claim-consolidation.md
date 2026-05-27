# ugsoft-connector-api Contribution Claim Consolidation

日期: 2026-05-20

## 結論

`ugsoft-connector-api` 可以列為 Nick 真實開發過、且履歷價值高於一般後台 CRUD 的 provider connector / gateway repo。Nick / `10gt12nc` 在 `origin/master` 與相關分支可見大量 direct commits，範圍包含:

- AntPlay adapter：login / info / balance / request log / transfer API / bet record。
- DerPlay adapter：login / prepare enter game、balance、transfer in / out / out-all、single transaction、bet record。
- callback path：AntPlay / DerPlay callback 驗簽、balance / bet-settle、成功後送 bet record MQ。
- transfer wallet：provider position、provider switch 時 inner transfer、transfer in/out before-after balance / query order。
- request log / bet record MQ：單一錢包 callback 寫 MQ、跨日 `pt_day`、currency default、job sync。
- reliability：Circuit Breaker fast-fail、deadlock compensation、DB partition / schema route、white IP filter。

履歷可以保守寫:

> 參與 UGSoft provider connector / gateway 開發與維護，串接 AntPlay / DerPlay 等第三方遊戲 provider，處理 login、balance、transfer in / out、bet-settle、callback、request / bet record MQ、熔斷與轉帳錢包補償等流程。

不要寫:

- 不得寫成主導完整 UGSoft 平台。
- 主導全部 provider integration。
- 不得寫成主導完整 wallet / ledger / reconciliation。
- 建立 exactly-once / outbox。
- 完整架構 owner 或量化改善。

## Evidence Summary

| 類別 | 判斷 | Evidence |
| --- | --- | --- |
| 直接開發 | 真實開發過 | `origin/master` 自 2024 起約 179 筆 Nick / `10gt12nc` commits |
| Provider adapter | 真實開發過 | `feat: ugAdapter`、`adapter 串AntPlay`、`adapter 串DerPlay` 系列 commits |
| Transfer wallet | 真實開發過 | AntPlay / DerPlay transfer in / out / out-all、provider position、deadlock compensation commits |
| Callback / bet-settle | 真實開發過 | AntPlay / DerPlay callback、bet-settle、bet record MQ commits |
| MQ / async | 真實開發過 | `单一钱包：回调时候写mq`、`feat: mq`、`request log MQ`、`job BetRecordMq` |
| Reliability | 真實開發過 + code-backed | Circuit Breaker docs / code、deadlock 補償、DB partition / schema route、white IP filter |
| final 全量 flow | 待 refresh | Step 1 / Step 2 已建立；本批三條代表 flow `transfer-wallet-in-out-query`、`provider-callback-bet-settle-to-mq`、`request-bet-record-mq-sync` 均已完成 Step 5。本檔仍是 2026-05-20 rolling consolidation，待 `contribution claim consolidation refresh` 將三條 Step 5 全量校正進 project-level claim。 |

## Source Scan Record

來源 repo:

```text
/Users/nick/Git/ugsoft/ugsoft-connector-api
```

掃描方式:

- 已執行 `git fetch --all --prune` 更新 remote refs。
- local branch: `Nick_Test`
- local HEAD: `c2cab730c0cd6ead6d92a038ef56f97987577059`
- remote HEAD: `origin/develop`
- `origin/develop`: `079aa6603b50db3c185e383295ca5966bbe272fb`
- `origin/master`: `4bd2195e1e574978f11a1d4b5e744792f16ecad0`
- local vs `origin/master` ahead / behind: `0 / 61`
- local vs `origin/develop` ahead / behind: `190 / 0`
- source repo 工作樹不乾淨：有 `.DS_Store`、test、docs 等 local changes / untracked files。本次只讀 remote objects，不改 source repo，不把本機髒檔當正式 evidence。
- remote HEAD 雖指向 `origin/develop`，但 AntPlay / DerPlay adapter 與 latest tags 位於 `origin/master`；本次 provider connector claim 以 fetched `origin/master` 為主要 evidence，`origin/develop` 作背景。

2026-05-26 code / KB recheck:

- 重新 fetch 成功，`origin/master` 已前進；本檔保留原本 contribution claim，不因新 remote refs 自動擴張履歷主張。
- source repo 仍有本機未提交 / untracked 內容，本檔只採 remote objects、git history 與已讀 source path，不採髒工作樹作履歷 evidence。
- 目前結論仍成立：可寫 provider connector / gateway、AntPlay / DerPlay adapter、callback / MQ、transfer wallet / compensation 類真實開發與 code-backed 維護；不可寫完整 connector architecture、全部 provider owner 或完整 wallet / reconciliation owner。

本次掃描範圍:

- `git log origin/master --author='10gt12nc|Nick|nick'`
- `git log origin/develop --author='10gt12nc|Nick|nick'`
- `git log --all --author='10gt12nc|Nick|nick'`
- `git show --stat --name-only` 抽查 key commits
- `src/main/java/com/ps/domain/connector/controller`
- `src/main/java/com/ps/domain/connector/service`
- `src/main/java/com/ps/domain/connector/adapter`
- `src/main/java/com/ps/domain/connector/vo`
- `src/main/java/com/ps/domain/api/game/facade/TransferFacade.java`
- `src/main/java/com/ps/domain/game/slot/...` 相關 bet record / compensation / job path
- `src/main/java/com/ps/common/rabbitMq` / `RabbitMQConfig` / `RabbitMq`
- `docs/circuit-breaker.md` 只讀摘要，未複製內部測試 URL。
- vault 既有 `projects/`、`senior-owner-playbook/` 內 ugsoft / resume 關鍵字；`archive/` 已清空，不再作必要來源

未完成:

- 已補 `ugsoft-connector-api` Step 1 / Step 2；尚未做單條 flow 深掃。
- 未逐條 flow 建立 `flow.md` / `career-interview.md`。
- 未逐檔逐行 Level 3。
- 未驗證每個 provider adapter 是否已實際上線。

## Important Commit Evidence

### UG Adapter / Provider gateway 初版

- `98eb9b9`：`feat: codex實作ugAdapter //todo:測試`，新增 `UgAdapterController`、多個 action handler、idempotency / transfer order / wallet service、callback / transfer controller 與 VO。
- `a22282f` / `e236a59` / `33fb737` / `52d52be`：`ugAdapter` login / info / balance / betSettle / transfer、移動到 connector、命名與欄位說明。
- 這組 evidence 可支撐「參與 connector gateway 初版與 action handler / idempotency 概念」，但因後續 `origin/master` 已收斂到 `domain/connector` adapter path，正式說法要以 AntPlay / DerPlay adapter 為主。

### AntPlay adapter

- `9913ed2` 到 `5e4448a`：`adapter 串AntPlay` login / info / balance / login log 系列。
- `842a3df`：`adapter 串AntPlay balance`。
- `92ad98b`：`adapter 串AntPlay requestLog`。
- `575996d` / `80828c1` / `04473d5`：AntPlay Transfer API 與 bet record。
- `59121a3`：`adapter 串AntPlay bet_settle back串AntPlay回傳格式`。

主要 code path:

- `ConnectClientController`
- `ConnectClientService`
- `ConnectAntplayAdapter`
- `AdapterClient`
- `ConnectCallbackService`

### DerPlay adapter

- `48ee13b` / `afff4b1` / `04e2c84`：DerPlay login / transfer in / out / balance / out-all。
- `1a1c0b1` / `92b6a1f`：DerPlay single transaction。
- `6c20862` / `f82eda6` / `fe16c76`：DerPlay transaction bet record。
- `df1fdcf`：DerPlay 單一錢包日期修正。

主要 code path:

- `ConnectDerPlayAdapter`
- `CallbackDerplayService`
- `DerPlayService`
- `DerPlayAuthTokenRepository`

### Callback / Bet record MQ / async

- `e95b353`：`单一钱包：回调时候写mq`，新增 / 調整 RabbitMQ config、callback service、`ConnectBetRecordMqService` 與 MQ DTO。
- `120c0fb`：callback 寫 MQ log。
- `a173cf3`：`feat: mq`，包含 `ConnectBetRecordMqService`、AntPlay / DerPlay bet record sync job、repository 與 schedule service。
- `3e9d0d0` / `2369690`：MQ 跨日 `pt_day`、request log MQ 時間差風險。
- `edf8f26` / `ac6d25c` / `5747ff3`：job MQ currency default 修正。

可支撐:

- 參與 callback 後的 bet record eventual consistency pipeline。
- 參與 MQ payload normalization、時間 / 分區 / currency 邊界修正。

不可誇大:

- 不說完整 outbox / exactly-once。
- 不說完整 reconciliation owner。

### Transfer wallet / compensation

- `54078fe` / `a2b2af5` / `31d7a46`：轉帳錢包 deadlock 補償，涉及 `AgentApiFacade`、`GameFacade`、`GameFlowFacade`、`BetRecordManageService`、`TransferBalanceService` 與 compensation path。
- `ConnectClientService` 在 transfer wallet agent login 時維護 provider position；provider switch 時 best-effort transfer out 舊 provider，再 transfer in 新 provider。
- `ConnectDerPlayAdapter` 的 transfer in / out 會做 before balance、create transfer order、transfer chip、query order、after balance。

可支撐:

- 參與 transfer wallet provider position / provider switch / compensation 類問題。
- 面試可講 failure window：transfer out 成功但 transfer in 失敗、query order 不一致、callback / MQ 落後。

不可誇大:

- 不說完整 wallet source of truth owner。
- 不說已完整解決所有 deadlock / reconciliation。

### DB partition / schema / request log

- `6f6caa7` 到 `311ece0`：`feat(#167)` bet record / request log 拆表、查詢、SQL / notify count / delay 修正。
- `5f7edc9` 到 `72f7dc0`：DB switch、`@UseSchema`、schema route / table existence / partition v2。
- `d3e0002`：RequestLog 改 RabbitMQ 非同步。

可支撐:

- 參與高量資料分表、request / bet record query 與 job retry 類 maintenance。

### Circuit Breaker / provider reliability

`origin/master` 存在:

- `ConnectorCircuitBreaker`
- `ConnectAdapterExecute`
- `docs/circuit-breaker.md`

主要能力:

- provider-level Resilience4j circuit breaker。
- `ConnectAdapterExecute` 包住 HTTP request、request log、response log、elapsed time、非 200 response、`CallNotPermittedException` fast-fail。
- AntPlay / DerPlay provider 分別有 circuit breaker instance。

claim 邊界:

- 可面試講「code-backed 分析 / 參與維護或理解 provider fail-fast」。
- 是否由 Nick 主導設計 circuit breaker，仍待補 commit / ticket；不在履歷主 bullet 寫主導。

## Resume Claim

可放履歷:

- 參與 UGSoft provider connector / gateway 開發與維護，串接 AntPlay / DerPlay 等第三方遊戲 provider，處理 login、balance、transfer in / out、bet-settle、callback 與 transaction / bet record 查詢。
- 參與 callback 後 request / bet record MQ 非同步資料處理，處理 `pt_day`、currency、provider bet id、job sync 與資料入庫邊界。
- 參與 transfer wallet provider position、provider switch、deadlock compensation 與分表 / schema route 類維護。

可面試講:

- Provider adapter contract 怎麼統一到 `ConnectAdapter` / `AdapterClient`。
- 商戶端 `/connect/client/*` 如何驗簽、驗 currency、驗 game / agent，然後轉到 provider adapter。
- AntPlay / DerPlay 的 amount unit、currency、language、sign、cert / token 差異。
- Callback 成功後為什麼送 bet record MQ，MQ eventual consistency 的 duplicate / missing / late message 風險。
- DerPlay transfer in / out 為什麼要 before balance、create order、transfer chip、query order、after balance。
- Provider switch / transfer wallet position 的 failure window。
- Circuit breaker fast-fail 的好處與不能解決的問題。

不可誇大:

- 不說主導完整 UGSoft connector architecture。
- 不說全部 provider integration owner。
- 不說完整 wallet / ledger / reconciliation owner。
- 不說 MQ exactly-once、outbox、完整 DLQ 設計。
- 不說量化改善或完整事故 owner。

## 05 / 08 更新口徑

建議加到履歷 / 自傳時，用一個保守句子併入現職主軸:

> 參與 UGSoft provider connector / gateway 開發維護，串接 AntPlay / DerPlay 等第三方遊戲 provider 的 login、balance、transfer in / out、bet-settle、callback 與 request / bet record MQ，並處理 transfer wallet、分表與 provider fail-fast 相關維護。

若正式投遞篇幅有限，`ugsoft-connector-api` 可和 `ugsoft-admin-api` 合併成一段:

> 參與 UGSoft 後台 API 與 provider connector 維護，範圍包含後台權限 / 白名單、AntPlay / DerPlay provider adapter、transfer wallet、request / bet record MQ、Quartz / report job 與 provider fail-fast。

## Suggested Next

2026-05-27 Step 5 回填：第二條代表 flow `provider-callback-bet-settle-to-mq` 已完成 Step 5，可強化本 project provider connector / callback / bet record MQ claim。Direct evidence 支撐 Nick / `10gt12nc` 參與 callback 寫 MQ、`ConnectBetRecordMqService`、admin-api BetRecord MQ 入庫、currency / pt_day / duplicate boundary；IP whitelist、subAgent rewrite、amount scaling、error code propagation 等 current behavior 多為 `arnold` / 團隊 context，不作 Nick direct claim。

2026-05-27 Step 5 回填：第三條代表 flow `request-bet-record-mq-sync` 已完成 Step 5，可作本 project job-driven bet record sync / late data 補資料 / 跨日 `pt_day` 查重 / MQ path 的 project-level claim 強化 evidence。Nick / `10gt12nc` direct evidence 支撐 BetRecord MQ、job、跨日 `pt_day`、DerPlay 單一錢包日期、currency default / currency 修正；Redis watermark、per-currency current behavior、amount normalization、subAgent 等仍屬 code-backed / 團隊 context，不升級成 Nick direct claim。

`ugsoft-connector-api` 本批三條代表 flow 已全部完成 Step 5。下一步若繼續 connector，應做 `contribution claim consolidation refresh`，把 transfer wallet、callback / MQ、job-driven bet record sync 三條 Step 5 結論整理成 project-level claim，並保留 rolling / final 邊界。本檔仍是 rolling consolidation，不是已刷新後的 final 全量收口。

```text
ugsoft ugsoft-connector-api contribution claim consolidation refresh
```
