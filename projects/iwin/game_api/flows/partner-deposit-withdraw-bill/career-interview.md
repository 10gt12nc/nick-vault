# partner-deposit-withdraw-bill career interview

更新時間：2026-05-19
Step：5
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 面試定位

這條 flow 適合當作「外部 partner money API 到內部錢包 side effect」的 code-backed 面試案例。它能展示我會怎麼拆 partner sign、idempotency、Mongo 訂單狀態、GM command、查單 reconciliation 與 failure window。

這不是正式履歷 claim。Step 5 重新 fetch 並重跑 path-specific history 後，仍沒看到 Nick / `10gt12nc` 直接修改此 partner flow，所以只能說「我分析過 / 我會這樣設計與排查」，不能說「我開發 / 主導」。

## 一句話版本

我分析過 `game_api` 的 partner 上分 / 下分 / 查單 flow：partner request 驗簽後，`game_api` 先寫 Mongo 訂單，再呼叫 gameserver GM command 做錢包異動，最後更新訂單狀態；核心風險是 partner 重送、GM timeout、Mongo pending 與查單 reconciliation。

## 30 秒版本

這條 flow 有四個入口：`NewPay`、`Withdraw`、`BillInfo`、`BillList`。上分 / 下分會先驗參數與 sign，找玩家帳號，產生 `billNos`，寫 Mongo `coin_change_log_{yyyyMMdd}`，狀態先是 `1`，再呼叫 `GM_CMD_DEPOSIT` 或 `GM_CMD_WITHDRAW`。GM 成功後把 Mongo 狀態改成 `0`。

我會特別看三件事：第一，partner timeout 重送時有沒有外部 idempotency key；第二，GM 成功但 Mongo 更新失敗時怎麼查；第三，查單跨每日 collection 時是否可能 partial result。這條目前是 code-backed 面試素材，不作正式履歷 claim。

## 2 分鐘版本

Partner 上下分 flow 不是單純 Controller 呼叫 Service。它跨了外部 partner request、`game_api` 本地 Mongo 訂單、gameserver 錢包 side effect 與查單 API。

入口在 `PartnerController`，參數檢查在 `ValidatedServiceImpl`。`NewPay` 要 `value`，`Withdraw` 要 `withdrawType`，查單要 `billNos` 或 `start/end`。簽章由 `PartnerLogServiceImpl#checkSign` 做，main branch 有 30 秒 time window 判斷但 throw 被註解；`origin/k3s` 才補了真正的 time window、nonce 與移除 secret log。

上分時，`PartnerServiceImpl#newPay` 產生 `yyyyMMdd-UUID` 的 `billNos`，把 partner 金額依 money ratio 轉成遊戲內分值，先用 `saveCoin` 寫 Mongo 訂單 `status=1`，再送 `GM_CMD_DEPOSIT` 到 gameserver，成功後 `updCoin(status=0)`。下分是同樣模式，只是送 `GM_CMD_WITHDRAW`，並依下游回傳 value 換回 partner 可讀金額。

Senior 角度的重點是 transaction boundary：Mongo insert、GM side effect、Mongo update 不是同一個 transaction。若 GM 已成功但 `updCoin` 失敗，partner 查單可能看到 pending，但玩家錢包已變；若 partner timeout 後重送，而系統每次都產生新 UUID，可能形成重複上下分。因此我會要求 partner order id / idempotency key、本地唯一鍵、下游 billNos 去重、unknown 狀態、aging pending monitor 與 reconciliation job。

## 5 分鐘版本

我會用三層來講這條 flow：入口契約、本地訂單、下游錢包。

第一層是入口契約。Partner 呼叫 `/api/NewPay`、`/api/Withdraw`、`/api/BillInfo`、`/api/BillList`。`ValidatedServiceImpl` 會先檢查 `agentId`、`accountId`、`time`，再看不同 API 的必要欄位。簽章用 Redis 裡的 API secret 算 MD5。這裡我會先檢查 replay prevention，因為 main branch 的 time window throw 被註解，`origin/k3s` 才補了 30 秒時窗、nonce 與 secret log 移除。

第二層是本地訂單。上分 / 下分都會先寫 Mongo `bi_log.coin_change_log_{date}`，欄位包含 `accountId`、`agentId`、`value`、`billNos`、`reason`、`time`、`status`、`type`。`status=1` 表示新建，GM 成功後改 `status=0`，業務 exception 改 `status=4`。這裡的風險是 generic exception 沒有看到狀態更新，可能留下 pending。

第三層是下游錢包。真正玩家餘額不是 `game_api` 改，而是 `GmIntfcComponent` 送 `GM_CMD_DEPOSIT` 或 `GM_CMD_WITHDRAW` 到 gameserver。這意味著本地 Mongo 狀態和玩家錢包是 eventual consistency，不是同一個 transaction。

如果我要 owner 這條 flow，我會先定 success contract：`NewPay` 成功到底代表「GM accepted」還是「wallet applied + local order success」？接著補 partner idempotency contract：partner 必須帶 merchant order id，本地用 `agentId + merchantOrderId + type` 去重，下游也要能用 billNos 去重。第三步補 reconciliation：掃 aging pending / unknown，查 gameserver wallet log，再把本地狀態補到一致。最後補查單 partial failure 語意，避免跨日 collection 有一天查失敗卻回給 partner 一份看似完整的結果。

## STAR 版本

Situation：有一條 partner API 需要支援外部系統對玩家上分、下分與查單，涉及外部 request、本地訂單、gameserver 錢包與 Mongo 分日表。

Task：我的分析目標是確認這條 money flow 的資料邊界、失敗窗口與可面試講法，並判斷能否作履歷 claim。

Action：我從 Controller、validation、sign、PartnerService、Mongo order、GM command、BillInfo / BillList 查單一路追到 path-specific history 與 `origin/k3s` diff，整理出 transaction boundary、idempotency 缺口、generic exception pending、跨日 update collection 與查單 partial result 風險。

Result：這條 flow 可作 Senior Backend 面試案例，用來講 partner money API consistency、idempotency 與 reconciliation。但因未看到 Nick / `10gt12nc` direct path evidence，目前不寫入正式履歷，也不說自己開發或主導。

## Step 5 claim gate

結論：保留為 code-backed 面試素材，不寫入正式履歷 / 自傳。

可用：

- 「我分析過 partner 上分 / 下分 / 查單 flow，能拆出 API contract、Mongo order、GM command 與 reconciliation 風險。」
- 「我會優先補 partner merchant order id、unknown state、pending aging monitor 與查單 partial failure contract。」

不可用：

- 「我開發了 partner 上下分。」
- 「我主導 partner API / wallet reconciliation。」
- 「我設計或修復 `origin/k3s` 的 sign replay prevention。」

## Senior Backend 口吻

我會先確認 partner API 的 idempotency contract。`game_api` 目前看到的是每次產生新的 `yyyyMMdd-UUID` billNos；如果 partner timeout 重送同一筆 business request，沒有外部 order id 就很難判斷是不是重複上下分。我的優先順序會是：partner order id、本地唯一鍵、下游 billNos 去重、unknown 狀態與 reconciliation。

## Platform Backend 口吻

這條 flow 的平台價值在 contract 與 observability。外部 partner 只看 API response 和查單，但內部有 Mongo order、GM request、gameserver wallet log 三種視角。平台層應該提供跨層 trace key，例如 `billNos` / merchant order id，並監控 pending aging、status=4、GM timeout 和查單 partial failure。

## System Owner 口吻

Owner 要先決定 success contract 與補償責任。若 GM 已扣分但本地訂單沒改成功，客服、partner、錢包帳本誰是 source of truth？我會把這類狀態標成 unknown，不直接回成功或失敗，再用 reconciliation 查 wallet log 收斂，避免 partner 看到錯誤終態。

## 可說

- code-backed 分析過 `game_api` partner 上分 / 下分 / 查單 flow。
- 能說明 `NewPay` / `Withdraw` 如何經過 validation、sign、Mongo order、GM command 與 status update。
- 能說明 `BillInfo` / `BillList` 如何查每日 collection。
- 能指出 partner 重送、GM timeout、Mongo pending、跨日 update、查單 partial result 與 sign replay 風險。
- 能提出 idempotency、unknown state、reconciliation、observability 改造方向。

## 不可說

- 我開發了 partner 上分 / 下分 flow。
- 我主導 partner API / wallet / reconciliation。
- 我修復了重複上下分或 production 錯帳。
- 我設計 `origin/k3s` 的 nonce / replay prevention。
- 這條可以直接寫進正式履歷。

## 面試官可能追問

| 追問 | 回答方向 |
| --- | --- |
| `NewPay` 回 success 就代表玩家一定上分了嗎？ | 只能說 code path 上 GM 呼叫成功且本地 `updCoin` 成功；真正 source of truth 仍要看 gameserver wallet log。 |
| partner 重送怎麼防？ | 需要 partner merchant order id + 本地 unique key + 下游 billNos 去重；目前每次 UUID 不足以支撐 retry-safe。 |
| GM timeout 但其實成功怎麼辦？ | 標 unknown，進 reconciliation 查 wallet / GM log，不應直接當失敗或重送新 billNos。 |
| 查單為什麼可能不準？ | 它查 Mongo 分日 collection；如果跨日 collection 部分查失敗或 `updCoin` 更新錯日期 collection，就可能 partial / stale。 |
| `origin/k3s` 修了什麼？ | 補 time window throw、nonce、防止 secret log；但 production deploy 與 Nick 貢獻仍待確認。 |

## 下一步建議

```text
iwin game_api contribution claim consolidation
```
