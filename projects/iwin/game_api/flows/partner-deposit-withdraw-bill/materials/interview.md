# partner-deposit-withdraw-bill interview notes

更新時間：2026-05-19
Step：5
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Step 5 結論

這條 flow 已可作 code-backed 面試案例。主軸是：

- 外部 partner money API 的 contract。
- 本地 Mongo 訂單與 gameserver 錢包 side effect 的 transaction boundary。
- partner 重送與 idempotency。
- GM timeout / partial success / pending order reconciliation。
- 查單分日 collection 的 partial result 風險。

不更新正式履歷 / 自傳。Step 5 重新 fetch source repo 並重跑 partner path-specific history 後，仍未看到 Nick / `10gt12nc` direct path evidence。

## 面試主軸

這條 flow 可以用來講「外部 partner money API 如何串到內部 wallet side effect」。

核心不是 Controller 寫法，而是：

- partner sign / replay prevention
- 本地訂單與下游錢包的 transaction boundary
- idempotency key
- GM timeout / partial success
- 查單 reconciliation

## 3 分鐘講法

這條 flow 是 `game_api` 對 partner 開的上下分與查單 API，主要入口是 `NewPay`、`Withdraw`、`BillInfo`、`BillList`。

`NewPay` 和 `Withdraw` 都會先做參數檢查與 sign 驗證，再用 `agentId:accountId` 找內部玩家，產生 `yyyyMMdd-UUID` 格式的 `billNos`。接著 `saveCoin` 會先寫 Mongo `bi_log.coin_change_log_{date}`，初始 `status=1`。真正的錢包異動不是本地 DB 做，而是透過 `GmIntfcComponent` 送 `GM_CMD_DEPOSIT` 或 `GM_CMD_WITHDRAW` 到 gameserver。GM 回成功後，`updCoin` 再把 Mongo 狀態改成 `0`。

Senior 角度我會先看 transaction boundary。Mongo insert、GM wallet side effect、Mongo status update 是三段，不在同一個 transaction。這代表 GM 成功但 Mongo update 失敗時，玩家餘額可能已改，但 partner 查單仍看到 pending；GM timeout 但其實下游成功時，也不能直接重送新 billNos，否則可能重複上下分。

所以我會建議先補 idempotency contract：partner 要帶 merchant order id，本地以 `agentId + merchantOrderId + type` 做 unique，下游也要用 billNos 去重。再來補 unknown state 與 reconciliation，監控 aging pending order，用 wallet / GM log 收斂狀態。查單也要定義 partial failure 語意，不能跨多天 collection 有一天查失敗卻回一份看似完整的資料。

## 常見追問

| 問題 | 保守回答 |
| --- | --- |
| `NewPay` 成功代表什麼？ | 代表 `game_api` code path 上已寫本地 Mongo 訂單、GM deposit 呼叫成功、並成功把本地狀態改成 success。真正錢包 source of truth 仍要看 gameserver wallet log。 |
| `Withdraw` 和 `NewPay` 最大差別？ | `Withdraw` 會帶 `withdrawType`，`withdrawType=1` 才要求 value；GM 回傳 value 後再除 money ratio 回給 partner。 |
| partner timeout 後重送怎麼辦？ | 目前看到 `billNos` 每次由系統產生 UUID，retry-safe evidence 不足。正式設計應要求 partner order id，並在本地與下游雙層去重。 |
| GM 成功但 `updCoin` 失敗怎麼辦？ | 玩家錢包可能已異動，但 Mongo 仍 pending；需要 aging pending monitor 與 reconciliation，不能只靠查單狀態。 |
| GM timeout 但下游其實成功怎麼辦？ | 應標 unknown，查 GM / wallet log 後收斂，不應直接當失敗，也不應用新 billNos 重送。 |
| 為什麼查單要看每日 collection？ | 訂單寫在 `coin_change_log_{yyyyMMdd}`，BillInfo 用 billNos 日期定位，BillList 用 start/end 推日期範圍。 |
| 查單可能 partial 嗎？ | 可能。`queryBillInfos` 逐日查 collection，catch exception 後只 log；若某天查失敗，partner 可能拿到不完整結果。 |
| `origin/k3s` 對這條 flow 有什麼關係？ | 它補了 partner sign replay 防護：啟用 30 秒 time window、加 nonce、防止 secret log；但是否 production deploy 與 Nick 是否參與仍待確認。 |

## Lead / Architect 追問

| 追問 | 回答方向 |
| --- | --- |
| success contract 怎麼定？ | 要先定義 partner API success 是「GM accepted」還是「wallet applied + local order success」。不同 contract 決定 timeout、查單與 reconciliation 語意。 |
| source of truth 是誰？ | 玩家餘額應以 gameserver wallet / wallet log 為準；Mongo order 是 partner 查單與 reconciliation projection，不應單獨當錢包真相。 |
| idempotency key 放哪裡？ | 外部用 merchant order id，本地唯一鍵用 `agentId + merchantOrderId + type`，下游 GM / wallet log 用 billNos 去重。 |
| pending aging 怎麼處理？ | 建 job 掃 `status=1` 超過 SLA 的訂單，查 GM / wallet log，能確認成功就補 success，確認失敗就補 failed，無法確認就保留 unknown / 人工處理。 |
| 跨日 collection 怎麼避免更新錯？ | `updCoin` 應從 `billNos` prefix 解析原 collection date，而不是用當天日期；找不到原單時不要 upsert status-only doc。 |
| sign replay 防護怎麼做穩？ | time window + nonce + Redis setIfAbsent TTL；nonce key 應包含 agentId 與 sign，secret 不進 info log。 |

## 反問面試官

- 你們 partner API 的 success contract 是同步錢包完成，還是 accepted 後用查單收斂？
- 外部 merchant order id 是強制欄位嗎？重送語意怎麼定？
- 下游 wallet 是否支援同 billNos idempotent replay？
- pending / unknown order 目前是人工查，還是有 reconciliation job？
- 查單 API 面對 partial result 是 fail whole request，還是回 partial 並標示？

## 不同職缺講法

Senior Backend：

- 聚焦 API contract、idempotency、exception branch、Mongo 狀態與查單正確性。

Platform Backend：

- 聚焦跨系統 trace key、GM command contract、reconciliation pipeline、observability 與 replay-safe API。

System Owner：

- 聚焦 source of truth、success contract、補償責任、客服查帳流程與風險溝通。

## Step 5 claim gate

本輪已完成單條 flow claim gate，不做完整 `game_api` project consolidation。

判定：

- partner flow 本身可作 code-backed 面試素材。
- 目前不能寫成 Nick 真實開發過。
- 若 Nick 本人未來確認參與此 flow，要標成「本人確認，待 commit / ticket 補強」，並再回頭更新 claim boundary。
- 後續 `game_api contribution claim consolidation` 已完成；本 flow 仍維持 code-backed 面試素材，不更新正式履歷。

下一步：

```text
- 歷史下一步已完成：iwin_gameserver center-http-deposit-withdraw 已完成到 Step 5；目前沒有預設下一步，請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
```
