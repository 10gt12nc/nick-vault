# Evidence - transfer-wallet-in-out-query

## Source Repo

```text
/Users/nick/Git/ugsoft/ugsoft-connector-api
```

本輪只讀 source repo，不改公司 code。

## Remote / Local State

- Step 3 已執行 `git fetch --all --prune`，成功。
- Step 4 開始前再次執行 `git fetch --all --prune`，成功。
- Step 5 開始前再次執行 `git fetch --all --prune`，成功。
- local branch：`Nick_Test`
- local HEAD：`c2cab730c0cd6ead6d92a038ef56f97987577059`
- remote HEAD：`refs/remotes/origin/develop`
- `origin/master`：`4bd2195e1e574978f11a1d4b5e744792f16ecad0`
- `origin/develop`：`079aa6603b50db3c185e383295ca5966bbe272fb`
- local vs `origin/master` ahead / behind：`0 / 61`
- local vs `origin/develop` ahead / behind：`190 / 0`
- source repo 工作樹不乾淨：`.DS_Store`、test、docs 與 `src/test/java/com/ps/domain/` 有既有 modified / untracked。本文只採 git history、remote object 與 source path，不採本機髒檔作正式 evidence。
- 本輪主現況以 `origin/master:path` 讀取，避免 local branch 落後 `origin/master` 導致誤判。

## Step 5 補充掃描

- 任務：`ugsoft ugsoft-connector-api transfer-wallet-in-out-query Step 5`。
- 掃描等級：Level 2+ claim gate；沿用 Step 3 / Step 4 code path，補跑 path-specific history、重要 diff、current blame 與 branch contains。
- 已重讀：本 flow `flow.md`、`career-interview.md`、`materials/evidence.md`、`materials/claim-boundary.md`、`ugsoft-connector-api/contribution-claim-consolidation.md`。
- 已補查：AntPlay / DerPlay adapter transfer commits、DerPlay get-single-transaction commits、transfer transaction / lookup / compensation commits、`TransferFacade` / `TransferService` current blame。
- Step 5 不改公司 repo，不採 source repo dirty files。
- Step 5 不直接更新 `05 / 08`；只回填 flow-level claim boundary 與 project-level索引。

## Step 4 補充掃描

- 任務：`ugsoft ugsoft-connector-api transfer-wallet-in-out-query Step 4`。
- 掃描等級：Level 2 面試轉換；沿用 Step 3 code path 與 evidence，補確認 remote refs / local dirty state。
- 已重讀：`AGENTS.md`、`00-operating-rules.md`、`09-ai-prompt-manual.md`、`03-flow-learning-package-template.md`、本 flow `flow.md`、`career-interview.md`、`materials/interview.md`、`materials/claim-boundary.md`、本 evidence。
- Step 4 未新增公司 code 結論；只把 Step 3 已確認 flow 轉成面試講法、追問、陷阱與 claim boundary。
- Step 4 不更新正式履歷 / 自傳；下一步 Step 5 才做單條 flow claim gate。

## 已重讀 Vault 文件

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`
- `projects/ugsoft/README.md`
- `projects/ugsoft/ugsoft-connector-api/README.md`
- `projects/ugsoft/ugsoft-connector-api/step1-candidate-flows.md`
- `projects/ugsoft/ugsoft-connector-api/step2-flow-comparison.md`
- `projects/ugsoft/ugsoft-connector-api/contribution-claim-consolidation.md`

## Code Path Evidence

### Controller

- `src/main/java/com/ps/domain/connector/controller/ConnectClientController.java`
- `origin/master` lines 294-328：`POST /transfer/transfer-in`
- `origin/master` lines 335-369：`POST /transfer/transfer-out`
- `origin/master` lines 376-410：`POST /transfer/transfer-out-all`
- `origin/master` lines 418+：`POST /transfer/get-single-transaction`

### Service

- `src/main/java/com/ps/domain/connector/service/ConnectClientService.java`
- `origin/master` lines 299-342：`transferIn`
- `origin/master` lines 350-397：`transferOut`
- `origin/master` lines 406-452：`transferOutAll`
- `origin/master` lines 463-505：`transferGetSingleTransaction`
- `origin/master` lines 245-254：loginV3 success 時維護 provider position。
- `ConnectClientService#innerTransferOther`：provider switch 時 best-effort 先轉出舊 provider、再轉入新 provider；本輪只作 upstream context，不作本 flow 的 Nick direct claim。

### Transaction Facade

- `src/main/java/com/ps/domain/api/game/facade/TransferFacade.java`
- `origin/master` lines 314-383：`beforeTransaction`
- `origin/master` lines 386-423：`afterTransaction`
- `origin/master` lines 104-143：insert success transaction，amount / balance 轉 cents。
- `origin/master` lines 283-295：insert lookup data。

### DB Access

- `src/main/java/com/ps/domain/manage/service/TransferService.java`
- `origin/master` lines 41-50：Redis 3 秒 duplicate guard。
- `origin/master` lines 53-63：查 `pt_transfer_player_wallet_transaction`。
- `origin/master` lines 67-123：insert `pt_transfer_player_wallet_transaction`。
- `origin/master` lines 126-147：insert `ag_transfer_order_lookup`。
- `origin/master` lines 152-172：查 lookup 與 transaction data。

### Provider Adapter

- `src/main/java/com/ps/domain/connector/adapter/AdapterClient.java`
- `origin/master` lines 153-176：transferIn dispatch / response normalization。
- `origin/master` lines 181-202：transferOut dispatch / response normalization。
- `origin/master` lines 207-228：transferOutAll dispatch / response normalization。
- `origin/master` lines 257-280：getSingleTransaction dispatch / response normalization。

- `src/main/java/com/ps/domain/connector/service/ConnectAntplayAdapter.java`
- `origin/master` lines 416-456：AntPlay transferIn。
- `origin/master` lines 467-504：AntPlay transferOut。
- `origin/master` lines 515+：AntPlay transferOutAll / getSingleTransaction 續段已抽讀。

- `src/main/java/com/ps/domain/connector/service/ConnectDerPlayAdapter.java`
- `origin/master` lines 147-213：DerPlay transferIn。
- `origin/master` lines 217-282：DerPlay transferOut。
- `origin/master` lines 286-334：DerPlay transferOutAll。
- `origin/master` lines 338-365：DerPlay getSingleTransaction。

### Constants / Tables

- `src/main/java/com/ps/domain/constant/ShardingTableName.java`
  - `AG_TRANSFER_ORDER_LOOKUP = ag_transfer_order_lookup`
  - `PT_TRANSFER_PLAYER_WALLET_TRANSACTION = pt_transfer_player_wallet_transaction`
- `src/main/java/com/ps/domain/connector/constant/TransferConst.java`
  - `transfer-in`
  - `transfer-out`
  - `transfer-out-all`
  - `transfer-out-inner`
  - `transfer-in-inner`

## Git Evidence

Nick / `10gt12nc` direct commits relevant to provider adapter transfer:

- `575996d` / 2026-03-17 / `10gt12nc` / `feat: adapter 串AntPlay Transfer API 01版`
  - touched `AdapterClient.java`, `ConnectAdapter.java`, `ConnectAntplayAdapter.java`, `ConnectClientService.java`, `ConnectDerplayAdapter.java`
- `80828c1` / 2026-03-17 / `10gt12nc` / `feat: adapter 串AntPlay Transfer API 02#`
  - touched `ConnectAntplayAdapter.java`, config / application yml files
- `04473d5` / 2026-03-17 / `10gt12nc` / `feat: adapter 串AntPlay Transfer bet_record`
- `48ee13b` / 2026-03-19 / `10gt12nc` / `feat: adapter 串DerPlay`
- `afff4b1` / 2026-03-19 / `10gt12nc` / `feat: adapter 串DerPlay t 001`
- `04e2c84` / 2026-03-19 / `10gt12nc` / `feat: adapter 串DerPlay transfer In、Out、Balance、OutAll`
  - touched `ConnectDerPlayAdapter.java`
- `1a1c0b1` / 2026-03-19 / `10gt12nc` / `feat: adapter 串DerPlay getSingleTransaction`
  - touched `ConnectDerPlayAdapter.java`
- `92b6a1f` / 2026-03-20 / `10gt12nc` / `feat: adapter 串DerPlay getSingleTransaction 02`
  - touched `ConnectDerPlayAdapter.java`

Supervisor / team context commits, not Nick direct evidence:

- `98ffb6a` / 2026-03-20 / `arnold` / `feat: 新增beforetransaction 和afterTransaction`
- `5a2a204` / 2026-03-20 / `arnold` / `fix: add insert order lookup`
- `61a3ac6` / 2026-03-30 / `arnold` / `fix: get single transaction , derplay 暫時用他們的id`
- `0b1c34e` / 2026-04-28 / `arnold` / `fix: transfer 冪等回放回傳實際 balanceBefore/balanceAfter`
- `85d6f29` / 2026-05-20 / `arnold` / `fix: transferGetSingleTransaction 二級代理時用 replaceAgentId 查 lookup`
- `d7a1717` / 2026-05-20 / `arnold` / `fix: transfer/bet_record 全鏈路把 subAgentId 傳給下游 Antplay`

Additional Step 5 Nick / `10gt12nc` evidence:

- `e236a59` / 2026-03-11 / `10gt12nc` / `feat: ugAdapter transfer～`
  - touched early `ugAdapter` transfer response / adapter paths and later connector adapter path.
- `aaddfdc` / 2025-11-27 / `10gt12nc` / `todo : ag_transfer_order_lookup`
  - touched transfer order lookup service / entity / table-name path; older path, supports lookup concept evidence but not final `TransferFacade` ownership.
- `3531f42` / 2025-11-27 / `10gt12nc` / `todo : pt_transfer_player_wallet_transaction`
  - touched transfer transaction table creation job path; older path, supports transaction table / partition context.
- `eb2573a` / 2025-11-27 / `10gt12nc` / `todo : pt_transfer_request_log`
  - touched request log table creation job path; older path, supports request log table context.
- `99c63ff` / 2025-11-27 / `10gt12nc` / `todo : ag_transfer_player_wallet`
  - touched transfer player wallet table / service path; older path.
- `54078fe` / `a2b2af5` / `31d7a46` / 2025-12-30~31 / `10gt12nc` / transfer wallet deadlock compensation
  - touched `TransferBalanceService`, `CompensationService`, `GameFacade`, `GameFlowFacade`, `BetRecordManageService`; supports transfer wallet compensation / wallet update maintenance context.

Step 5 current blame notes:

- `ConnectAntplayAdapter` transfer in / out / out-all current lines still have substantial `10gt12nc` blame for request mapping, sign payload, request body, response parse call. Later HTTP execute wrapper changes are `arnold`.
- `ConnectDerPlayAdapter` transfer in / out / out-all current lines have substantial `10gt12nc` blame for query balance before / after, create order, transfer chip, query order and response mapping. Later provider setting / execute wrapper / currency parameters are `arnold`.
- `ConnectDerPlayAdapter#getSingleTransaction` has `10gt12nc` early implementation and current residual lines, but final production signature and provider transaction id mapping have important `arnold` changes. Treat as `Nick direct adapter work + team finalization` rather than pure Nick owner.
- `TransferFacade#beforeTransaction / afterTransaction` current blame is dominated by `arnold` (`98ffb6a`, `5a2a204`, `7b47601`, `61a3ac6`, `cf5a1cf`); do not mark as Nick direct implementation.
- `TransferService` / older `TransferBalanceService` transaction / lookup SQL has mixed history: `10gt12nc` touched insert / query / table context in 2025, while current class / later provider columns include `arnold` and older Derek context. Treat as supporting evidence, not sole ownership.

Branch contains check:

- `575996d`, `04e2c84`, `1a1c0b1` are contained in local `Nick_Test`, local `master`, `origin/master`, `origin/feat-transfer-inner-retry`, and `origin/codex/connector-api-harness-guide`.
- This supports that adapter commits are not just orphan local experiments, but still does not prove production deployment branch.

## Blame Notes

- `ConnectClientService#transferIn` 的 signature / sign / beforeTransaction / adapter call / afterTransaction 多為 `arnold` commits。
- `TransferFacade#beforeTransaction / afterTransaction` 多為 `arnold` commits。
- `ConnectAntplayAdapter` 與 `ConnectDerPlayAdapter` transfer adapter path 有明確 `10gt12nc` direct commits。

## 已確認

- Flow entrypoints 存在於 `/connect/client/transfer/*`。
- `transferIn / transferOut / transferOutAll` 都會走 `TransferFacade#beforeTransaction` 與 `afterTransaction`。
- 短時間重複提交 guard 是 Redis hash + 3 秒 TTL。
- 成功 provider response 後才寫 `pt_transfer_player_wallet_transaction` 與 `ag_transfer_order_lookup`。
- `get-single-transaction` 先查 lookup / transaction，再用 provider transaction id 查 provider。
- 最新 `origin/master` 已把 subAgentId 傳給 transfer provider adapter 與 query path。

## 合理推論

- `pt_transfer_player_wallet_transaction` 是 connector 本地 transfer transaction record，不是完整 wallet ledger。
- `ag_transfer_order_lookup` 是查單索引，讓 merchant reference id 可以找到 provider transaction id。
- Redis 3 秒 guard 是 frequency guard，不足以當完整 idempotency source of truth。

## 待確認

- production 實際部署 branch 是 `origin/master`、`origin/develop` 或其他 release branch。
- DB DDL / unique constraint 是否對 `agent_id + account + transfer_reference_id` 有更強保護。
- provider 成功但本地 DB insert / lookup insert 失敗時，是否已有人工補償 SOP 或 job。
- 是否有正式 ticket / MR / incident 可證明 Nick 參與 transaction facade / recovery。

## Step 5 claim 判斷

| Claim | 判斷 | 理由 |
| --- | --- | --- |
| 參與 AntPlay / DerPlay transfer adapter 串接 | 可放履歷 / 真實開發過 + code-backed | 多筆 `10gt12nc` commits 與 current blame 命中 adapter transfer path |
| 參與 DerPlay get-single-transaction / provider query order | 可面試講，可作 project-level supporting evidence | 有 `10gt12nc` commits，但 final provider transaction id mapping 有 `arnold` 後續修正 |
| 參與 transfer transaction / lookup / request log table path | 可作 supporting evidence | 2025 有 `10gt12nc` transfer table / lookup / request log path commits；final connector `TransferFacade` 仍非 Nick 主體 |
| 參與 transfer wallet compensation / deadlock 補償 | 可作 project-level supporting evidence | `54078fe` / `a2b2af5` / `31d7a46` direct commits，與本 flow 同屬 transfer wallet domain，但不是 connector transfer API 全流程 |
| 主導 `beforeTransaction / afterTransaction`、Redis guard、lookup replay | 不可寫 | current blame 與重要 commits 為 `arnold` / team context |
| 完整 transfer wallet / ledger / reconciliation owner | 不可寫 | 缺 production branch、ticket、incident、reconciliation / outbox evidence |
