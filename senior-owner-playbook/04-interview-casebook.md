# Senior / Lead / Architect 面試案例

這份不是背稿，而是用來把 flow 轉成面試可講的 case。

## 案例 1：金流 callback 一致性

對應 flow：

- `payment-provider-callback`
- `payment-order-provider-request`
- `withdrawal-auto-review-refund`

面試主軸：

第三方金流 callback 不是單純接 API。真正困難在於 provider 可能重送、timeout、欄位不一致、簽章失敗、訂單已終態、MQ 副作用失敗、人工補償介入。Senior 要能定義訂單 lifecycle、冪等 key、callback audit、對帳查詢與補償邊界。

可講重點：

- callback 必須先驗簽與正規化欄位。
- 訂單狀態轉移要單向、可審計。
- 重複 callback 應該 no-op 或回傳一致結果。
- 入分 / 扣分副作用不能和 callback ack 混在一起亂做。
- 若 MQ 或下游失敗，要有可查詢的 pending / failed 狀態。

payment provider request 補充：

- `payment-order-provider-request` 已完成 Step 5 claim gate，可用 Nick 本人 provider 對接與維護素材。
- 最強 code evidence 是 Pay4z：`origin/pay4z-Nick` / `7853917` 新增 `Pay4zController` 與 `Pay4zServiceImpl`，涵蓋 `/newPay`、callback、查單、簽章、金額單位與 merchant order id。
- NaNapay、BFPAY、NimTestPay 也可作輔助 evidence，但要分清 production provider、查單 / fix、local / SIT manual testing。
- 面試時可以說「參與 provider request / callback / query 對接與維護」，不要說主導完整金流或全部 provider owner。

Lead / Architect 追問：

- 如果 provider 說成功，但你 DB transaction rollback 怎麼辦？
- 如果 DB 成功，但 MQ 發送失敗怎麼辦？
- callback log 是 source of truth 嗎？
- 怎麼設計 reconciliation job？
- 什麼時候需要人工補償？

## 案例 2：Transfer wallet 轉入轉出

對應 flow：

- `transfer-wallet-transfer-in-out`
- `transfer-wallet-ledger`
- `provider-transfer-in-out`
- `transfer-compensation-retry`

面試主軸：

Transfer wallet 的核心是半完成狀態。轉出成功、轉入失敗、provider timeout、reference id 重複、retry 重入，都可能造成金額錯誤。Senior 要能把 transfer reference、ledger、狀態機、補償 retry 與人工查詢設計清楚。

可講重點：

- reference id 必須可追蹤且可冪等。
- ledger 要能反映每一步狀態，不只看 API response。
- timeout 不能直接當失敗。
- compensation 要有 retry count、next retry time、exhausted 狀態。
- 後台要能查到目前卡在哪一段。

## 案例 3：下注 / 派彩 / rollback

對應 flow：

- `bet-settlement`
- `game-round-settlement`
- `antplay-bet-settle-rollback`
- `third_games_api/gsc-transfer-bet-settle-rollback`

面試主軸：

遊戲結算的風險是玩家餘額、round log、provider transaction、BI retention 不一致。Senior 要能找出真正的 commit boundary，避免 reporting job 或 request log 被誤當成結算真相。

可講重點：

- round id / transaction id 是核心冪等點。
- wallet mutation 與 round log 應有一致的 commit 狀態。
- rollback 不是反向再打一筆就好，需要狀態與副作用邊界。
- BI / report 是 projection，不應是 source of truth。

GSC transfer 補充案例：

- `third_games_api` 是 provider adapter，不是 wallet source of truth。
- GSC `transfer` 非 rollback 情境會呼叫 gameserver `PGTRANSFERINOUT`，真正錢包異動在 gameserver job。
- `third_games_api` Mongo `third_log_gsc` / `third_transaction_gsc` 是 callback audit / transaction evidence，不是已確認的唯一帳本。
- `ROLLBACK` 分支目前 code-backed 行為是不呼叫 gameserver mutation，只回算 balance 並寫 Mongo；必須查 provider spec，不可說已完成 wallet rollback。
- gameserver 成功但 adapter Mongo insert 失敗，是這條 case 的核心 failure window。

GSC transfer 保守邊界：

- 證據層級是 `專案存在 / code-backed` 與 `分析素材 / learning-only`。
- 沒有 Nick 本人 MR / ticket / production issue / 本人確認前，不寫成 Nick 主導 GSC 串接或修復 rollback。

## 案例 4：Kafka / MQ 可靠性

對應 flow：

- `settled-bets-kafka`
- `bet-record-request-log-mq`
- `request-log-monitor-alert-mq`

面試主軸：

非同步不是把資料丟出去就好。要看資料進 MQ 前後的 durable state、consumer retry、DLT、重複消費、刪除時機與 audit。

可講重點：

- Redis delete-before-ack 是典型風險。
- 重要事件應有 outbox 或 durable pending state。
- consumer 要能 idempotent。
- DLT 不是垃圾桶，要有重放與告警流程。
- 監控要能從 request id / trace id 找回整條 flow。

## 案例 5：K3s / rollout / observability

對應 flow：

- `k3s-migration-track`
- `iwin-service-rollout`
- `gameserver-phased-rollout`
- `observability-pipeline`

面試主軸：

平台部署不是 YAML 管理，而是 release risk management。Senior / Lead 要能看 deployment strategy、probe、resource、config、secret、log pipeline、rollback 與 service topology。

可講重點：

- rollout 要分批、可回滾、可觀測。
- readiness / liveness 不能亂設。
- config / secret 要和 image 分離。
- log pipeline 要能支援 incident RCA。
- 服務上線前要知道依賴與 failure blast radius。

## 案例 6：報表 projection / 批次彙總可信度

對應 flow：

- `app_bi/daily-game-record-summary`
- `game_job/daily-game-data-summary`

證據邊界：

- `game_job/daily-game-data-summary` 已完成 Step 5 claim gate，且已納入 `projects/iwin/game_job/contribution-claim-consolidation.md`。
- `10gt12nc` 在 `game_job` daily summary job / service / mapper / config path 有 #247 主體 commits，也有 PG / Antplay 時區修正、新增玩家 / 留存與備份 / 清理相關 commits。
- `app_bi` 查詢端仍只作下游 local snapshot；上游 writer 只做線索掃描，不可寫成完整 BI pipeline owner。

面試主軸：

後台報表不是 source of truth。每日遊戲資料彙總表面上是 app_bi 查詢頁，實際要追到 game_job producer、來源戰績日表、projection table、summary row 與後台 pivot 查詢。Senior 要能分清楚原始交易 / 遊戲紀錄、批次產物、報表展示與營運判讀之間的邊界。

可講重點：

- 報表 table 多半是 projection，不應直接當交易真相。
- 批次 job 要能判斷日期切分、時區、重跑與半成品風險。
- summary 查詢要看 group by / pivot / 金額單位是否一致。
- 留存、活躍、新增玩家這類指標通常有成熟度與延遲問題。
- 報表異常排查要先回到 producer log、source table 與產生時間。

Lead / Architect 追問：

- 如果 job 跑一半失敗，後台會看到什麼？
- 如果時區切錯一天，怎麼發現與修正？
- 報表補跑會不會覆蓋人工修正或造成重複統計？
- 報表 table 需要 batch id / generated_at / source range 嗎？
- 營運看到數字異常時，第一個排查入口應該是哪裡？

## 案例 6-2：Mongo retention / copy-then-delete 批次安全

對應 flow：

- `game_job/third-party-record-mongo-backup`

證據邊界：

- 目前完成 Step 5，可作 code-backed 面試 case，且已納入 `projects/iwin/game_job/contribution-claim-consolidation.md`。
- `10gt12nc` 有 GSC 分批查詢與 batch size 調整 direct commits：`d11b1f4`、`bf92773`。
- 可寫局部「參與 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與 batch size 調整」。
- 不可寫成 Nick 主導完整第三方遊戲紀錄備份或 retention policy owner。

面試主軸：

第三方遊戲 provider log / transaction retention 不是單純清 Mongo。真正的風險在於 active collection 到 backup collection 的 copy-then-delete 不是 atomic；Senior 要能定義重跑 idempotency、duplicate backup、delete count reconciliation、retention policy 與 audit 查詢邊界。

可講重點：

- active collection 不是 wallet source of truth，而是 provider log / transaction evidence。
- copy-before-delete 的方向偏安全：寧可 duplicate，不可丟資料。
- backup collection 應保留 source `_id` 或 source id，並使用 duplicate-safe write。
- 每批要記錄 fetched / copied / deleted / remaining；delete count 不一致應 fail 或告警。
- batch size 是 production tuning，不是越大越好，要看文件大小、Mongo 壓力、job duration 與錯誤率。
- retention 的 14 / 30 天要對齊客服查詢、provider dispute、audit 與 storage 成本。

Lead / Architect 追問：

- 如果 insert backup 成功但 delete origin 失敗，下一輪怎麼避免 duplicate？
- backup collection 是否需要 unique index？如果 duplicate key 發生怎麼處理？
- 為什麼不用 Mongo transaction？成本與部署前提是什麼？
- retention policy 誰決定？30 天後 hard delete 是否合規？
- 多 instance 或 manual endpoint 同時跑同 collection 怎麼鎖？

## 案例 6-3：Payment reporting projection / replay-safe 報表

對應 flow：

- `game_job/online-payment-data-cleaning`

證據邊界：

- 已完成 Step 5 claim gate，只作 code-backed 面試 case，不更新正式履歷 / 自傳。
- direct path history 未見 Nick / `10gt12nc` commit；不能說 Nick 開發或主導此 flow。
- 這是 payment order reporting projection，不是 provider callback、payment source of truth、錢包上分或提款出款核心。

面試主軸：

payment order 進入 BI / economic reporting 後，仍然會影響 money reporting correctness。Senior 要能分清 source order、資料日、Redis distinct uid、Mongo insert projection、MySQL `economic_data_day_log` delete+insert 與 downstream daily total 的一致性邊界。

可講重點：

- `payment_order_{yyyy_m}` 成功訂單不是直接等於報表，需要經過 status、資料日、reason / tradeType / onlineType 分類。
- `last_modify_time` 作為資料日會受 late callback、人工補單或修單影響。
- Mongo insert、MySQL delete+insert、MySQL upsert、Redis 去重四種 state 語意不同，重跑策略不能混在一起。
- `economic_data_day_log` 的空窗會傳到 `daily_economic_data_total`，影響 profit、pay rate、ARPU / ARPPU。
- replay-safe 設計要先定義資料日與 projection 唯一鍵，再考慮 upsert、staging / versioned snapshot 與 downstream 重算順序。

Lead / Architect 追問：

- 如果 Mongo 已 insert，但 `economic_data_day_log` delete 後掛掉，下游會看到什麼？
- `last_modify_time`、create time、provider event time 哪個才是報表資料日？
- Redis distinct uid TTL 到期後，custom date replay 的人數語意是否一致？
- Mongo 指標要保存 snapshot history 還是 deterministic latest state？
- 怎麼避免 reporting 異常被誤判成 provider callback 或 wallet transaction 錯？

## 案例 6-4：Table rollover / schema template 可靠性

對應 flow：

- `game_job/partition-table-creation`

證據邊界：

- 已完成 Step 5 claim gate，只作 code-backed 面試 case，不更新正式履歷 / 自傳。
- direct path history 未見 Nick / `10gt12nc` commit；不能說 Nick 開發或主導此 flow。
- `origin/k3s` 的 prod cron 對齊 commit 由 Arnold 提交，且 `createTableEnable=false`，不能說 production enable 已確認。
- 這是 table rollover support flow，不是完整 schema migration platform。

面試主軸：

分表建立不是業務交易，但它是 log writer、batch projection 與報表查詢的前置可靠性條件。Senior 要能說清楚 SQL template、日期 / 月份 table naming、`CREATE TABLE IF NOT EXISTS` 的 idempotency 邊界、多 channel DB partial success、schema drift 與補建能力。

可講重點：

- `CREATE TABLE IF NOT EXISTS` 解決重跑時表已存在的問題，不解決 schema evolution。
- template 檔名是 contract，未知格式或 typo 應該 fail fast。
- 多 channel DB 建表不能只看 job 結束，要看 expected / created / existed / failed summary。
- job 只建下一天 / 下一月，不代表可以補回歷史缺表。
- production-grade 補強應包含 dry-run expected list、failed table alert、指定日期補建工具與 schema drift check。

Lead / Architect 追問：

- 如果某個 channel DB 建表失敗，但 job 繼續跑完，下游會看到什麼？
- `CREATE TABLE IF NOT EXISTS` 為什麼不是完整 idempotency？
- template 改 schema 後，既有分表怎麼處理？
- 為什麼這裡不直接用 Flyway / Liquibase？兩者解決的問題差在哪？
- 如果跨月第一天報表 table not found，排查順序是什麼？

## 案例 7：遊戲局紀錄查詢 / 玩家申訴排查

對應 flow：

- `app_bi/game-round-record-query`

證據邊界：

- 此案例只作面試分析素材，不更新正式履歷 / 自傳。
- `app_bi` 查詢端未看到 Nick author；`iwin_gameserver` writer 有 Nick commit 線索，但應另開後端 flow 深挖。

面試主軸：

後台遊戲局查詢不是單純查表。它是玩家申訴與 production troubleshooting 的入口，要能分清楚後台看到的 `log_reel` 戰績 log、wallet / currency ledger、provider transaction 與真正 settlement truth。Senior 要能追 app_bi 查詢端、每日分表、channel shard、`serial_id`、bet / settle update 與 log writer failure window。

可講重點：

- `log_reel` 是 troubleshooting projection，不是完整交易 truth。
- 查不到局不等於玩家沒玩，可能是日期分表、channel shard、log pipeline 或查詢條件問題。
- 第三方遊戲的 `serial_id` 是 provider transaction / bet id 的關聯 key，但要確認唯一性與 update miss。
- bet insert 成功、settle update 失敗時，後台可能看到不完整戰績。
- 長區間每日分表查詢與 `date_format(game_start_time, ...)` 可能造成效能與索引風險。
- 玩家申訴 SOP 應該同時查戰績 log、wallet / currency log、provider transaction 與 settlement 狀態。

Lead / Architect 追問：

- `log_reel`、wallet ledger、provider transaction 的資料契約怎麼定？
- `serial_id` update miss 如何告警、補償與重放？
- 是否需要統一 troubleshooting portal 或 correlation id？
- 多 provider 的 bet / settle / refund 狀態機是否應標準化？
- 如何讓客服查詢不壓垮 log DB？
- 查不到資料時，系統是否要提示可能原因而不是只回空結果？

## 案例 8：優惠券兌換上分 / 打碼要求

對應 flow：

- `game_api/coupon-redeem-credit-grant`

證據邊界：

- 已完成 Step 5 claim gate，可作 game_api project contribution consolidation evidence；正式履歷 / 自傳仍以 project-level consolidation 為準。
- `10gt12nc` 在 `game_api` coupon Controller / Service / DAO / mapper / entity 與 `iwin_gameserver` bet target handler 有 path-specific commits。
- `origin/k3s` 有 Redis lock 修正，但 commit author 不是 `10gt12nc` 且 production 是否部署待確認；不可說成 Nick 設計 Redis lock 或修復雙領。

面試主軸：

優惠券兌換不是單純查 coupon 再寫 record。它是 API 驗 token 與資格後，跨到 gameserver 做上分，再設定提款打碼要求，最後才寫本地 `coupon_record` 與 `used_count`。Senior 要能拆出 coupon local state、玩家錢包、打碼狀態、GM command 與 reconciliation 的 transaction boundary。

可講重點：

- `game_api` 是 orchestration layer，不是玩家錢包 source of truth。
- `main` 上同 uid 同 coupon 是 check-then-insert；DB dump 顯示 `(coupon_code, log_user_id)` 是普通 index，不是 unique key。
- `DEPOSIT` 與 `SET_BET_TARGET_COUPON` 是兩個外部 side effect，可能只有一個成功。
- GM command 成功後才寫本地 `coupon_record`，會有「錢已發但本地未記錄」窗口。
- Redis lock 是 mitigation，不應替代 DB unique constraint 與下游 deterministic idempotency key。
- 對帳要比對 coupon record、reason 124 wallet log、coupon bet target log 與 `used_count`。

Lead / Architect 追問：

- coupon redeem 的 success contract 是 wallet applied，還是 wallet + bet target + record 都完成？
- 下游 deposit 是否支援同 bill no 重送 idempotent？
- 如果 deposit 成功但 bet target 失敗，補償 owner 是誰？
- Redis lock TTL 與 delete 是否用 compare-and-delete？
- campaign 若有總量上限，如何避免 concurrent oversell？
- 客服如何查「玩家說領了但系統查不到 record」？

## 案例 9：Payment runtime config selection

對應 flow：

- `payment/payment-channel-config-selection`

證據邊界：

- 此案例只作面試分析素材，不更新正式履歷 / 自傳。
- 已完成 Step 5 claim gate；未找到 Nick 直接修改 payment list/detail/withdrawConfig 或 app_bi Redis sync 的 path-specific evidence。
- `10gt12nc` 在相關 path 的 `03c28e3` / `6539d7a` 屬於 order insert id copy 修正，支撐 provider request / order insert consistency，不支撐本 flow owner claim。

面試主軸：

支付方式與商戶設定不是單純後台資料，而是玩家能否進入 money flow 的 runtime eligibility contract。Senior 要能看懂 MySQL source of truth、Redis projection、payment API filter 三層一致性，並處理多 key partial sync、cold-cache fallback、player layer / channel / device filter 與 fail closed。

可講重點：

- MySQL 設定是 source of truth，Redis `settings` 是 runtime projection，payment API 是 eligibility decision。
- `/payment/list` 決定玩家可見支付類型，`/payment/detail` 決定該 pay code 下有哪些商戶 / VIP / 快捷支付 detail。
- `payTypeList`、`merchantList`、`merchantAccountList`、`convenientPay`、`vipPay`、`paySetting` 必須同版本或可驗證。
- Redis key miss 可以 fallback DB，但第一個 request 不能仍拿到空列表。
- 設定不完整時應 fail closed，不曝光不可確認商戶。

Lead / Architect 追問：

- DB 已改、Redis 未同步時玩家會看到什麼？
- `payTypeList` 有新 pay code，但 `merchantList` 還沒同步，怎麼偵測？
- 如何設計 config version / batch validation？
- per-channel readiness 要存在哪裡、誰看？
- fail closed 對營收有影響時，owner decision 怎麼取捨？

## 案例 10：Partner API 上分 / 下分 / 查單

對應 flow：

- `game_api/partner-deposit-withdraw-bill`

證據邊界：

- 已完成 Step 5，可作 code-backed 面試素材。
- 目前未看到 Nick / `10gt12nc` 直接修改 partner flow path 的 evidence；不得寫成正式履歷，也不得說 Nick 主導 partner API 或 wallet reconciliation。
- `origin/k3s` 有 sign replay / secret log 安全性修正方向，但 production deploy 與 Nick 貢獻待確認。

面試主軸：

這是外部 partner money API 到內部 gameserver wallet side effect 的典型 boundary case。`game_api` 會驗參數與 sign，先寫 Mongo 訂單，再送 GM command 做上分 / 下分，最後更新訂單狀態；查單則查 Mongo 每日分表。Senior 要能拆出 partner idempotency、Mongo order、GM wallet side effect、pending / unknown state 與 reconciliation。

可講重點：

- `NewPay` / `Withdraw` 每次產生 `yyyyMMdd-UUID` billNos；目前未看到外部 partner order id 作為 idempotency key。
- `saveCoin(status=1)`、GM command、`updCoin(status=0)` 是三段，不在同一個 transaction。
- GM 成功但 `updCoin` 失敗時，partner 查單可能 pending，但玩家錢包已異動。
- generic exception branch 未看到 `updCoin`，可能留下 pending order。
- `updCoin` 用當日 collection，跨日補償時可能找錯原單 collection。
- `BillInfo` / `BillList` 跨每日 collection 查詢；若部分 collection 查詢失敗，可能形成 partial result。

Lead / Architect 追問：

- partner API success contract 是 GM accepted，還是 wallet applied + local order success？
- partner timeout 重送時，用什麼 idempotency key 判斷同一筆 business request？
- 下游 gameserver 是否支援同 billNos idempotent replay？
- pending aging order 要由誰掃、用哪個 source of truth 收斂？
- 查單 partial failure 是 fail whole request，還是回 partial 並標示？
- sign replay prevention 的 nonce key、TTL 與 secret log policy 怎麼定？

## 案例 11：代理佣金領取 / 轉帳

對應 flow：

- `game_api/agent-bonus-receive-transfer`

證據邊界：

- 已完成 Step 5，可作 code-backed 面試素材。
- 目前未看到 Nick / `10gt12nc` 直接修改 agent bonus API、Mongo model、Redis lock、`game_job` agent bonus wash / settlement path 的 evidence；不得寫成正式履歷，也不得說 Nick 主導代理分潤或佣金結算。
- `game_api` repo-wide Nick evidence 主要集中在 coupon 與邀請轉盤活動；`game_job` repo-wide Nick evidence 主要集中在每日彙總、GSC 分批查詢與 coupon ActivityJob / CouponRecord 相關。

面試主軸：

這是 money-like balance flow，不是單純後台查詢。`game_job` 先把每日佣金累加到 Mongo `agent_money.money`，`game_api` 再提供代理轉帳與領取。轉帳會更新雙邊 Mongo balance、Redis projection 與 audit log；領取會先送 GM deposit，再清 Mongo 可領餘額、更新 Redis、寫領取 log。Senior 要能拆出 Mongo source of truth、Redis projection、GM wallet side effect、短 lock vs idempotency、pending / unknown state 與 reconciliation。

可講重點：

- Mongo `agent_money` 比較像佣金可領餘額 source of truth，Redis `GameAgent:{rid}` 是 projection。
- `Bonus:ReceiveLook:{rid}` 只能擋短時間連點，不是 money flow idempotency。
- `bonusReceive` 每次 GM 上分使用新的 UUID billNos；GM 成功但 Mongo 清零失敗後，重送可能形成第二次 wallet side effect。
- `transferReceive` 先扣轉出者、再加轉入者、再更新 Redis、最後寫兩筆 log；任一步失敗都需要 operation id / 補償或 reconciliation。
- `Daily:Task:{date}.purchase_status` 是 game_job 與 game_api 的粗粒度互斥，應定義更清楚的 job lifecycle。

Lead / Architect 追問：

- 佣金可領餘額、Redis projection、gameserver wallet balance 三者誰是 source of truth？
- GM timeout 時，同一筆領取要怎麼用 deterministic billNos 重試？
- 轉帳雙邊 balance update 失敗時，是補償、重放，還是人工修復？
- audit log 在最後才寫，失敗時客服怎麼查帳？
- game_job settlement 與玩家領取同時發生時，fail closed 的條件怎麼定？

## 案例 12：Slot math contract / fixedMultiBet / currency 相容

對應 flow：

- `antplay/*-math/fixed-multi-bet-currency-math-core-compatibility`

證據邊界：

- 已完成 Step 5，可作 `*-math` grouped 履歷 bullet 的強化 evidence，也可作 Senior Backend 面試主案例。
- Nick / `10gt12nc` 在 `math-core`、`sdt-math`、`sfm-math` 有 direct commits；`slc-math` 作 code-backed 旁證與部分 direct evidence。
- 已深掃代表 path：`math-core`、`sdt-math`、`sfm-math`、`slc-math`。
- 未深掃全部 71 個 `*-math` repo、未深掃上游 game-api caller、未補 production incident / ticket evidence。

面試主軸：

Slot math module 的 fixedMultiBet / currency 調整不是單純加欄位，而是多 module contract compatibility 與 money-like correctness。Senior 要能說清楚 core interface default fallback、module input、currency multiplier、totalBet、jackpot scaling、debug bet 與 result 欄位如何使用同一組下注上下文。

可講重點：

- `math-core` default fallback 是多 module rollout 安全網，不是全部 module 完整支援的證明。
- fixedMultiBet 不只影響 totalBet，也要看 jackpot multiplier、debug / test input 與前端可核對 result。
- currency multiplier 用 ThreadLocal 可降低傳參成本，但要注意入口初始化與 thread reuse。
- math module 不負責 wallet rollback，但它的輸出會被上游扣款 / 派彩 / 對帳依賴，所以仍要用 money-like correctness 檢查。
- module compatibility 要用 checklist 管：入口 override、InputData、calculation、jackpot、result、test coverage。

Lead / Architect 追問：

- 為什麼不一次強制所有 `*-math` module 改新 interface？
- 如果 caller 以為 fixedMultiBet 生效，但 module fallback 忽略了，怎麼偵測？
- currency multiplier 應該 ThreadLocal、明確傳參，還是放進 immutable spin context？
- fixedMultiBet / currency 要不要提升到 core result contract？
- 如果 jackpot 金額比例錯，排查順序是 lineBet、fixedMultiBet、currency、maxBet / nowBet、jackpot balance payload 哪一層？
- 如何設計 module compatibility matrix 與 release checklist？

## 案例 13：RTP / 輪帶模擬與驗證

對應 flow：

- `antplay/*-math/rtp-reel-strip-simulation-validation`

證據邊界：

- 已完成 Step 5，可作 `*-math` grouped 履歷 bullet 的強化 evidence，也可作遊戲數學 / high-risk domain validation 面試案例；正式履歷不單獨寫成完整 RTP owner。
- Nick / `10gt12nc` 在 `sph-math` 有 RTP / JP / simulation / histogram / trigger log 相關 commits；在 `spn-math` 有 RTP_3 / buy free / scatter / `lastSymbols` 相關 commits。
- 已深掃代表 path：`sph-math` 的 `RTPConstants`、`Ratio_RTP_*`、`GenerateByRatio`、`Simulate`、`ProcessGameSpin`、`SlotReelStripTable`、`AbstractSlotMath`，並用 `spn-math` 作對照。
- 未掃正式 GDD / certification 文件、未掃 game-api runtime caller、未深掃全部 71 個 `*-math` repo。

面試主軸：

RTP / reel strip validation 不是線上交易 API，但會影響 production payout correctness。Senior 要能把 target / tolerance、symbol ratio、candidate reel strip、simulation rounds、Base RTP、FG trigger、Free RTP、Jackpot hit rate、runtime path 一致性與 state reset 串起來，而不是只說「調輪帶」。

可講重點：

- 總 RTP 接近不夠，還要拆 Base RTP、Free trigger、Free RTP、Jackpot hit rate。
- simulation 要走真實 `P21008SlotMath#spin` / `AbstractSlotMath#init`，不能和 production runtime 分叉。
- `flagRTP="RTP_REEL_STRIP_OPTIMIZER"` 是候選輪帶接到 runtime path 的關鍵 routing。
- `spn-math` 的 `lastSymbols` 顯示 feature state 會跨 spin 影響驗證，helper 必須 reset / carry 正確。
- release gate 應保存 target source、rounds / attempts、seed 或 candidate reel strip、output summary、accepted production diff 與 reviewer note。

Lead / Architect 追問：

- 如果總 RTP pass，但 free trigger 偏高，會怎麼處理？
- simulation pass，上線後 RTP 偏差，回查順序是什麼？
- 要如何避免 simulation 和 production runtime path 分叉？
- `GenerateByRatio` 若沒有固定 seed，怎麼讓 validation 可重跑 / 可追溯？
- GDD target、code constants、production reel strip diff 要怎麼做 traceability？
- 這和 payment consistency 的相同與不同是什麼？

## 案例 14：Buy Free / Scatter / RTP_3 結果契約

對應 flow：

- `antplay/*-math/buy-free-scatter-rtp3-result-contract`

證據邊界：

- 已完成 Step 4，可作 `*-math` grouped 履歷 bullet 的強化 evidence，也可作 slot math feature 的跨層 contract consistency 面試案例；正式履歷不單獨寫成完整 buy free owner。
- Nick / `10gt12nc` 在 `spn-math` 有 RTP_3、buyFreeWinInfo、`HAVE_3_SCATTER_WIN`、`lastSymbols` reset 相關 direct commits。
- 已深掃代表 path：`spn-math` 的 `SPNOperatorService`、`SPNMathFactory`、`AbstractSlotMath`、`P21008SlotMath`、`GameSetting`、`SlotReelStripTable`、`RoundResult` / `FreeBetResult`，並補讀 `antplay-slot-game-api` 的 `GameFacade` / `SlotMathFacade` / `GameFlowFacade` caller path。
- 未掃完整 wallet mutation、bet record DB schema、前端展示、darkpool service implementation、正式 GDD / validation report、全部 71 個 `*-math` repo。

面試主軸：

Buy free 不是只改 `RTP_3` 輪帶，而是 pricing contract、runtime routing contract、result contract 三層一致性。Senior 要能說清楚 game-api 如何 validate `freeSpinType`、取得 odds 並影響 bet amount，math module 如何透過 `flagRTP=RTP_3` 走 buy free reel strip，最後 `RoundResult` / `FreeBetResult` 與 result JSON 如何讓前端、bet record、客服查詢與統計理解這筆是 buy free。

可講重點：

- `GameFacade` 在 beforeBet 前用 math config 的 buy free odds 調整 bet amount；`GameFlowFacade#afterBet` 補 result JSON 的 buy free metadata。
- `SlotMathFacade.freeSpinBet` 是 normal bet 與 buy free runtime path 的分岔點。
- `RTP_3` 在本 flow 是 buy free routing key，不只是 simulation label。
- `lastSymbols` 會保存 / 回寫 scatter 與 wild state；helper loop 或 debug flow 沒 reset 會造成結果污染。
- `RoundResult.freeTotalWin`、`freeScatterPay`、`freeBetResults` 要和前端 / bet record 語意一致。

Lead / Architect 追問：

- odds 應該放 math config、營運配置，還是兩者同步？如何避免 beforeBet / afterBet odds 漂移？
- result JSON 的 `totalBet` 是一般 bet 還是 buy free 後成本？schema 要怎麼定義？
- 如果玩家回報買免費局扣款或結果不對，排查順序是 request type、odds、`RTP_3` routing、free result，還是 bet record？
- `lastSymbols` 這種跨 spin state 是否應包成明確 state object，避免 static / helper loop 污染？
- normal bet / buy free 的 darkpool 統計要怎麼分流與對帳？

## 面試回答公式

```text
我先說這條 flow 的業務風險。
接著說 code 入口與資料怎麼走。
然後說我看到的 failure window。
再說我會優先處理哪個 owner decision。
最後說我不會誇大的邊界，以及下一步要補的 evidence。
```
