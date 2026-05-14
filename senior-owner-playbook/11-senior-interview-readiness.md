# Senior / 10 萬以上職缺面試準備檢查表

這份用來判斷目前是否已經可以穩定面試 Senior Java Backend / Platform Backend / System Owner 方向職缺。

先講清楚：

> 整理完 `nick-vault` 不等於已經穩上 10 萬以上職缺。

`nick-vault` 的價值是把準備路線、提示詞、履歷、自傳、case study 方法論整理乾淨。真正能不能打 Senior 面試，取決於你是否能把 3-5 條 production flow 講清楚，而且每條都有 code evidence、風險判斷、owner decision 與不誇大的履歷邊界。

## 目前狀態判斷

### 已完成

- Senior / Owner 核心定位已整理。
- 學習路線已整理。
- 高價值 flow inventory 已整理。
- Step 1-5 AI 提示詞已整理。
- 重複 flow 檢查規則已整理。
- 唯一履歷 master 已整理。
- 投遞用自傳已整理。
- 未來專案資料夾 `projects/` 已建立。

### 尚未完成

- `projects/` 尚未有任何完成的 production flow。
- 尚未有 `flow.md + materials/evidence.md` 的實際專案 case。
- 尚未完成 3-5 條可面試案例。
- 尚未把已完成 case 轉成 3 分鐘面試說法。
- 尚未依 evidence 最終更新履歷 bullet。

所以目前狀態是：

```text
準備系統已完成。
Senior 面試內容尚未完成。
```

## 能投 10 萬以上職缺前的最低門檻

至少完成以下 5 條中的 3 條：

1. `payment-provider-callback`
2. `transfer-wallet-transfer-in-out`
3. `bet-settlement` 或 `game-round-settlement`
4. `settled-bets-kafka` 或 `bet-record-request-log-mq`
5. `observability-pipeline` 或 `k3s-migration-track`

每條都必須有：

- `flow.md`
- `materials/evidence.md`
- `career-interview.md`
- 3 分鐘面試說法
- 5 個可能追問
- 履歷保守 bullet
- claim boundary

## 10 萬 Senior 面試必備能力

### 1. Production Flow 表達

你要能不用看稿講：

- 這條 flow 的業務目的
- 入口在哪裡
- code path 怎麼走
- DB / Redis / MQ / 外部 API 怎麼串
- 哪裡是 source of truth
- 哪裡只是 cache / projection / report

合格標準：

```text
面試官問「這條 flow 怎麼跑？」你能在 3 分鐘內講清楚。
```

### 2. Failure Window 判斷

你要能回答：

- callback 重送怎麼辦？
- timeout 後不能直接當失敗時怎麼辦？
- DB 成功但 MQ 失敗怎麼辦？
- MQ 成功但 consumer 失敗怎麼辦？
- retry 重入會不會重複副作用？
- 人工補償後怎麼對帳？

合格標準：

```text
面試官開始追 failure，你不是慌，而是能分狀態、分邊界、分補償策略。
```

### 3. Consistency / State Machine

你要能講清楚：

- 訂單狀態
- wallet 狀態
- round 狀態
- callback 狀態
- retry 狀態
- 終態是否可覆蓋
- 半完成狀態如何查詢與恢復

合格標準：

```text
你不是只說「加 transaction」，而是能說 transaction 邊界之外的問題。
```

### 4. Owner Decision

你要能說：

- 先修什麼
- 暫時不修什麼
- 為什麼
- 風險是什麼
- 如何驗證
- 如何 rollback
- 如何 monitoring

合格標準：

```text
你能做取捨，不只是列技術名詞。
```

### 5. Claim Boundary

你要能誠實回答：

- 哪些是你實際做過
- 哪些是你參與維護
- 哪些是你分析整理
- 哪些只是改善建議
- 哪些需要再補證據

合格標準：

```text
被問「這是你主導的嗎？」時，你能保守但不失分地回答。
```

## 目前履歷可投程度

### 可投方向

- Java Backend Engineer
- Backend Engineer
- Platform Backend Engineer
- Gaming / Payment / Wallet / Provider Integration Backend
- 偏 Senior 的 Backend 職缺

### 需要小心的方向

- 明確要求正式 Senior title 且要帶人
- 明確要求系統架構師經驗
- 明確要求完整主導金流 / 帳務架構
- 明確要求大規模 K8s / SRE ownership
- 明確要求金融等級帳務與稽核設計主導經驗

### 目前履歷語氣

目前履歷 master 是保守版，可以支撐投遞。但正式投遞前要依職缺調整標題與前 5 個 bullet，不要每個職缺都丟同一份。

## 面試前必做清單

面試前至少準備：

1. 一段 30 秒自我介紹。
2. 一段 3 分鐘 career summary。
3. 3 條 production case。
4. 每條 case 各 5 個追問。
5. 一份「我實際做過 / 參與 / 分析 / 待確認」邊界表。
6. 一份離職 / 轉職理由，不能抱怨公司。
7. 一份薪資說法，聚焦職責與市場，不要只講缺錢。

## 第一輪要做的 3 條 Case

建議先做：

1. `payment-provider-callback`
2. `transfer-wallet-transfer-in-out`
3. `settled-bets-kafka`

理由：

- 金流 callback 對 Senior 面試價值最高。
- Transfer wallet 最能講 state / timeout / compensation。
- Kafka flow 能補上 async / reliability / observability。

這三條完成後，再補：

4. `bet-settlement`
5. `observability-pipeline`

## 最誠實的結論

目前 `nick-vault` 已經整理到可以開始有效準備 Senior / 10 萬以上職缺。

但還不能說：

```text
已經穩能面試所有 10 萬以上 Senior 職缺。
```

比較準確的說法是：

```text
方向正確，系統已建立。
接下來只要完成 3-5 條 evidence-backed production case，
就有機會把自己從「一般 Java 後端」包裝成「高交易平台 / System Owner 型後端」。
```

真正的分水嶺不是文件數量，而是你能不能把每條 case 講到：

- flow
- state
- failure
- consistency
- trade-off
- evidence
- claim boundary
