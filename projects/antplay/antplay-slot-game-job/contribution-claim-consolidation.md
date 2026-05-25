# antplay-slot-game-job Contribution Claim Consolidation

日期: 2026-05-20

2026-05-25 補充: `proxy-user-data-report-projection Step 5` 已完成。該 flow 可作 project-level Kafka report projection / Quartz summary / report key correctness 的直接 evidence；但 `ReportAgentPlayerRepositoryInternal` 的 2026 batch padding / safety 修正屬後續他人 context，Kafka replay / DLQ 消費治理與 DB unique key 仍未確認，不能升級成完整 Kafka / BI platform owner。

2026-05-25 補充: `activity-accumulated-bet-voucher Step 5` 已完成。該 flow 可作 activity accumulated bet / voucher reward supporting evidence 與 reward correctness 面試素材；但 `BetVoucherService` implementation、DB unique key、transaction boundary 不在本 repo，且 current implementation 主要是 Gill / Arnold / Eliot。Nick 的 `62fa93f` 屬 merge evidence，不足以把此 flow 單獨升級成正式履歷主 claim。

2026-05-25 補充: `big-win-notification Step 5` 已完成。該 flow 有 `#303` direct evidence，可支撐 Nick 參與中大獎通知初版與金額格式修正；current behavior 後續由 Gill / Arnold / Eliot 修正玩家遮罩、currency / translation、`totalWin` 判斷與 id collection。Step 5 已補查 `_id`、`BetIdPersistence` 與 `antplay-push` 下游 bridge：目前不能證明通知去重或 `fullPlayerName` 已被過濾，因此只回填 project-level supporting evidence，不單獨更新 `05 / 08`。

2026-05-25 補充: `settle-pool-monitor-darkpool-sync Step 4` 已完成。該 flow 技術價值高，可作 Kafka settlement projection / Redis DB consistency / dark pool reset sync 的 analysis-first 正式面試素材；但本輪 path-specific log / blame 顯示 current implementation 主要是 Arnold / Eliot，未找到 Nick / `10gt12nc` direct evidence，因此不回填正式履歷主 claim，也不單獨更新 `05 / 08`。

## 結論

`antplay-slot-game-job` 可以列為 Nick / `10gt12nc` 真實開發過的 job / event processing repo。Direct commits 觸及 Kafka consumer、Quartz job、代理玩家報表聚合、活動累積投注、big-win notification、db partition / job config 與後續 report 修正。

履歷可以保守寫:

> 參與 AntPlay slot job / event processing 開發維護，處理 Kafka consumer / Quartz job、代理玩家報表聚合、活動累積投注、big-win notification、bet record / report 分表與 job config。

不要寫:

- 主導完整 AntPlay slot platform。
- 主導完整 Kafka event platform、exactly-once、outbox 或 replay architecture。
- 主導完整 settle pool / risk / jackpot owner。
- 主導完整 BI / report platform 或完整遊戲數學 / RTP 策略。
- 量化改善或完整 incident owner，除非後續補 production issue / metric。

## Evidence Summary

| 類別 | 判斷 | Evidence |
| --- | --- | --- |
| 直接開發 | 真實開發過 | `git log --all --author='10gt12nc|Nick|nick'` 有 direct commits；`shortlog --all` 顯示 `10gt12nc` 約 51 筆、`nick` 約 1 筆 |
| Kafka job foundation | 真實開發過 + code-backed | `kafka JOB` branch commits 涉及 `KafkaConsumerService`、`KafkaProducerService`、application config、rollback / platform-mock notification 嘗試 |
| 代理玩家資料 | 真實開發過 + code-backed | `#386` 系列新增 `ProxyUserDataConsumerService`、`ReportAgentPlayerService`、repository / entity、`ReportAgentPlayerJob` |
| 報表 currency / key 修正 | 真實開發過 + code-backed | `#590` ReportAgentPlayer 拆 currency、每天一次；`#702` key 重複；`fix ag_report_player` |
| big-win notification | 真實開發過 + code-backed / Step 5 已完成 | `#303` 新增 `BigWinConsumerService`、game / message cache、push user topic，後續小數格式修正；current behavior 有多人後續修改；下游未見 notification dedupe / privacy filtering |
| activity accumulate bet | code-backed / Step 5 已收口 supporting evidence | `62fa93f` merge by `nick`，source 有 `ActivityAccumateBetConsumerService`；可面試講 reward correctness，但不單獨放正式履歷 |
| settle pool / risk | 專案存在 / code-backed / Step 4 已完成，Nick claim 保守 | source 有 settle pool consumer / processor；Step 4 已確認 current implementation 主要 Arnold / Eliot，未找到 Nick direct evidence，不作 Nick 完整 owner claim |
| final 全量 flow | 待補 | 尚未建立 Step 1 / Step 2 與 flow packages；本檔是 rolling consolidation |

## Source Scan Record

來源 repo:

```text
/Users/nick/Git/antplay/antplay-slot-game-job
```

遠端狀態:

- 已嘗試執行 `git fetch --all --prune`，但 remote 為內網 Git，當前連線失敗；remote refs 未能更新。
- local branch: `master`
- local HEAD: `d847357f0a8262c0c91cf9b20989c6296c3d9892`
- remote HEAD: `origin/imgtest`
- 本機既有 `origin/master`: `d847357f0a8262c0c91cf9b20989c6296c3d9892`
- local vs 本機既有 `origin/master`: `0 / 0`
- `origin/develop` 不存在。
- source repo 工作樹乾淨。
- 重要限制：因 fetch 失敗，本次只能宣稱「本機既有 refs / code-backed」，不能宣稱已看最新 remote。

本次掃描範圍:

- vault KB: `AGENTS.md`、`00-operating-rules.md`、`09-ai-prompt-manual.md`、`03-flow-learning-package-template.md`
- vault index: source repo inventory、flow inventory、05 / 08 / todo、既有 antplay admin-api / game-api claim 邊界
- source git: `git log --all`、`shortlog --all`、remote branch list、key commit stats
- key branches: `origin/master`、`origin/kafka_Nick`、`origin/kafka_Nick_dockerTest`、`origin/ProxyUserData`、`origin/feature/accumate_bet`、`origin/beta-dev-bigWin-Nick`、`origin/settle-pool`、`origin/risk-mng`、`origin/jackpot-balance`
- key commits: `kafka JOB` 系列、`#303` big-win notification、`#386` 代理用戶數據、`#590` ReportAgentPlayer currency / schedule、`#702` key 重複、`db_partition v2`、`fix ag_report_player`
- source code: `BigWinConsumerService`、`ProxyUserDataConsumerService`、`ReportAgentPlayerService`、`ReportAgentPlayerJob`、`ActivityAccumateBetConsumerService`、`SettlePoolMonitorConsumerService`、`GroupSettleTypeRecord`、`SyncDbFromRedis`

未完成:

- 未做 `antplay-slot-game-job` 全量 Step 1 / Step 2。
- 未逐條 flow 建立 `flow.md` / `career-interview.md`。
- 未逐檔逐行 Level 3。
- 未把 settle pool / risk / jackpot 做 Nick claim；後續 commit 需要另行拆 flow 並判斷 owner boundary。

## Important Commit Evidence

### Kafka job / event processing foundation

重要 commits:

- `kafka JOB` 系列：Kafka consumer / producer、rollback、platform-mock notification、OutOfMemoryError 處理與設定檔。
- `be86b4d`: `kafka JOB - 設定檔`。
- `a695231`: `kafka JOB - 通知 platform-mock`。
- `5998c4b`: `kafka JOB - 先做rollback`。

可支撐:

- 參與 job repo 的 Kafka consumer / producer 與 event processing 嘗試。
- 面試可講 event consumer 與下游通知 / rollback 嘗試、payload wrapper、記憶體風險與 job 設定。

不可誇大:

- 不說完整 Kafka platform owner。
- 不說已建立 exactly-once / outbox / full replay architecture。

### 代理玩家資料聚合 / report job

重要 commits:

- `12df475`: `feat(#386): 代理用戶數據 kafka匯入db`。
- `7aa06ed`: `feat(#386): Squash 代理用戶數據`。
- `512f1dd`: `feat(#386): 代理用戶數據 job匯總`。
- `4411d88`: `feat(#590): ReportAgentPlayer 拆 currency`。
- `806dc23`: `fix(#590): 每天一次`。
- `ef4f8f5`: `fix(#702): key重複`。
- `6866866`: `fix ag_report_player`。

已確認 code path:

- `ProxyUserDataConsumerService`: consume `settled_bets`，以 agent / player / day / currency 聚合 bet count、bet amount、win、profit。
- `ReportAgentPlayerService`: batch insert / update、summary、backup、delete report。
- `ReportAgentPlayerJob`: per-agent / per-player 汇總三天前資料，依 currency 更新 summary，backup 後刪除舊資料。

可支撐:

- 參與代理玩家報表資料從 Kafka 匯入 DB、每日 / 歷史 summary、currency 維度拆分與 key collision 修正。
- 面試可講 event-driven projection 的 key design、currency 維度、報表與交易真相的差異、batch job 重跑與資料清理風險。

不可誇大:

- 不說完整 BI / report platform owner。
- 不說完整對帳或完整資料平台 owner。

### Big-win notification

重要 commits:

- `1135490`: `feat(#303): 中大奖通知`。
- `c2cd523`: `feat(#303): 中大奖通知 小數X.00`。

已確認 code path:

- `BigWinConsumerService`: consume `settled_bets`，判斷 total win 是否達 bet + voucher bet 的 10 倍，依 agent / currency 找遊戲翻譯名稱，遮罩玩家名稱，產生 push user message。
- `GameDataCache` / `TransGames` / `TransMessageTemplates`: 補足通知資料來源。

可支撐:

- 參與 big-win notification 的 Kafka consumer 與 push message 組裝。
- 面試可講事件通知不是交易 source of truth，需保守處理缺翻譯、玩家遮罩、金額格式與下游 push topic。
- 2026-05-25 Step 5 已補 current code-backed flow、正式面試 case 與 claim gate: `(bet + voucherBet) * 10` 門檻、currency-based translation、`push_user` message、producer async failure、privacy boundary、重送去重與 outbox 取捨；`BetIdPersistence` 不是通知去重，下游 `antplay-push` 未見 `_id` dedupe 或 `fullPlayerName` 過濾。

不可誇大:

- 不說完整 push platform owner。
- 不說完整獎金 / jackpot owner。
- 不說通知 exactly-once / guaranteed delivery。

### Activity accumulated bet / voucher

已確認 code path:

- `ActivityAccumateBetConsumerService`: consume `settled_bets`，依活動設定、agent、player、currency、date / period 聚合投注，使用 Redis 累計投注與發放紀錄，再透過 `BetVoucherUtils` 發送 voucher。

可支撐:

- 作為下一步 Flow Track 候選，能面試活動累積投注、Redis key design、每日 / 期間限制、voucher 發放冪等邊界。

Step 5 收口:

- 已追 consumer、Redis key、voucher wrapper、path-specific history、merge evidence 與 current blame。
- 本 repo 沒有 `BetVoucherService` 下游 implementation / DB unique key evidence；每次發券使用新的 UUID `refId`，不能證明 deterministic idempotency。
- current implementation 主要是 Gill，後續 Arnold / Eliot context；Nick 是 merge evidence。
- 可作 project-level supporting evidence 與面試素材；暫不單獨寫入正式履歷主 bullet。

### DB partition / report path repair

重要 commits:

- `b754dae`: `feat: db_partition v2`，觸及 bet record repository、request log、jackpot balance repository、Kafka consumer 與 request log service。
- `6866866`: `fix ag_report_player`，修正 report repository 查詢 / 聚合 path。

可支撐:

- 參與 job / report path 的分表與查詢維護。
- 面試可講分表會影響 repository、job、request log 與報表查詢，不只是 schema 名稱變更。

不可誇大:

- 不說主導完整 DB sharding architecture。

### Settle pool / risk / jackpot boundary

已確認 code path:

- `SettlePoolMonitorConsumerService`: consume `settled_bets`，分 normal / activity / player-control 類型處理 settle pool。
- `GroupSettleTypeRecord`: 依 bet type、free spin、jackpot game、player control config 分群。
- `SyncDbFromRedis`: reset flag 出現時，從 Redis sync normal / activity / player control dark pool data 到 DB。

保守判斷:

- 這是很高價值的 code-backed 面試候選，但近期 log 顯示 Arnold / Eliot 在 2026-01 到 2026-02 有大量 settle pool / risk / jackpot 後續 commits。
- 本輪不把 settle pool / risk / jackpot 寫成 Nick 真實開發 owner。若 Nick 要用，需另做 Step 1 / Step 2 / flow 深挖，並逐 commit 判斷 Nick 的實際參與邊界。

## Resume Claim

可放履歷:

- 參與 AntPlay slot job / event processing 開發維護，處理 Kafka consumer / Quartz job、代理玩家報表聚合、活動累積投注、big-win notification、bet record / report 分表與 job config。
- 參與代理玩家報表 projection 維護，處理 `settled_bets` event 匯入、agent / player / day / currency 聚合、daily summary、backup / delete 與 key collision / currency 維度修正。
- 參與 big-win notification，處理 settled bet event 判斷、玩家名稱遮罩、遊戲名稱翻譯、金額格式與 push user topic message。

可面試講:

- Kafka `settled_bets` event 如何分流成 report projection、activity accumulate bet、big-win notification。
- 報表 projection 為什麼要設計 agent / player / day / currency key，以及 key collision / missing currency 的風險。
- Quartz job 如何做 historical summary、backup / delete，以及為什麼 schedule 要避免一天多次重複副作用。
- Big-win notification 為什麼不能當交易真相，只能當 derived event / notification。
- Activity accumulate bet 的 Redis key、每日 / 期間累積、voucher 發放與冪等風險。

不可誇大:

- 不說主導完整 AntPlay slot platform。
- 不說完整 Kafka event platform、exactly-once、outbox、full replay architecture。
- 不說完整 settle pool / risk / jackpot owner。
- 不說完整 BI / report platform owner。
- 不說完整遊戲數學 / RTP 策略 owner。

## 05 / 08 更新口徑

建議補入履歷 / 自傳的保守句:

> 參與 AntPlay slot job / event processing 開發維護，處理 Kafka consumer / Quartz job、代理玩家報表聚合、活動累積投注、big-win notification、bet record / report 分表與 job config。

若篇幅有限，可併入現職的「遊戲 API runtime / betting settlement / transfer wallet / event processing / scheduled job」一段，不需要單獨寫成完整平台 owner。

## Suggested Next

`antplay-slot-game-job` 的 Career Track 已能保守補履歷；Flow Track Step 1 / Step 2 已完成，`proxy-user-data-report-projection Step 5`、`activity-accumulated-bet-voucher Step 5`、`big-win-notification Step 5` 與 `settle-pool-monitor-darkpool-sync Step 4` 已完成。下一步若延續本 repo，應做同 flow Step 5，並維持 code-backed analysis-first，不作 Nick 主導 settle pool / risk owner。

```text
antplay antplay-slot-game-job settle-pool-monitor-darkpool-sync Step 5
```
