# 2026-06-22 星期一重掃清單

狀態：候選待辦，不是自動開工授權。

目的：把 2026-06-20 前後討論到的投遞準備、基本功焦慮、flow 閱讀困難、git log 風險重建與糖蛙 JD 客製包集中成一份星期一可重掃清單。這份只負責「下次看什麼、怎麼判斷要不要補」，不直接改 `04 / 05 / 08 / 17`，也不直接改任何 flow。

## 非目標

- 不重掃全部 repo。
- 不新增泛用 Java / SQL / Redis / MQ 題庫。
- 不把基本功變成新主線。
- 不把 `git log` 的他人 commit 說成 Nick direct evidence。
- 不因 flow 難讀就重寫全部 `flow.md`。
- 不把 Team Lead JD 的管理說法覆蓋到通用履歷。

## 1. 焦慮與收斂狀態

已確認：

- Nick 的焦慮點包含：基本功忘記、怕面試考、flow 太難讀、擔心是否需要一個人懂完整大系統。
- KB 目前規則是：先處理收斂與焦慮，不用新 backlog 回避問題。
- 目前方向不是再掃全部 repo，而是投遞、讀熟主力 flows、練口說、依 JD 補洞。

星期一重掃時要確認：

- 是否仍維持 `70 / 20 / 10`：70% production case、20% 基本功、10% coding test。
- 是否有面試邀約或新 JD；若有，才做 JD-specific 補洞。
- 若 Nick 只是焦慮，先回到「已足夠投遞 / 可選補強 / 暫不建議」三段式，不新增大規模任務。

## 2. 基本功最小掃描

目前結論：

```text
工作遇到再查可以；面試不能完全等遇到再查。需要一輪面試最低防穿透基本功。
```

星期一只掃 12 題，不擴張：

1. `@Transactional` 什麼時候 rollback？
2. self-invocation 為什麼 transaction 會失效？
3. DB 成功但 MQ 發送失敗怎麼辦？
4. MQ 重複消費怎麼處理？
5. Redis lock 要注意什麼？
6. Redis 和 DB 不一致怎麼辦？
7. MySQL index 怎麼設計？
8. `EXPLAIN` 先看什麼？
9. callback 重送如何避免重複上分？
10. timeout 不能確定成功或失敗時怎麼辦？
11. batch 跑一半失敗怎麼重跑？
12. AI 產出的 code 要 review 哪些 production 風險？

掃描方式：

- 每題綁一條 production case。
- 每題練 60-90 秒，不追求教科書完整定義。
- 若答不出，再補 `19-interview-coaching-question-bank.md` 對應主題，不新開泛用題庫。

## 3. Flow 聊天版 / 面試人話版

目前結論：

- `flow.md` 是研究報告與 evidence，不是面試聊天稿。
- `career-interview.md` 已有面試素材，但仍可能偏整理稿。
- 不建議新增每條 flow 的新檔，先在主力 flow 的 `career-interview.md` 補 `面試聊天版` 區塊即可。

建議格式：

```text
30 秒版
90 秒版
3 分鐘版
面試官追問時怎麼接
不要這樣講
```

星期一優先檢查三條：

1. `payment-provider-callback` / `payment-order-provider-request`
2. `slot-bet-settle-rollback` 或 `third-party-transfer-in-out`
3. `proxy-user-data-report-projection` 或 `request-log-rabbitmq-async`

已先口頭示範：

- `slot-bet-settle-rollback` 的 30 秒 / 90 秒 / 3 分鐘聊天版。

星期一判斷：

- 若 Nick 覺得示範口吻可用，再挑 1 條正式補進對應 `career-interview.md`。
- 若覺得太長，先只補 `30 秒版 + 90 秒版 + 不要這樣講`。

## 4. Git Log / Diff 風險重建

目前結論：

- KB 已有 `git log` / path-specific history / important diff 的 evidence 規則。
- 很多 flow 的 `materials/evidence.md` 已實際記錄 path-specific history、代表 commit 與重要 diff。
- 目前較缺的是把這件事整理成面試可講的能力，而不只是 claim evidence。

建議面試能力名稱：

```text
Git History Debugging / Risk Reconstruction
```

可講能力：

- 用 `git log --all --author` 找 Nick / `10gt12nc` direct evidence。
- 用 path-specific history 看某段 code 當初為何修改。
- 用 important diff 判斷當時是在修 bug、補 idempotency、處理 timeout，還是修資料一致性。
- 區分 Nick direct evidence、主管 / team context、current behavior context。
- 避免把他人 commit 說成自己的成果。

建議人話版：

```text
我接手既有系統時，不會只看目前程式碼。我會一起看 git history 和重要 diff，確認這段邏輯當初為什麼改、是修 bug、補 idempotency、處理 provider timeout，還是修資料一致性問題。這樣比較能避免只看 current code 卻誤判風險。
```

星期一判斷：

- 若要正式回填，優先補到 `04-interview-casebook.md` 或 `19-interview-coaching-question-bank.md`。
- 不建議為此新增大量新文件。

## 5. 12 條 Flow 閱讀組

目前正式 KB 說法：

- 主力 1-7：投遞前優先讀熟。
- 8-10：Senior / Platform 追問加強。
- 11-12：投遊戲 / slot / provider JD 時加讀的差異化 flows。

### 先讀熟 1-7

1. `payment-order-provider-request`
2. `payment-provider-callback`
3. `third-party-transfer-in-out`
4. `slot-bet-settle-rollback`
5. `transfer-wallet-money-in-out`
6. `proxy-user-data-report-projection`
7. `daily-game-data-summary`

### 補到 8-10

8. `bet-record-sharding-schema-route`
9. `db-partition-job-report-routing`
10. `gameserver-phased-rollout`

### 遊戲 / slot / provider JD 加讀 11-12

11. `fixed-multi-bet-currency-math-core-compatibility`
12. `rtp-reel-strip-simulation-validation`

糖蛙 JD 優先順序：

```text
1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 8 -> 11 -> 12
```

## 6. 近期 JD 與投遞規劃

已建立客製包：

- `applications/tangfrog-backend-team-lead-java-2026-06-18/`
- `applications/tangfrog-senior-java-backend-2026-06-18/`
- `applications/weida-senior-java-backend-2026-05-26/`

目前優先順序：

1. 糖蛙 Backend Team Lead：測上限，定位 `Senior Java Backend / Hands-on Backend Tech Lead Candidate`，不誇大正式管理 3-8 人。
2. 微達 Senior Java Backend：通用 Senior / Platform Backend market check。
3. 糖蛙 Senior Java Backend：偏 Senior IC / Game Backend，薪資上限 `100,000` 偏保守，可作保底 / market check。

星期一確認：

- 是否已投遞。
- 是否有 HR 回覆。
- 是否需要針對某份 JD 進入口說練習。
- 若沒有回覆，不改主線，持續投遞 + 練 3 條主力 case。

## 7. 星期一建議執行順序

1. 先看是否有面試邀約 / HR 回覆。
2. 若有明確面試：讀對應 `applications/*/interview-qa.md`。
3. 若沒有面試：做基本功 12 題中的前 3 題。
4. 補一條 flow 的聊天版，優先 `slot-bet-settle-rollback` 或 payment callback。
5. 視 Nick 狀態決定要不要把 `Git History Debugging / Risk Reconstruction` 回填到 `04` 或 `19`。

## 8. 收口標準

這份星期一重掃清單完成，不代表所有 flow 完成。只要達到以下狀態即可收口：

- Nick 知道今天要讀哪 1-2 條 flow。
- Nick 知道今天要補哪 2-3 題基本功。
- Nick 不再把「基本功忘記」解讀成要重學全部 Java。
- Nick 能用一句話說明 `git log` 如何支撐風險重建。
- 若要改 KB，只改最小必要檔案，不開大規模重構。
