# Evidence: online-payment-data-cleaning

## 掃描結論

掃描日期：2026-05-19

掃描等級：Level 2。

證據層級：專案存在 / code-backed；目前沒有足夠 Nick / `10gt12nc` direct path evidence 可標成真實開發過。

## KB / vault 重讀

已重讀：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/iwin/game_job/README.md`
- `projects/iwin/game_job/step1-candidate-flows.md`
- `projects/iwin/game_job/step2-flow-comparison.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`
- 既有 `daily-game-data-summary`、`third-party-record-mongo-backup`、`coin-flow-batch-projection` 文件片段作格式與下一步同步參考。

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

未做：

- 未 pull / checkout / merge / rebase。
- 未修改公司 repo。
- 未掃 production deployment 實際 enable 狀態。
- 未掃 `payment` repo source of truth。
- 未掃 app_bi 查詢端如何讀 Mongo `online_*` 或 `daily_economic_data_total`。

## 已讀 code path

主要 path：

- `config/application-quartz.yml`
- `src/main/resources/application-quartz.yml`
- `src/main/java/com/quartz/QuartzService.java`
- `src/main/java/com/quartz/biQuartz/OnlinePaymentDataJobQuartz.java`
- `src/main/java/com/common/config/JobProperties.java`
- `src/main/java/com/common/enums/BiTaskEnums.java`
- `src/main/java/com/common/enums/OrderTypeEnums.java`
- `src/main/java/com/common/enums/OnlineTypeEnum.java`
- `src/main/java/com/common/enums/OrderChannelEnums.java`
- `src/main/java/com/common/enums/PaymentReasonEnum.java`
- `src/main/java/com/common/utils/entityOption/CoinLogReasonChoose.java`
- `src/main/java/com/common/job/BiJobBase.java`
- `src/main/java/com/job/biTask/OnlinePaymentDataJob.java`
- `src/main/java/com/job/biTask/onlineDataService/OnlineDataHandleService.java`
- `src/main/java/com/job/biTask/onlineDataService/OnlineHandlerServerBase.java`
- `src/main/java/com/job/biTask/DailyEconomicDataTotalJob.java`
- `src/main/java/com/data/dao/bilog/PaymentOrderDao.java`
- `src/main/java/com/data/dao/bilog/OnlinePaymentDataDao.java`
- `src/main/java/com/data/dao/bilog/EconomicDataDayLogDao.java`
- `src/main/java/com/data/dao/bilog/DailyEconomicDataTotalDao.java`
- `src/main/java/com/pojo/entity/bilog/PaymentOrder.java`
- `src/main/java/com/pojo/entity/bilog/EconomicDataDayLog.java`
- `src/main/java/com/pojo/entity/bilog/DailyEconomicDataTotal.java`
- `src/main/java/com/pojo/vo/OnlineServicveVo.java`
- `src/main/resources/mapper/bilog/PaymentOrderDao.xml`
- `src/main/resources/mapper/bilog/EconomicDataDayLogDao.xml`
- `src/main/resources/mapper/bilog/DailyEconomicDataTotalDao.xml`
- `src/main/resources/initBiTable/payment_order-month.sql`
- `src/main/resources/initBiTable/payment_diamond-month.sql`

## 已確認 evidence

- `OnlinePaymentDataJobQuartz` 使用 `@DisallowConcurrentExecution`，設定 `BiTaskEnums.ONLINE_PAYMENT`、`BITaskTypeEnums.DAILY`、`taskOverTime=600` 後呼叫 `runTask()`。
- `QuartzService` 在 `onlinePaymentDataEnable=true` 時註冊 online payment data job。
- main config 目前 `onlinePaymentData: 0 */8 * * * ?`，`onlinePaymentDataEnable=false`。
- `OnlinePaymentDataJob.execute()` 每輪從 `lastOrderId=0` 開始查 `payment_order_{yyyy_m}`。
- `queryPaymentOrders()` 條件包含 `id > lastId`、`status=0`、`DATE_FORMAT(last_modify_time,'%Y-%m-%d') = date`、`order by id asc limit 5000`。
- `MAX_TASK_PAGE=1000`、`PAGE_LIMIT=5000`。
- 充值訂單判斷 `bill_type=PAY`，reason 來自 `CoinLogReasonChoose.payReason`：`51`、`52`、`203`、`54`；`reason=54` 還需 `trade_type=210 / 209`。
- 提現訂單判斷 `bill_type=WITHDRAW`，並把 `money` 改成 `actual_out_money` 後統計。
- `OnlineDataHandleService` 用 Redis hash 計算 distinct uid，key 形如 `Online:{service...}:Person:{date}`，TTL 2 天。
- Mongo 指標寫入 `bi_log`，collection 名稱由 `online_*` 加年月後綴產生。
- `economic_data_day_log` 寫入前會先 `delete from economic_data_day_log where date = ?`，再逐 channel / cps insert。
- `payment_amount`、`first_payment_amount`、`payment_amount_agent` 用 `insert ... on duplicate key update` 保存區間分布。
- `DailyEconomicDataTotalJob` 讀 `economic_data_day_log`，把 pay / withdraw / first pay / new register pay 等欄位組成 `daily_economic_data_total`。

## Commit / history 掃描

已讀 path-specific history：

- `OnlinePaymentDataJob.java`
- `OnlineDataHandleService.java`
- `OnlineHandlerServerBase.java`
- `OnlinePaymentDataJobQuartz.java`
- `PaymentOrderDao.xml`
- `EconomicDataDayLogDao.xml`
- `DailyEconomicDataTotalDao.xml`
- `payment_order-month.sql`
- `payment_diamond-month.sql`

觀察：

- direct path history 主要是 first commit、`5346303 feat(#RD-71): 删除query移动到insert前` 與 2026 k3s / Java 21 / Spring Boot 3.2 遷移。
- `--author='10gt12nc|Nick|nick'` 在上述 direct path 沒有命中。
- 目前不可把此 flow 標為 Nick 真實開發過。
- `5346303` 變更的是 `DailyEconomicDataTotalJob` / `DailyEconomicDataTotalDao`，不是 `OnlinePaymentDataJob` direct path；可作下游 delete / insert 順序風險參考，不作 Nick contribution evidence。

## 已確認風險

- source query 依 `last_modify_time` 定義資料日，late callback / 人工補單 / 修單可能影響報表歸屬日期。
- 每輪從 `lastOrderId=0` 掃當日全量成功訂單；沒有持久 checkpoint，資料量大時成本會上升。
- Mongo `online_*` 指標是 insert 型寫入；目前未看到先清理或 upsert，同日重跑可能重複。
- `economic_data_day_log` delete by date 再 insert；失敗時會有整日 economic summary 空窗。
- Redis distinct uid key TTL 2 天；超過 TTL 的 custom date replay 需要確認人數去重語意。
- `DailyEconomicDataTotalJob` downstream 依賴 `economic_data_day_log`，上游失敗會影響 profit、pay rate、ARPU / ARPPU。

## 待確認

- production 實際是否啟用 `onlinePaymentDataEnable`。
- `payment` repo 如何寫入 / 更新 `payment_order_{yyyy_m}`，以及 `last_modify_time` 是否等於成功時間。
- app_bi 或 BI 報表如何查 Mongo `online_*` 指標，是否容忍重複文件。
- `payment_amount*` 表的 unique key 定義。
- custom date replay 是否會清 Mongo `online_*` 與 Redis distinct uid key。
- 是否有 Nick 本人確認、MR、ticket、production issue 可補強此 flow。
