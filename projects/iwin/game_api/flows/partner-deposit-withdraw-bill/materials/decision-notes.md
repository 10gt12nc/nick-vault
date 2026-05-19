# partner-deposit-withdraw-bill decision notes

更新時間：2026-05-19
Step：3
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Decision 1：Partner money API 要有外部 idempotency key

現況：

- `NewPay` / `Withdraw` 每次呼叫都由 `game_api` 產生 `yyyyMMdd-UUID` 的 `billNos`。
- 目前未看到 partner 外部訂單號欄位作為 idempotency key。
- timeout 後 partner 若重送同一 business request，可能變成新的 `billNos`。

建議：

- contract 要求 partner 帶 `merchantOrderNo` 或等價 request id。
- 本地 Mongo 以 `agentId + merchantOrderNo + type` 做唯一約束。
- 下游 GM command 的 `billNos` 也應可重試，不能每次重送都變新 side effect。

## Decision 2：訂單狀態要能表示 unknown，不只 success / exception

現況：

- `status=1` 新建。
- `status=0` 成功。
- `status=4` 異常。
- generic exception 後未看到狀態更新，可能留下 pending。

建議：

- 補 `unknown` 或 `gm_sent` 狀態，用於 GM timeout / response parse error / Mongo update failure。
- 對 aging pending / unknown 建 reconciliation job。
- partner 查單應能區分「處理中」、「成功」、「失敗」、「待人工確認」。

## Decision 3：`updCoin` 應依原 billNos 日期定位 collection

現況：

- `saveCoin` 寫 `coin_change_log_{today}`。
- `billNos` prefix 也是當天日期。
- `updCoin` 仍用當天日期，不從 `billNos` 解析日期。

風險：

- 跨日補償或延遲 update 時，可能更新錯 collection。
- 使用 upsert 時，若找不到原單，可能產生不完整 document。

建議：

- `updCoin` 從 `billNos` prefix 解析 collection date。
- 找不到原單時不要直接 upsert status-only doc；應記錄 reconciliation error。

## Decision 4：簽章防重放要以 `origin/k3s` 方向收斂

現況：

- main branch 的 30 秒 time window throw 被註解。
- main branch 會 info log API secret 物件內容。
- `origin/k3s` 啟用 time window、加 nonce、移除 secret log。

建議：

- 若 production 尚未部署 `origin/k3s` 安全性修正，應優先 cherry-pick 或重做同等修正。
- nonce key 應包含 agentId 與 sign，並確認 Redis TTL / setIfAbsent 語意。
- secret 不應出現在 info log。

## Decision 5：查單要標明 partial result 語意

現況：

- `queryBillInfos` 跨每日 collection 查詢。
- catch exception 後只 log，不往上丟。

風險：

- 某一天 collection 查詢失敗，partner 可能拿到部分資料但不知道不完整。
- count 可能是 partial count。

建議：

- 查單 response 加上 query range、成功 collection、失敗 collection 或 error code。
- 對 partner contract 定義 partial failure 是 fail whole request 還是回 partial。
- money reconciliation 查詢建議 fail closed，不要靜默 partial success。
