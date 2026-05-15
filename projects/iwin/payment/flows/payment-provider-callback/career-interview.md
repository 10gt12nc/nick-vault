# payment-provider-callback career-interview

## 證據層級

- Flow 存在：`專案存在 / code-backed`
- Nick 個人參與：`待確認`
- 本檔用途：面試素材整理，不是正式履歷 final claim。

## 30 秒版本

我會把這條 flow 當成金流 callback consistency 的案例來講：三方 provider callback 進來後，payment 先做白名單與簽章驗證，再把 provider 狀態轉成內部訂單狀態，透過 XXL-MQ 非同步交給 consumer 處理。consumer 端會再做訂單狀態 guard，避免重複 callback 或 MQ retry 導致重複處理；payment 也會把 `billNo` 傳到 game lobby / center 作為跨系統追蹤鍵。提現失敗退款是最高風險分支，因為退款成功後如果再 retry，可能造成重複退款，所以 code 裡有針對退款例外後回 SUCCESS 的防護。

## 3 分鐘版本

這條 flow 的核心不是單純接 provider API，而是 provider callback、payment 訂單、玩家錢包三邊要收斂一致。

正常情境下，provider 打 `/pay/notify` 或 `/withdraw/notify` 到各 provider controller。controller 會解析 provider 欄位、檢查 IP 白名單、重算簽章、查 `payment_order` 並確認訂單還在 `WAIT` / `PROCESSING`。如果訂單已經是成功、拒絕、退回或異常，controller 會回 provider ack 並阻止再次處理。

驗證通過後，controller 不直接做所有錢包副作用，而是呼叫 `asynUpdateOrderStatus` 丟到 XXL-MQ。MQ consumer 收到後進入 `updateUserInfo`，再檢查一次訂單狀態。充值成功會呼叫 game lobby / center 做上分，再更新訂單與玩家業務欄位；提現成功主要更新訂單與通知；提現失敗會把錢退回玩家，更新訂單為異常並寫入失敗原因。

Senior 角度我會特別看三個點：第一，callback ack 與 MQ enqueue 不是同一個 transaction，produce 失敗只 log 會有 callback accepted but not processed 的窗口。第二，consumer 雖然有終態 guard，而且 `billNo` 會傳到下游餘額異動與 currency log，但還沒確認下游有查重或 unique key，所以不能宣稱 exactly-once。第三，提現失敗退款是高風險分支，code 裡有避免已退款後 MQ retry 重複退款的處理，這是很好的 owner discussion point。

## 可以安全放在面試的能力點

- 分析金流 callback 的 trust boundary：IP 白名單、簽章驗證、provider 狀態 mapping。
- 分析 money correctness：provider 狀態、payment order、玩家錢包三邊一致性。
- 分析至少一次 delivery 下的 idempotency：controller guard、consumer no-op、終態保護。
- 分析 cross-system correlation key：`billNo` / `billNos` 從 payment 傳到 game lobby / center 與 currency log。
- 分析 MQ retry 與補償：retryCount、consumer fail / success、提現失敗退款的重複退款防護。
- 分析 owner trade-off：ack 時機、outbox / inbox、callback log、對帳 job、人工補單。

## 不應誇大的說法

- 不說「我主導設計 payment callback 架構」。
- 不說「我修好 pay4z 重複退款」。
- 不說「整套金流已達 exactly-once」。
- 不說「我是 payment owner」。
- 不寫任何改善百分比或 production incident 結果，除非 Nick 補上 evidence。

## 可回答的面試題

### Q1：provider callback 重送怎麼避免重複入帳？

保守回答：

payment 在入口與 consumer 端都有訂單狀態 guard。入口會查 `payment_order`，只有 `WAIT` / `PROCESSING` 才繼續處理，成功 / 退回 / 異常等終態會直接回 provider ack 並停止。consumer 收到 MQ 後也會再檢查一次，不可處理就回 `MqResult.SUCCESS`。但我不會宣稱這已經是 end-to-end exactly-once，還要確認下游 game lobby 是否用 billNo 做 idempotency。

Step 4 補掃後，可以再補一句：payment 確實會把 `billNo` 以 `billNos` 傳到 game lobby / center，下游也會帶進玩家餘額異動與 currency log；但目前沒有看到同一 `billNo` 重送時會被下游 no-op 的明確 evidence，所以面試要把它說成 correlation / audit key，而不是已確認的 idempotency guarantee。

### Q2：提現失敗退款為什麼比充值成功更危險？

保守回答：

充值成功的主要風險是重複上分；提現失敗退款則是已扣款後要退回玩家，若退款成功但後續更新訂單或通知失敗，MQ retry 可能再跑退款流程，造成重複退款。這條 flow 的 code 有針對退款例外做防護：失敗場景 catch exception 後會把訂單標為需要人工處理並回 SUCCESS，避免已退款後再 retry。

### Q3：這條 flow 的 transaction boundary 在哪裡？

保守回答：

它不是單一 DB transaction。callback controller 驗證後丟 MQ，producer 失敗只 log；consumer 之後會呼叫外部 game lobby / center，再更新本地訂單與玩家欄位。外部 HTTP、DB 更新、MQ retry 是分段收斂，所以 owner 要設計 idempotency、對帳、人工補償與 observability。

### Q4：如果要改善，你會先改哪裡？

保守回答：

我會先補 durable event / outbox 或 callback inbox，讓 callback accepted 與後續處理可追蹤、可重放；再確認 game lobby 的 `billNos` 是否具備 idempotency。第二步會把 provider controller 的狀態 mapping 與 ack 規則集中化，避免每家 provider 寫法漂移。第三步才會談更完整的 reconciliation dashboard 或定時對帳。

### Q5：如果後台可以修訂單狀態，這算 reconciliation 嗎？

保守回答：

只能算 repair boundary 的一部分。Step 4 掃到 app_bi local HEAD 有用 `bill_no` 與月表更新 `payment_order` 狀態的入口，也有操作 log，但 app_bi 本機落後遠端 4 commits，而且修狀態不等於完整 reconciliation。完整 reconciliation 還要能對 provider statement、payment order、wallet / currency log 三邊，明確決定 source of truth。

## 可轉成履歷的保守草稿

以下只能在 Nick 補到本人參與 evidence 後再考慮放入正式履歷：

- 分析並整理金流 provider callback flow，聚焦三方 callback 驗證、訂單狀態轉移、XXL-MQ retry、玩家上分 / 提現退款與重複 callback 冪等風險。
- 針對提現失敗退款場景梳理重複退款 failure window，整理 callback ack、MQ retry、終態 guard 與人工補償邊界。

目前正式履歷不要放。
