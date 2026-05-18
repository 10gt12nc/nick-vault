# interview

## Senior 追問

| 追問 | 回答 |
| --- | --- |
| 為什麼提款 flow 比充值更危險？ | 提款會先扣玩家錢，再等待 provider 代付與 callback。中間任何一段斷掉，都會讓玩家餘額、payment order 與 provider 狀態不一致。 |
| 這條 flow 的 source of truth 是什麼？ | payment 內部訂單狀態看 `payment_order`；玩家餘額在 game lobby / center；provider 終態看三方 callback / 查單。三者不是同一個 transaction。 |
| 扣分與建單順序怎麼看？ | code 顯示先 `gmDownScore` 成功，再 insert `payment_order`。這讓扣分成功但建單失敗成為重要 failure window。 |
| 自動審核條件在哪裡？ | `getAutoMerchant` 先看商戶金額 / 時間；`checkAutoWithdraw` 再看玩家自動出款開關、玩家層級、金額上下限、今日充值、今日打碼比例。 |
| provider 同步下單成功是不是終態？ | 不是。多數只代表 provider accepted，真正終態還要 callback 或查單。 |
| provider 下單失敗怎麼退錢？ | provider service 呼叫 `asynUpdateOrderStatus(ERROR, WITHDRAW)`，notify consumer 進 `updateUserInfo`，把 `tradeType` 設 `WITHDRAWBACK`，呼叫 `upperDeposit` 退回玩家。 |
| 怎麼防重複退款？ | `updateUserInfo` 只允許 `WAIT` / `PROCESSING` 處理；終態重送 no-op。退款 catch 裡也有防 MQ retry 再次退款的保護。下游 `billNo` 去重仍待確認。 |
| 最需要補的 owner evidence？ | DB unique key、下游 billNo 去重、扣分成功建單失敗補償、provider no callback 對帳、MQ produce failure alarm。 |

## Lead / Architect 追問

| 追問 | 回答 |
| --- | --- |
| 如果你要重構，第一步改什麼？ | 先不動 provider 細節，先補交易事件 audit / outbox 或 pending record，讓扣分前後至少有可追蹤狀態，再補 reconciliation。 |
| 你會怎麼設計 idempotency？ | 以 `billNo` 作 payment / center / provider 的 trace key；payment order 要 unique；center money mutation 要用 billNo 去重；provider callback 要有 raw event / callback idempotency guard。 |
| MQ produce 失敗怎麼辦？ | 不能只 log。要有可查的 failed event、alarm、重送機制，或把狀態變更與事件發送改成 outbox pattern。 |
| `PROCESSING` 卡住怎麼辦？ | 需要 aging monitor + provider query job + 人工 repair SOP，並明確區分 unknown、failed、success，避免未知狀態直接退款。 |
| 怎麼避免自動審核錯放？ | 自動條件要可審計，包含命中商戶、命中時間、金額區間、玩家層級與打碼判斷；不符時轉人工，不能靜默失敗。 |
| 如果 provider 查單也是 unknown？ | 不退款、不標成功，進人工 reconciliation；要保存 provider request / callback / query raw evidence，等 provider 終態或人工確認。 |
| 如果要補監控，先補哪三個？ | `PROCESSING` aging、notify MQ retry / fail、provider callback delay。這三個最直接對應卡單、重試與終態延遲。 |

## Step 5 面試版

本檔可以作為面試稿，但不是正式履歷 claim。Step 5 判定不更新正式履歷 / 自傳；面試時應保守說「我分析 / 梳理過這類提款 flow」，不能說 Nick 主導、設計或修復。

## 口說版本

### 90 秒版本

這條提款 flow 我會從 money correctness 看。玩家提款時，payment 先檢查玩家、綁定、黑名單、提款設定、待審單與打碼目標；通過後先呼叫 game lobby `WITHDRAW` 扣分，扣分成功才建 `payment_order`。後面自動審核 job 會掃 `is_auto_review=1` 且 `WAIT` 的訂單，再依玩家層級、金額、今日充值與打碼比例決定是否出款。出款時訂單改 `PROCESSING`，再呼叫 provider。

真正的難點是 provider accepted 不等於成功，最終要看 callback 或查單。失敗時走 `asynUpdateOrderStatus` / `updateUserInfo`，呼叫 game lobby `DEPOSIT` 退回玩家。我要特別看扣分成功建單失敗、MQ produce 失敗只 log、provider no callback、重複 callback 是否重複退款，以及下游 `billNo` 是否真的去重。

### 風險分級

| 風險 | 等級 | 原因 |
| --- | --- | --- |
| 扣分成功但建單失敗 | 高 | 玩家餘額已變，但 payment 缺訂單 source of truth |
| provider accepted no callback | 高 | 訂單卡 `PROCESSING`，不能直接成功或退款 |
| 重複 callback / MQ retry | 高 | 若終態 guard 或下游去重不足，會重複退款 |
| MQ produce fail only log | 中高 | 狀態已變但事件未送出，需 alarm / outbox |
| 自動審核條件不符 | 中 | 會轉人工，風險是 backlog 和 remark 不完整 |

## 可說案例

> 我會先把提款 flow 拆成三個 source of truth：payment order、game lobby 玩家餘額、provider payout status。接著看每個跨系統邊界：扣分成功後才建單、訂單從 WAIT 轉 PROCESSING、provider accepted 但 callback 未回、失敗退款。這幾個地方都不是單一 transaction，所以 owner 要補 idempotency、retry、reconciliation 和人工補償。

## 不要說

- 不要說已完成 Level 3 逐檔逐行。
- 不要說已確認所有 provider 一致。
- 不要說已確認下游強去重。
- 不要說 Nick 本人修過或主導這條 flow。

## 下一步

回到 `payment` project candidate ranking，選下一條未完成 flow。
