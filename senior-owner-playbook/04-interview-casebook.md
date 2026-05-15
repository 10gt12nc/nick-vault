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

面試主軸：

遊戲結算的風險是玩家餘額、round log、provider transaction、BI retention 不一致。Senior 要能找出真正的 commit boundary，避免 reporting job 或 request log 被誤當成結算真相。

可講重點：

- round id / transaction id 是核心冪等點。
- wallet mutation 與 round log 應有一致的 commit 狀態。
- rollback 不是反向再打一筆就好，需要狀態與副作用邊界。
- BI / report 是 projection，不應是 source of truth。

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

- 此案例只作面試分析素材，不更新正式履歷 / 自傳。
- 目前是 `專案存在 / code-backed`；Nick 本人 MR / ticket / commit / production issue / 本人確認待補。
- `origin/k3s` 有 Redis lock 修正，但 production 是否部署待確認；不可說成 Nick 修復雙領。

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

## 面試回答公式

```text
我先說這條 flow 的業務風險。
接著說 code 入口與資料怎麼走。
然後說我看到的 failure window。
再說我會優先處理哪個 owner decision。
最後說我不會誇大的邊界，以及下一步要補的 evidence。
```
