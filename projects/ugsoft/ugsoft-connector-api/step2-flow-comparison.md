# ugsoft-connector-api Step 2 - Flow Comparison

## 閱讀定位

- Project: `ugsoft-connector-api`
- Step: Step 2 / candidate flow comparison
- 掃描等級: Level 1.5 flow comparison；不是單條 flow Level 2 深掃，也不是 Level 3 逐 commit final。
- 目標: 比較 Step 1 篩出的候選 production flows，決定本批最值得進 Step 3 的代表 flow。
- 本輪不做: 不建立 `flows/{flow}/`、不寫 Step 3 主報告、不直接更新 `05 / 08 / 04 / 17`。
- 證據層級: `真實開發過 + code-backed`、`code-backed / 主管或團隊 context`、`分析素材 / 待深掃` 混合；本檔只做排序與邊界，不把 candidate 直接升級成完整履歷 claim。Nick 已確認 `arnold` 是主管帳號，不是 Nick direct evidence。

## 已重讀與既有文件狀態

已重讀:

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `projects/ugsoft/README.md`
- `projects/ugsoft/ugsoft-connector-api/README.md`
- `projects/ugsoft/ugsoft-connector-api/contribution-claim-consolidation.md`
- `projects/ugsoft/ugsoft-connector-api/step1-candidate-flows.md`
- source repo: `/Users/nick/Git/ugsoft/ugsoft-connector-api`

既有文件判斷:

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `contribution-claim-consolidation.md` | 可沿用，rolling | Career Track 已能保守支撐 provider connector / gateway claim；但不代表 flow 完整。 |
| `step1-candidate-flows.md` | 可沿用 | 已整理 module map 與 6 條 candidate flows。 |
| `step2-flow-comparison.md` | 可沿用，已同步進度 | 用來決定本批代表 flow 與下一步。 |
| `flows/` | 已建立部分代表 flow | `transfer-wallet-in-out-query` 已完成 Step 5；`provider-callback-bet-settle-to-mq` 已完成 Step 5；`request-bet-record-mq-sync` 已完成 Step 4。 |

## Source Repo 狀態

來源 repo:

```text
/Users/nick/Git/ugsoft/ugsoft-connector-api
```

遠端 / 本機狀態:

- 已執行 `git fetch --all --prune`；第一次受 sandbox 權限限制失敗後，經 approval 成功更新 remote refs。
- local branch: `Nick_Test`
- local HEAD: `c2cab730c0cd6ead6d92a038ef56f97987577059`
- remote HEAD: `origin/develop`
- `origin/develop`: `079aa6603b50db3c185e383295ca5966bbe272fb`
- `origin/master`: `4bd2195e1e574978f11a1d4b5e744792f16ecad0`
- local vs `origin/master`: ahead / behind = `0 / 61`
- local vs `origin/develop`: ahead / behind = `190 / 0`
- source repo 工作樹不乾淨：有 `.DS_Store`、test、docs 等既有 local changes / untracked files。本輪只讀 remote objects 與 git history，不採 source repo 髒工作樹作正式 evidence，也不改 source repo。

本輪補讀範圍:

- `git log --all --author='10gt12nc|Nick|nick'`
- `git log --all -- src/main/java/com/ps/domain/connector`
- `git log --all -- src/main/java/com/ps/domain/api/game/facade/TransferFacade.java`
- `git log --all -- src/main/java/com/ps/domain/manage/service/TransferService.java`
- `git log --all -- src/main/java/com/ps/quartz/main/service/ConnectBetRecordSyncService.java`
- `origin/master:src/main/java/com/ps/domain/connector/service/ConnectClientService.java`
- `origin/master:src/main/java/com/ps/domain/api/game/facade/TransferFacade.java`
- `origin/master:src/main/java/com/ps/domain/connector/service/ConnectCallbackService.java`
- `origin/master:src/main/java/com/ps/quartz/main/service/ConnectBetRecordSyncService.java`
- `origin/master:src/main/java/com/ps/domain/connector/service/ConnectAdapterExecute.java`
- `origin/master:src/main/java/com/ps/domain/connector/service/ConnectorCircuitBreaker.java`
- `origin/master:src/main/java/com/ps/datasource/SchemaRouteAspect.java`

未完成:

- 未逐檔逐行 Level 3。
- 未建立單條 flow 的 `flow.md / career-interview.md / materials/*`。
- Nick 已確認 `arnold` 是主管帳號；本檔將 `arnold` commits 視為主管 / 團隊 context，不直接當 Nick direct evidence。
- 未驗證 production 目前到底跑 `origin/master` 或 `origin/develop`；本輪以 `origin/master` 的 connector runtime code 與 path history 做保守比較。

## Candidate Flow 比較表

| Rank | Flow | Evidence 強度 | 技術價值 | Failure / owner 追問價值 | 履歷 / 面試價值 | 邊界 | Step 2 決策 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | `transfer-wallet-in-out-query` | 高。Nick / `10gt12nc` 有 AntPlay / DerPlay transfer API、single transaction、provider adapter direct commits；transaction facade / replay / lookup 多為 code-backed / 主管或團隊 context。 | 很高。簽名、agent / subAgent、Redis duplicate guard、provider adapter、request log、wallet transaction、order lookup、查單集中在同一條 money-adjacent flow。 | 很高。可追問重複提交、provider 成功但 DB 寫入失敗、query order 不一致、transfer out / in failure window。 | 很高。最適合補 UGSoft 非 iwin 的 Senior Backend 主力 case。 | 不能寫完整 wallet / ledger / reconciliation owner；`beforeTransaction / afterTransaction` 若只見 `arnold` evidence，須標為主管 / 團隊 context，不作 Nick direct claim。 | 已完成 Step 5；可回填 project-level provider connector / transfer wallet evidence。 |
| 2 | `provider-callback-bet-settle-to-mq` | 高。Nick / `10gt12nc` 有 callback 寫 MQ、AntPlay / DerPlay bet record MQ、pt_day / currency 類 commits；部分最新 amount / subAgent 修正屬主管 / 團隊 context。 | 很高。provider callback、驗簽、wallet type、bet-settle、MQ eventual consistency、payload normalize。 | 高。可追問 callback 重送、MQ duplicate / missing、bet id 去重、BigDecimal / cents conversion、late data。 | 很高。可和 AntPlay / iwin bet-settle case 互相補強。 | 不能寫 exactly-once / outbox owner；IP whitelist、subAgent、amount scaling 等 current behavior 多為團隊 context。 | 已完成 Step 5；可回填 project-level provider connector / callback / MQ evidence。 |
| 3 | `request-bet-record-mq-sync` | 中高。Nick / `10gt12nc` 有 MQ / pt_day / currency / job 修正 evidence；Step 3 已確認 sync service / watermark / 查重 / MQ path，Step 4 已轉正式面試 case。 | 高。Quartz pull provider bet record、時間窗、水位、跨日查重、批次 existing keys、MQ 補資料。 | 高。可追問水位不前進、跨日分區、重播、provider 拉取失敗、資料晚到、MQ publish failure 後水位誤前進。 | 高。可補 asynchronous data / report projection 類廣度。 | 容易和 callback -> MQ 重疊；要避免包成完整 reconciliation。 | Step 4 已完成；下一步可做 Step 5。 |
| 4 | `provider-client-login-launch-game` | 中高。Nick / `10gt12nc` 有 AntPlay login / info direct commits；login v3、subAgent、lang allow list、provider switch 多為 code-backed / 主管或團隊 context。 | 中高。gateway contract、sign / currency / game route、provider adapter、transfer wallet provider position。 | 中。可追問 provider switch、inner transfer、gameUrl / html 差異，但 money state 較間接。 | 中高。適合作為 connector gateway supporting case。 | 不宜當第一條主力，因核心風險比 transfer / callback 弱。 | 可選第四順位。 |
| 5 | `provider-circuit-breaker-fast-fail` | 中。code-backed 存在 `ConnectAdapterExecute` / `ConnectorCircuitBreaker` / provider-level breaker；Nick direct ownership 未確認。 | 高。provider HTTP wrapper、request / response log、elapsed time、Resilience4j fast-fail。 | 中高。可追問熔斷打開後的 user experience、retry / fallback、不能解決的 consistency 問題。 | 中。Platform / reliability JD 可加分。 | 目前 evidence 不足以放主履歷 bullet。 | 可選 supporting，不列本批必做。 |
| 6 | `schema-route-partition-transfer-record` | 中。schema route / partition code-backed，Nick direct ownership 混合；部分 high-volume data commits 在其他 path。 | 中高。`@UseSchema`、agent / table route、read-only route、partition table。 | 中。可追問跨 schema 查詢、nested schema route、table existence、讀寫分離。 | 中。適合作為 transfer / MQ flow 的支撐，不宜獨立第一條。 | 抽成獨立 flow 會偏 infrastructure；應先嵌入 transfer / MQ。 | 支撐材料，不列本批必做。 |

## 本批代表 Flows

本批不平均掃 6 條；先用 3 條代表 flow 補 UGSoft 的可面試 production case。

1. `transfer-wallet-in-out-query`
   - 理由: money-adjacent transaction correctness 最集中，且有 Nick / `10gt12nc` provider adapter transfer direct commits。
   - 產出目標: Step 3 後應能講 merchant transfer in/out/out-all/query 的正常流程、idempotency、request log、transaction record、provider query order 與 failure window。
2. `provider-callback-bet-settle-to-mq`
   - 理由: callback + bet-settle + MQ eventual consistency，是 provider integration 很核心的第二條主線。
   - 產出目標: Step 3 後應能講 callback 驗簽、wallet type boundary、MQ duplicate / late / missing data、payload normalization。
3. `request-bet-record-mq-sync`
   - 理由: 補 job / async sync / late data 的維度，和前兩條組成 connector runtime 的完整面試面。
   - 產出目標: Step 3 後應能講時間窗、水位、跨日查重、批次 existing keys、provider 拉取失敗不前進水位。

可選第四條:

- `provider-client-login-launch-game`
  - 用於補 gateway API / provider route / game launch 視角。
  - 若時間有限，先把它當 transfer flow 的上游 context，不獨立開 flow。

暫不作獨立第一批:

- `provider-circuit-breaker-fast-fail`: Platform / reliability 很有價值，但目前 direct ownership 還不夠清楚，先作 supporting evidence。
- `schema-route-partition-transfer-record`: 更適合嵌入 transfer / MQ flow，不先獨立。

## Step 3 / Step 4 首選

首選 Step 3 / Step 4 / Step 5 已於 2026-05-27 完成:

```text
ugsoft ugsoft-connector-api transfer-wallet-in-out-query Step 3
```

為什麼現在做它:

- 它是 Step 1 / Step 2 都排第一的 production flow。
- 它同時有 provider adapter direct evidence 與 transaction / idempotency / lookup code-backed 深挖空間。
- 它能補 UGSoft 作為非 iwin 的 Senior Backend case，而不是只停在 project-level contribution consolidation。

Step 3 必須補清楚:

- 商戶端 transfer in / out / out-all / get-single-transaction 的入口與參數。
- sign / signTime / agent / subAgent / wallet type / provider route。
- `TransferFacade#beforeTransaction`、Redis duplicate guard、existing transaction replay。
- provider adapter transfer in / out / query order。
- `TransferFacade#afterTransaction` 如何寫 wallet transaction / order lookup。
- failure window: provider 成功但 DB 寫入失敗、DB 有交易但 provider query 不一致、Redis guard 過短、provider timeout / duplicate request。
- claim boundary: Nick direct evidence 限於 provider adapter transfer；transaction facade / replay / subAgent 若只找到 `arnold` evidence，標成 code-backed / 主管或團隊 context，不作 Nick direct claim。

後續若繼續同 project Flow Track，第三順位已完成 Step 4；下一步做 claim gate：

```text
ugsoft ugsoft-connector-api request-bet-record-mq-sync Step 5
```

## Relationship Check

- `projects/ugsoft/ugsoft-connector-api/README.md`: 已同步第一條 flow Step 5 完成、第二條 flow Step 5 完成、第三條 flow Step 4 完成，下一步是第三條 `request-bet-record-mq-sync Step 5`。
- `projects/ugsoft/README.md`: 已同步 connector Flow Track 狀態。
- `projects/source-repo-inventory.md`: 已同步 UGSoft 整理狀態。
- `projects/source-repo-flow-audit.md`: 已同步 connector 第一條 flow Step 5 完成、第二條 flow Step 5 完成。
- `senior-owner-playbook/06-todo.md`: 已同步目前待辦，下一步改為第三順位 Step 3。
- `contribution-claim-consolidation.md`: 已同步 Flow Track 狀態與 Suggested Next；不擴張履歷 claim。
- `05 / 08 / 04 / 17`: 本輪不更新，因 Step 2 只做 candidate ranking，沒有新增 final project-level claim 或可直接投遞內容。
