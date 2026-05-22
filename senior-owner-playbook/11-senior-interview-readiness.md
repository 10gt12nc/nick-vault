# Senior / 10 萬以上職缺面試準備檢查表

這份用來判斷目前是否已經可以穩定面試 Senior Java Backend / Platform Backend / System Owner 方向職缺。

先講清楚：

> 整理完 `nick-vault` 不等於已經穩上 10 萬以上職缺。

`nick-vault` 的價值是把準備路線、提示詞、履歷、自傳、case study 方法論整理乾淨。真正能不能打 Senior 面試，最低取決於你是否能把 3-5 條主力 production flow 講清楚，而且每條都有 code evidence、風險判斷、owner decision 與不誇大的履歷邊界；完整證據包則要擴到 8-10 條可切換使用的 production flow。

## 對標資深的整理結束點

整理不是無限做下去。對標 Senior Java Backend / Platform Backend 的完成標準是：

1. 履歷主成果夠用：3-5 個 project-level claim 可保守放履歷。
2. 面試案例夠用：8-10 條 production flow 能講 3 分鐘，且能被追問 state、failure、idempotency、retry、observability、owner decision。
3. 邊界夠乾淨：每個 claim 都知道哪些是真實開發過、哪些是 code-backed 分析、哪些不可誇大。
4. 投遞包完成：`05-resume-master-zh.md`、`08-application-autobiography-zh.md`、`04-interview-casebook.md`、`17-salary-negotiation.md` 與最新 claim 對齊。

口徑拆分：

- `3-5 條主力 flow`：最低投遞 / 面試門檻，必須練到能穩定口說與抗追問。
- `8-10 條 production flow`：完整 Senior / Platform Backend 證據包，用來覆蓋不同職缺與不同追問方向。

達到這個標準後，就不應再平均掃所有 repo。下一階段應改成：

- 投履歷。
- 練 3 分鐘 case 和追問。
- 依職缺補洞。
- 面試後回填被追問打穿的缺口。

Backlog 永遠會存在，但 backlog 不等於必做。

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
- 已有 `app_bi` 四條完成到 Step 5 的 flow 作為入門分析 case：`point-control-admin-operation`、`admin-config-redis-sync`、`daily-game-record-summary`、`game-round-record-query`。但它們目前仍屬於後台 / BI / control plane 分析素材，Nick 個人貢獻待確認，不足以作為 10 萬 Senior 面試主力 case。
- 已有多條 iwin flow 轉成保守面試素材或完成 claim gate，例如 `payment-provider-callback`、`payment/withdrawal-auto-review-refund`、`payment/payment-order-provider-request`、`payment/manual-order-review-repair`、`payment/payment-channel-config-selection`、`game_api/coupon-redeem-credit-grant`、`game_job/daily-game-data-summary`、`game_job/third-party-record-mongo-backup`、`game_job/coin-flow-batch-projection`、`game_job/online-payment-data-cleaning`、`game_job/partition-table-creation`、`third_games_api/gsc-transfer-bet-settle-rollback`、`iwin_gameserver/third-party-transfer-in-out`、`k3s-deploy/gameserver-phased-rollout`。`payment` 已完成 project contribution claim consolidation，可用「參與 provider 對接、維護與金流後台修補」口徑；其他 project 仍要等 Step 2 定義的本批代表 flows 完成 Step 5 後，才做完整 contribution consolidation，不能只靠單條 flow Step 5 直接升級成履歷主張。

### 尚未完成

- 尚未完成 3-5 條同時具備 high-value backend depth、完整 evidence、claim boundary 與 Nick 本人 evidence 的主力 production case，例如 payment callback、wallet transfer、Kafka settlement、game settlement。
- 尚未完成 3-5 條可作為 Senior 面試主力的後端案例；目前已有素材，但多數仍停在 `專案存在 / code-backed` 或 `分析素材 / learning-only`。
- 尚未把 3 條主力後端 case 打磨成可穩定口說、可抗追問的 3 分鐘版本。
- 已有部分 evidence-backed 履歷 bullet，但仍需要繼續收斂 3-5 條可抗追問的主力 production case，並對各 project 分開做 contribution consolidation。

所以目前狀態是：

```text
準備系統已完成。
已有 app_bi 入門 case、多條 iwin 保守面試素材與 payment project-level 履歷 claim。
Senior 面試主力內容仍需收斂到 3-5 條 evidence-backed production case。
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

### 10 萬以上現實判斷

可以投 10 萬以上職缺，也可以把月薪 `100,000` 當底線，但目前不能說「穩拿」。比較準確的定位是：

```text
有資格去談 10 萬以上，但要用 production case 證明。
```

需要避免兩種極端：

- 太自卑：把自己壓成一般 4 年 CRUD Java 後端，只敢填 `90,000` 以下。
- 太美化：把整理過的 flow、AI-assisted workflow 或 code-backed analysis 說成完整主導成果。

目前可以主打的是：Java Backend / Platform Backend、金流 / provider 串接、MQ / batch、legacy takeover、AI-assisted engineering workflow。真正能不能談到 `115,000-135,000`，取決於面試時能否把 3 條以上 production flow 講清楚。

目前月薪 `80,000` 不應被當成能力上限。它比較像目前公司 / 組織 / 職級 / 薪資帶的定價，不是外部市場的最終定價。是否真的能脫離 8 萬錨點，要靠外部投遞與面試驗證：

- 沒面試：履歷定位或職缺選擇要修。
- 有面試但被追問打穿：flow 還不夠熟。
- 有 offer 接近 `100,000-120,000`：代表目前薪資主要是組織定價問題，不是能力只能到 8 萬。

## 面試前必做清單

面試前至少準備：

1. 一段 30 秒自我介紹。
2. 一段 3 分鐘 career summary。
3. 3 條 production case。
4. 每條 case 各 5 個追問。
5. 一份「我實際做過 / 參與 / 分析 / 待確認」邊界表。
6. 一份離職 / 轉職理由，不能抱怨公司。
7. 一份薪資說法，聚焦職責與市場，不要只講缺錢；目前統一維護在 `17-salary-negotiation.md`。

## 第一輪要做的 3 條 Case

建議先做：

1. `payment-provider-callback`
2. `third-party-transfer-in-out` / `transfer-wallet-transfer-in-out`
3. `daily-game-data-summary` / `third-party-record-mongo-backup`

理由：

- 金流 callback 對 Senior 面試價值最高。
- 第三方遊戲 provider / wallet / bet-settle 最能講 state、timeout、compensation 與 provider transaction。
- Batch / projection / Mongo backup 能補上 retry、重跑、資料一致性、記憶體壓力與資料保留策略。

這三條完成後，再補：

4. `settled-bets-kafka` 或其他 Kafka / MQ flow
5. `observability-pipeline` / rollout / K3s 類 case

每條 case 必須能講：

- 入口在哪。
- DB / Redis / MQ / provider 怎麼走。
- 失敗會發生在哪個 window。
- 怎麼避免重複扣款、重複派彩、重複寫報表或資料漏投。
- 哪些是 Nick 真實開發過，哪些是 code-backed 分析。
- 如果重做，會怎麼改。

## 近期學習優先級

1. 交易一致性 / 冪等 / 補償 / 對帳：金流、錢包、provider、下注結算都吃這條主軸。
2. Kafka / RabbitMQ 實戰語言：retry、DLQ、重送、順序性、重複消費、觀測與補償。
3. SQL / MySQL transaction / lock / index：要能講 transaction boundary、慢查與索引判斷。
4. K8s / Docker / observability 基礎：不用裝成 SRE，但要能讀 rollout、pod log、probe、resource 與 log pipeline。
5. AI-assisted workflow：作加分項，講 code reading、diff review、文件同步、測試檢查、commit 收斂；不要講「AI 幫我寫」。

完整 8 週軟硬實力養成計劃維護在 `16-career-skill-matrix.md`。原則是每週只補一個 production 主題，每週都要產出 3 分鐘 case、5 個追問與 claim boundary，不做純讀書焦慮。

## 2026 趨勢觀察

2026 的後端職缺仍看重 Java / Backend / cloud / Kubernetes / CI/CD / system stability，但更偏向「可驗證經驗」與「能把 AI 工具放進工程流程」。目前應關注：

- AI-assisted development workflow。
- Java / Spring Boot 現代化。
- Kafka / MQ reliability。
- K8s / cloud / observability。
- 金流 / 帳務 / 對帳一致性。
- Legacy modernization。

趨勢來源需要定期重查；目前參考 2026-05-20 查到的 104 Senior Backend 職缺、Robert Walters Taiwan 2026 tech hiring trend、iThome 2026 CIO / CISO AI 軟體工程觀察。

## 求職節奏

若公司狀態沒有立刻危險，建議邊待邊投，不建議裸辭。理由：

- flows 還沒練到自然講。
- 目前信心不穩，裸辭會削弱談薪。
- 需要面試回饋來校正履歷與 case。
- 有現職時，談薪與選擇權比較好。

建議節奏：

```text
2 週：整理 3 條主力 flow 面試講稿
4 週：開始投 104 / LinkedIn，目標 10-15 個職缺
6-8 週：用面試回饋修履歷、case 與薪資策略
```

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
