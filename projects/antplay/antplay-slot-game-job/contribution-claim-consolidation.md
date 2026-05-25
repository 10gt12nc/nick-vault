# antplay-slot-game-job Contribution Claim Consolidation

日期: 2026-05-20
Refresh 日期: 2026-05-25

## 結論

`antplay-slot-game-job` 可以列為 Nick / `10gt12nc` 真實開發過的 job / event processing repo。五條代表 flows 已完成 Step 5，本次 refresh 後的 project-level 結論是：

- 可放履歷：AntPlay slot job / event processing、Kafka consumer、Quartz job、代理玩家報表 projection / summary、big-win notification、bet record / request log / report 分表與 job config 維護。
- 可面試講：event-driven projection、derived report correctness、reward flow 防重邊界、derived notification privacy / delivery、Redis / DB consistency analysis、schema route / partition table governance。
- 不可誇大：不說主導完整 AntPlay slot platform、完整 Kafka event platform、exactly-once / outbox / replay architecture、完整 settle pool / risk / jackpot owner、完整 reward platform、完整 DB sharding / schema routing framework、完整 BI / report platform 或完整遊戲數學 / RTP 策略。

履歷保守句：

> 參與 AntPlay slot job / event processing 開發維護，處理 Kafka consumer / Quartz job、代理玩家報表聚合、活動累積投注、big-win notification、bet record / request log / report 分表與 job config。

若篇幅較長，可拆成兩句：

> 參與 AntPlay slot job / event processing 開發維護，處理 Kafka `settled_bets` consumer、Quartz report summary、代理玩家報表 projection 與 historical summary / backup / delete。
>
> 參與 big-win notification、活動累積投注 reward flow 以及 bet record / request log / report 分表維護；面試可展開 event correctness、idempotency、schema route、privacy 與 failure recovery 邊界。

## Evidence 等級總表

| 主題 | project-level 判斷 | 履歷用途 | 不能說 |
| --- | --- | --- | --- |
| Kafka job foundation | 真實開發過 + code-backed | 可作 job / event processing 背景 evidence | 不說完整 Kafka platform / outbox / replay owner |
| Proxy user data report projection | 真實開發過 + code-backed | 可作主力履歷與面試 case | 不說完整 BI / report platform 或 exactly-once |
| Big-win notification | 真實開發過 + code-backed | 可作 project-level supporting evidence | 不說完整 push platform / guaranteed delivery / privacy 已完整治理 |
| Activity accumulated bet / voucher | code-backed + Nick merge evidence | 可作 supporting evidence / 面試素材 | 不說 Nick 主開發或完整 reward platform owner |
| Settle pool monitor / darkpool sync | code-backed / analysis-first | 可面試講分析，不作履歷主 claim | 不說 Nick 真實開發或主導 risk / jackpot / darkpool |
| DB partition / report routing | 真實開發過 + code-backed | 可作分表 / report path supporting evidence | 不說完整 DB sharding / `@UseSchema` framework owner |

## Source Scan Record

來源 repo:

```text
/Users/nick/Git/antplay/antplay-slot-game-job
```

遠端狀態:

- local branch: `master`
- local HEAD: `d847357f0a8262c0c91cf9b20989c6296c3d9892`
- local `origin/master`: `d847357f0a8262c0c91cf9b20989c6296c3d9892`
- local vs 本機既有 `origin/master`: `0 / 0`
- source working tree: clean
- 先前 `git fetch --all --prune` 因內網 remote 連線失敗；依 KB 不反覆重試。
- 最新性判斷：未確認最新遠端；本 refresh 依本地 refs / 本地 working tree 保守分析。

本次 refresh 重讀:

- KB: `AGENTS.md`、`senior-owner-playbook/00-operating-rules.md`、`senior-owner-playbook/03-flow-learning-package-template.md`、`senior-owner-playbook/09-ai-prompt-manual.md`
- Project docs: `README.md`、`step1-candidate-flows.md`、`step2-flow-comparison.md`
- Flow docs: 五條代表 flow 的 `flow.md`、`career-interview.md`、`materials/claim-boundary.md`
- Source evidence: Nick / `10gt12nc` author log、`shortlog --all`、source repo branch / HEAD 狀態

重要限制:

- 未做 Level 3 全 repo 逐檔逐行。
- 未確認最新 remote refs。
- 未掃 live DB schema、Kafka runtime topic / group、Quartz DB schedule、production runbook。
- 未把本檔直接回寫 `05 / 08`；若要更新履歷 / 自傳，後續 rolling resume package 已完成；若有新 evidence 才再指定 `05 / 08` refresh。

## 直接 Commit Evidence

Nick / `10gt12nc` 在本地 refs 中約 51 commits，另有 `nick` 1 commit。和本 consolidation 直接相關的 commit 包含：

| Commit | 日期 | 作者 | 主題 | Claim 用法 |
| --- | --- | --- | --- | --- |
| `6846246` ~ `be86b4d` | 2024-11 ~ 2024-12 | `10gt12nc` | Kafka job foundation / config / platform-mock / rollback 嘗試 | job / event processing 背景 evidence |
| `a2bad6f` / `c2cd523` 等 | 2025-02 | `10gt12nc` | `#303` 中大獎通知與金額格式 | big-win notification direct evidence |
| `7945101` / `7aa06ed` | 2025-04 | `10gt12nc` | `#386` 代理用戶數據 Kafka 匯入 DB / report flow | report projection direct evidence |
| `4411d88` / `a9bb983` / `dbb5a66` / `806dc23` | 2025-05 ~ 2025-06 | `10gt12nc` | `#590` currency / log / schedule | report currency / schedule correctness evidence |
| `ef4f8f5` | 2025-08 | `10gt12nc` | `#702` key 重複 | report key correctness evidence |
| `b754dae` | 2025-12 | `10gt12nc` | `db_partition v2` | DB partition / request log / bet record supporting evidence |
| `38b74bd` | 2025-12 | `10gt12nc` | report path fix context | report path repair supporting evidence |
| `6866866` | 2025-12 | `10gt12nc` | `fix ag_report_player` | report fixed table + `agent_id` direct evidence |
| `62fa93f` | 2025-11 | `nick` | merge `feature/accumate_bet` | activity voucher merge evidence；不是細節主開發 evidence |

## Flow-by-flow Refresh

### 1. Proxy user data report projection

Step 5 狀態: 已完成。

Project-level claim:

- 真實開發過 + code-backed。
- 可回填 `antplay-slot-game-job` 主力 claim，作 Kafka report projection / Quartz summary / report key correctness 的直接 evidence。

可說:

- `settled_bets` event 進來後，依 agent / player / day / currency 聚合 bet count、bet amount、win、profit。
- `ReportAgentPlayerJob` 對歷史資料做 summary、backup、delete。
- report projection 是 derived report，不是交易 source of truth；重跑、partial completion、currency key 與 batch update 都要保守處理。
- source code 有基本 Kafka retry / dead-letter-topic evidence，但不能升級成完整 replay / exactly-once。

不可誇大:

- 不說完整 Kafka platform owner。
- 不說完整 BI / report platform owner。
- 不說完整 exactly-once / replay / DLQ 消費治理 owner。
- 不把後續 Arnold / Eliot current fixes 全部算成 Nick direct contribution。

### 2. Activity accumulated bet / voucher

Step 5 狀態: 已完成。

Project-level claim:

- code-backed + Nick merge evidence。
- 可作 reward correctness / Redis + DB 防重分析素材。
- 不建議單獨新增正式履歷 bullet。

可說:

- `settled_bets` event 依活動設定、agent、player、currency、date / period 累積投注。
- Redis 累積投注與發放紀錄搭配 DB voucher count guard，形成 reward flow 防重邊界。
- 面試可講 daily / period overlap、Redis / DB race、voucher duplicate award、idempotency key 與 reconciliation。

不可誇大:

- 不說 Nick 主導此 flow 細節開發。
- 不說完整 reward platform owner。
- 不說已確認完整 idempotency；本 repo 沒有 `BetVoucherService` 實作、table schema、DB unique key 或 transaction boundary。
- 不把每次新 UUID `refId` 說成 deterministic 防重 key。

### 3. Big-win notification

Step 5 狀態: 已完成。

Project-level claim:

- 真實開發過 + code-backed。
- 可作 big-win notification / Kafka derived event supporting evidence。

可說:

- Nick / `10gt12nc` 有 `#303` direct evidence，參與中大獎通知初版與金額格式修正。
- current flow 會判斷 `(bet + voucherBet) * 10` 門檻，組 push user message，包含玩家遮罩名稱、game translation、prize、currency。
- 面試可講 derived notification 不是交易真相、缺翻譯 fallback、producer async failure、privacy、重送去重與 outbox 改善。

不可誇大:

- 不說完整 push platform owner。
- 不說 notification guaranteed delivery / exactly-once。
- 不說下游已做 `_id` dedupe 或 `fullPlayerName` filtering；Step 5 未見 evidence。
- 不說完整 jackpot / bonus owner。

### 4. Settle pool monitor / darkpool sync

Step 5 狀態: 已完成。

Project-level claim:

- code-backed / analysis-first。
- 技術含量高，可作面試分析素材，但不回填正式履歷主 claim。

可說:

- 能分析 `settled_bets` -> pool grouping -> `settled_pool` increment -> Redis reset snapshot -> alert 的 projection consistency。
- 可講 reset sync partial failure、Redis / DB consistency、busy guard、unit boundary、reconciliation 與 monitoring 改善。

不可誇大:

- 不說 Nick 真實開發這條 flow。
- 不說主導 settle pool / dark pool / risk / jackpot / player control platform。
- 不寫進 `05 / 08` 作正式履歷 bullet，除非之後補到 Nick direct evidence 或本人確認 + ticket / MR。

### 5. DB partition / report routing

Step 5 狀態: 已完成。

Project-level claim:

- 真實開發過 + code-backed。
- 可作 bet record / request log / report 分表與 report path repair supporting evidence。

可說:

- `db_partition v2` 觸及 bet record repository、request log、Kafka consumer 與 request log service。
- `fix ag_report_player` 將 report path 從 per-agent table name 修向固定 `ag_report_player` / `ag_report_player_bak`，並補 `agent_id` 條件。
- 面試可講 high-traffic table governance、`agentId` route key、SQL filter、ThreadLocal restore、metadata cache、migration / backfill 風險。

不可誇大:

- 不說主導完整 DB sharding architecture。
- 不說主導完整 `@UseSchema` framework；current framework 主要是 Eliot / Arnold 後續建立與調整。
- 不說 G3 routing 已完整落地；current evidence 只有 logical enum / aspect branch，未看到完整 data source mapping。
- 不說完整 migration / backfill / DDL / index owner。

### 6. Kafka job foundation

狀態: 背景 evidence，不作獨立代表 flow。

可說:

- 早期 Kafka job / config / producer / platform-mock notification / rollback 嘗試，能支撐「參與 job repo event processing foundation」。

不可誇大:

- 不說這已形成完整 production-grade Kafka platform。
- 不說有完整 outbox、exactly-once、replay 或 DLQ governance。

## Resume Claim

可放履歷:

- 參與 AntPlay slot job / event processing 開發維護，處理 Kafka consumer / Quartz job、代理玩家報表聚合、活動累積投注、big-win notification、bet record / request log / report 分表與 job config。
- 參與代理玩家報表 projection 維護，處理 `settled_bets` event 匯入、agent / player / day / currency 聚合、daily summary、backup / delete 與 key collision / currency 維度修正。
- 參與 big-win notification 與 report path / partition 維護，處理 derived notification、玩家名稱遮罩、遊戲名稱翻譯、金額格式、push user topic message 與 `ag_report_player` 查詢修正。

可面試講:

- Kafka `settled_bets` event 如何分流成 report projection、activity accumulated bet、big-win notification、settle pool monitor。
- 報表 projection 的 key design：agent / player / day / currency，與 key collision / missing currency 的風險。
- Quartz summary / backup / delete 的 partial failure、重跑、idempotency 與 repair runbook。
- Reward flow 的 Redis accumulate、DB awarded count、duplicate voucher 與 deterministic idempotency key 取捨。
- Big-win notification 的 derived event、privacy、producer failure、duplicate notification 與 outbox / deterministic key 改善。
- 分表與 schema route的 route key、SQL filter、metadata cache、ThreadLocal restore、G3 / DDL / migration 邊界。
- Settle pool monitor 可作 code-backed analysis-first 題材，講 Redis / DB consistency 與 reset sync，不包裝成 Nick direct owner。

不可誇大:

- 不說主導完整 AntPlay slot platform。
- 不說完整 Kafka event platform、exactly-once、outbox 或 full replay architecture。
- 不說完整 settle pool / risk / jackpot / player control owner。
- 不說完整 reward platform / voucher platform owner。
- 不說完整 DB sharding / schema routing framework owner。
- 不說完整 BI / report platform owner。
- 不說完整遊戲數學 / RTP 策略 owner。
- 不寫量化改善、正式 incident owner、全權 owner，除非後續補 production issue / ticket / metric。

## 05 / 08 回填口徑

本 refresh 可以支撐後續 `rolling resume package` 或 `05 / 08` refresh，但本輪不直接改 `05 / 08`。

建議口徑：

> 參與 AntPlay slot job / event processing 開發維護，處理 Kafka consumer / Quartz job、代理玩家報表 projection / summary、big-win notification、活動累積投注與 bet record / request log / report 分表維護；可從 event correctness、idempotency、schema route、derived notification 與 Redis / DB consistency 角度說明 production flow 的風險與改善方向。

更短版：

> 參與 AntPlay slot job / event processing 維護，涵蓋 Kafka / Quartz、代理玩家報表 projection、big-win notification、活動累積投注與分表 / report path 修正。

## Refresh 後狀態

- Career Track: refreshed / 2026-05-25。
- Flow Track: Step 1 / Step 2 已完成；五條代表 flows 均已 Step 5。
- `05 / 08`: 可後續回填，但本輪未直接修改。
- domain / system map: 本輪不建立。若 Nick 之後要求 AntPlay 大地圖，再檢查 / 建立 domain-level architecture / integration map。

歷史下一步紀錄:

```text
rolling resume package
```
