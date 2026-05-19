# partner-deposit-withdraw-bill interview notes

更新時間：2026-05-19
Step：3
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 面試主軸

這條 flow 可以用來講「外部 partner money API 如何串到內部 wallet side effect」。

核心不是 Controller 寫法，而是：

- partner sign / replay prevention
- 本地訂單與下游錢包的 transaction boundary
- idempotency key
- GM timeout / partial success
- 查單 reconciliation

## 常見追問

| 問題 | 保守回答 |
| --- | --- |
| `NewPay` 成功代表什麼？ | 代表 `game_api` 已寫本地 Mongo 訂單、GM deposit 呼叫成功、並成功把本地狀態改成 success。仍需確認下游 wallet log 與 billNos 去重語意。 |
| `Withdraw` 和 `NewPay` 最大差別？ | `Withdraw` 會帶 `withdrawType`，`withdrawType=1` 才要求 value；GM 回傳 value 後再除 money ratio 回給 partner。 |
| partner timeout 後重送怎麼辦？ | 目前看到 `billNos` 每次由系統產生 UUID，沒有足夠 idempotency evidence。正式設計應要求 partner order id，並在本地與下游雙層去重。 |
| GM 成功但 `updCoin` 失敗怎麼辦？ | 玩家錢包可能已異動，但 Mongo 仍 pending；需要 aging pending monitor 與 reconciliation，不能只靠查單狀態。 |
| 為什麼查單要看每日 collection？ | 訂單寫在 `coin_change_log_{yyyyMMdd}`，BillInfo 用 billNos 日期定位，BillList 用 start/end 推日期範圍。 |
| `origin/k3s` 對這條 flow 有什麼關係？ | 它補了 partner sign replay 防護：啟用 30 秒 time window、加 nonce、防止 secret log；但是否 production deploy 待確認。 |

## 5 分鐘講法草稿

我會先把 partner API 拆成三段：入口驗證、本地訂單、下游錢包。

入口驗證由 `ValidatedServiceImpl` 和 `PartnerLogServiceImpl#checkSign` 處理。參數會檢查 `agentId`、`accountId`、`time`，上分要求 `value`，下分要求 `withdrawType`，查單要求 `billNos` 或 `start/end`。sign 是用 Redis 裡的 API secret 算 MD5。這裡 main branch 的 time window throw 是註解掉的，`origin/k3s` 才補了真正的 30 秒檢查與 nonce 防重放。

本地訂單由 `saveCoin` 寫 Mongo `bi_log.coin_change_log_{date}`。上分是 `type=1`、`reason=201`，下分是 `type=2`、`reason=206`，初始 `status=1`。GM 成功後 `updCoin(status=0)`，業務 exception 則標 `status=4`。

下游錢包由 `GmIntfcComponent` 送 `GM_CMD_DEPOSIT` 或 `GM_CMD_WITHDRAW` 到 gameserver。這裡最重要的 boundary 是：Mongo insert、GM side effect、Mongo update 不是同一個 transaction。所以如果 GM timeout、response parse error、或 Mongo update 失敗，就會出現 partner 查單狀態與玩家實際餘額不一致。

我會建議 owner 先補 idempotency contract，例如 partner order id，再補本地唯一鍵與下游 billNos 去重；接著補 unknown 狀態與 reconciliation job，監控 pending aging order，而不是把所有 exception 都包成單一錯誤碼。

## Step 4 待補

- STAR 版本。
- 反問面試官版本。
- 依職缺調整成 Senior Backend / Platform Backend / System Owner 三種口吻。
- 更細 Q&A：跨日 update、查單 partial result、nonce TTL、secret log、generic exception pending。
