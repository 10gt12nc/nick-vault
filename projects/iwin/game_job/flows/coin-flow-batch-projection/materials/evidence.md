# Evidence: coin-flow-batch-projection

## 掃描結論

掃描日期：2026-05-19

掃描等級：Level 2。

證據層級：專案存在 / code-backed；Step 5 已完成 claim gate，正式履歷 / 自傳不更新。

## KB / vault 重讀

已重讀：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/iwin/game_job/README.md`
- `projects/iwin/game_job/step1-candidate-flows.md`
- `projects/iwin/game_job/step2-flow-comparison.md`
- `projects/iwin/game_job/flows/coin-flow-batch-projection/flow.md`
- `projects/iwin/game_job/flows/coin-flow-batch-projection/career-interview.md`
- `projects/iwin/game_job/flows/coin-flow-batch-projection/materials/interview.md`

## Source repo 狀態

Repo：`/Users/nick/Git/iwin/game_job`

已執行：

- `git fetch --all --prune`
- `git status --short --branch`
- `git rev-parse HEAD`
- `git rev-parse origin/main`
- `git rev-list --left-right --count HEAD...origin/main`

狀態：

- 分支：`main`
- local HEAD：`23908f474efb5cfe5a3ce2bc780fb67a0860c4c2`
- `origin/main` HEAD：`23908f474efb5cfe5a3ce2bc780fb67a0860c4c2`
- ahead / behind：`0 / 0`
- 工作區：乾淨
- Step 4 前已再次 fetch，local / remote HEAD 仍一致。
- Step 5 前已再次 fetch，local / remote HEAD 仍一致。

未做：

- 未 pull / checkout / merge / rebase。
- 未修改公司 repo。
- 未掃 production deployment 實際 enable 狀態。
- 未掃 `iwin_gameserver` upstream writer。
- 未掃 app_bi / BI 查詢端。

## 已讀 code path

主要 path：

- `config/application-quartz.yml`
- `src/main/resources/application-quartz.yml`
- `src/main/java/com/quartz/QuartzService.java`
- `src/main/java/com/quartz/CoinFlowJobQuartz.java`
- `src/main/java/com/common/job/BiJobBase.java`
- `src/main/java/com/common/enums/BiTaskEnums.java`
- `src/main/java/com/common/constant/TableNames.java`
- `src/main/java/com/job/biTask/CoinFlowJob.java`
- `src/main/java/com/common/utils/CoinFlowUtils.java`
- `src/main/java/com/pojo/entity/job/coinFlow/common/CoinFlowModelOneRoom.java`
- `src/main/java/com/pojo/entity/job/coinFlow/common/CoinFlowModelTwoRoom.java`
- `src/main/java/com/pojo/entity/job/coinFlow/common/CoinFlowModelThreeRoom.java`
- `src/main/java/com/pojo/entity/job/coinFlow/game/*.java` 入口與 switch 對照
- `src/main/java/com/service/impl/LogReelServiceImpl.java`
- `src/main/java/com/service/impl/LogJackpotServiceImpl.java`
- `src/main/java/com/service/impl/LogTaxtServiceImpl.java`
- `src/main/java/com/service/impl/UserGameBehaviourServiceImpl.java`
- `src/main/resources/mapper/logone/LogReelDao.xml`
- `src/main/resources/mapper/logone/LogJackpotDao.xml`
- `src/main/resources/mapper/logone/LogTaxtDao.xml`
- `src/main/resources/mapper/bilog/UserGameBehaviourDao.xml`

## 已確認 evidence

- `CoinFlowJobQuartz` 使用 `@DisallowConcurrentExecution`，設定 `BiTaskEnums.COIN_FLOW`、`BITaskTypeEnums.DAILY`、`taskOverTime=30 * 60` 後呼叫 `runTask()`。
- `QuartzService` 在 `coinFlowEnable=true` 時註冊 coin flow job，並於啟動時寫入今日 custom date。
- main config 目前 `coinFlow` cron 為每 5 分鐘，`coinFlowEnable=false`。
- `CoinFlowJob.execute()` 逐 channel 執行 `handleLogReelData()`、`handleLogJackpotData()`、`handleTaxtData()`、`saveUserGameBehaviour()`、`updateRedisCoinFlowData()`、`saveCoinFlowData()`。
- 三張 source table 都用 `id > lastPullMaxId order by id asc limit pageLimit` 增量查詢。
- `pageLimit=2000`，`maxLoop=2000`。
- Redis checkpoint key 來自 `BiJobBase.REDIS_KEY_LAST_PULL_MAX_ID`，pull finish 來自 `REDIS_KEY_PULL_OVER_STATUS`。
- `CoinFlowJob.REDIS_KEY_LAST_RESULT` 保存本日累計 coin flow JSON list。
- `user_game_behaviour_{yyyy_m}` 使用 `insert ... on duplicate key update` 累加。
- Mongo coin flow 使用 `bi_log.log_coin_flow_{gameId}`，同 channel / cps / date 先 remove 再 insert。
- `execAfter()` 要所有 channel 的 `log_reel`、`log_jackpot`、`log_taxt` 都 pull over 才 `taskFinish()`。
- `afterTaskFinish()` 會重置下一日 source table checkpoint / pull status，並刪除當日 Redis last result / last pull max id / pull over status。

## Commit / history 掃描

已讀 path-specific history：

- `CoinFlowJob.java`
- `com/pojo/entity/job/coinFlow/**`
- `CoinFlowUtils.java`
- `UserGameBehaviourDao.xml`
- `LogReelDao.xml`
- `LogJackpotDao.xml`
- `LogTaxtDao.xml`

觀察：

- `CoinFlowJob.java` path 主要 history 為 first commit 與 2026 k3s / Java 21 / Spring Boot 3.2 遷移。
- 相關 coin flow / mapper path 可見 `10gt12nc` 的 daily summary 類 commits，但這些不是 coin flow direct ownership evidence。
- 目前不可把此 flow 標為 Nick 真實開發過。

Step 5 重查：

- `git log --all` 於 `CoinFlowJob.java`、coin flow model、`CoinFlowUtils.java`、`UserGameBehaviourDao.xml`、`LogReelDao.xml`、`LogJackpotDao.xml`、`LogTaxtDao.xml` 只看到 first commit、2026 k3s / Java 21 遷移，以及 `10gt12nc` 的 daily summary 類相鄰 mapper commits。
- `git log --all --author='10gt12nc|Nick|nick'` 於同組 path 只看到 daily summary 類 commits，不能當作 coin flow direct contribution。

## Step 4 產出

已更新：

- `career-interview.md`：正式 30 秒、3 分鐘、STAR、常見追問、Lead / Architect 追問、可說 / 不可說。
- `materials/interview.md`：面試口語開場、深入講法、STAR 素材、追問回答與可用句子。
- `materials/claim-boundary.md`：更新 Step 4 判定；未補 direct evidence 前不更新正式履歷 / 自傳。

## Step 5 產出

已更新：

- `materials/claim-boundary.md`：完成 Step 5 claim gate，最終判定不更新正式履歷 / 自傳。
- `flow.md`：同步 Step 5 結論與下一步。
- `career-interview.md`：同步面試素材可用、履歷不可用與下一條建議。
- `projects/iwin/game_job/README.md`、`step1-candidate-flows.md`、`step2-flow-comparison.md`：同步本 flow 已完成 Step 5。
- 歷史下一步已完成：iwin_gameserver center-http-deposit-withdraw 已完成到 Step 5；目前沒有預設下一步，請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。

未更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

原因：目前 evidence 只能支撐 code-backed 面試分析，不足以支撐真實開發或參與維護 claim。

## 已確認風險

- `user_game_behaviour` 是累加 upsert；若 checkpoint 遺失或重拉同批資料，可能重複累加。
- Mongo coin flow 是 delete+insert；delete 成功 insert 失敗會讓 projection 缺資料。
- `LastCoinFlowResult` 是 Redis 暫存；遺失會影響當日累計基準。
- checkpoint / MySQL / Mongo / Redis 之間沒有跨資源 transaction。
- 跨日 pull over 依賴時間窗口與所有 channel / table status；source writer 延遲或單 channel 卡住會影響當日 finish。

## 待確認

- production 實際是否啟用 `coinFlowEnable`。
- `log_reel` / `log_jackpot` / `log_taxt` upstream writer 與寫入延遲。
- `user_game_behaviour_{yyyy_m}` 實際 unique key。
- app_bi / BI 查詢端如何讀 `log_coin_flow_{gameId}` 與 `user_game_behaviour_{yyyy_m}`。
- 是否有 production issue / MR / ticket 能把此 flow 升級為 Nick 真實開發過。
