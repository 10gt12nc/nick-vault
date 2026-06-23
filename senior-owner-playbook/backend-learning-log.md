# Backend Learning Log

狀態：weekly checkpoint，不是每日 / 每週流水帳。

用途：記錄每週學習摘要、資料來源、面試題、Production 思考與是否需要回頭補 KB。只保留可支撐面試、production thinking 或 KB 維護判斷的摘要，不保存文章全文、不累積未完成債務。

## 使用規則

- 每週最多新增一個 checkpoint。
- 沒讀完不用補，不回填為學習債務。
- 若主題重複，下一次必須加深 incident、production、trade-off 或 interview depth，不重講基礎。
- 只標註：`已做過`、`參與過`、`分析過`、`可作為目標`、`待驗證`。
- 不改履歷、自傳、三個故事稿；若真的產生好素材，只列為 KB 建議。

## Week 01：Spring Transaction

狀態：已建立第一週內容。

### 本週主題

Spring Transaction：transaction boundary、rollback rule、AOP proxy / self-invocation、DB 成功但外部副作用失敗的風險。

### 為什麼這週學這個

Spring Transaction 是 Senior Java Backend 面試的基本盤，也直接連到 Nick 的主力 cases：

- Provider Integration：callback / query / timeout unknown 不能只靠一個 method transaction 解決。
- Wallet / Bet-Settle：狀態轉移與錢包 mutation 要有明確 transaction boundary。
- MQ / Projection：DB 成功但 MQ publish 失敗是典型 dual-write 風險。
- Legacy Takeover：看舊系統時，要先找 transaction 邊界與非交易副作用。

### 核心概念

- `@Transactional` 通常透過 Spring AOP proxy 套用；同一個物件內部 self-invocation 可能繞過 proxy，導致 transaction advice 沒有生效。
- 預設 rollback 心智：runtime exception / error 常見會 rollback；checked exception 需要明確設定 rollback rule，不能靠感覺。
- transaction boundary 不等於 business flow boundary。一條 payment / wallet flow 可能橫跨 DB、Redis、MQ、external provider，DB transaction 只保護單一資料庫資源。
- transaction 內不要放不可控外部呼叫或長時間操作，否則 lock time、timeout、重試副作用都會變難處理。
- DB commit 成功後，MQ / callback / external notification 失敗，不能靠同一個 local transaction 自動解決；需要 outbox、補償、重試或 reconciliation 思考。

### Production 情境

在 payment callback 中，常見流程是：

```text
callback received
-> verify signature / amount / order
-> check current order state
-> update order success
-> trigger wallet / MQ / projection / notify
```

transaction 只應該保護「狀態檢查 + 狀態更新」這段核心 DB mutation。外部通知或 MQ publish 若放在同一個思考框架裡，就會出現 DB 成功但外部副作用失敗的 failure window。

### 常見錯誤

- 以為 method 標 `@Transactional` 就一定有 transaction。
- 忽略 self-invocation。
- catch exception 後吞掉，導致應 rollback 卻 commit。
- 在 transaction 裡呼叫慢外部 API，拉長 lock time。
- 把 MQ publish 當成跟 DB update 同一個 atomic operation。
- 用 transaction 掩蓋 idempotency 設計不足。

### Incident / Troubleshooting

情境：玩家付款成功，但平台訂單仍是 pending。

排查順序：

1. 查 provider callback 是否進來：request log / signature / provider transaction id。
2. 查 order state transition：pending -> success 是否有 DB update。
3. 查 transaction rollback log：是否丟 exception、是否被 catch。
4. 查是否 self-invocation 或 private method 導致 transaction 沒生效。
5. 查 DB commit 成功後的下游副作用：wallet、MQ、projection 是否有缺。
6. 若 DB success / MQ failed，先補 projection 或 outbox / retry，不要直接重跑整個 callback 造成二次副作用。

### Senior 面試怎麼問

1. `@Transactional` 什麼情境會失效？
2. callback handler 裡 DB update 成功，但 MQ publish 失敗，你怎麼處理？
3. 為什麼 transaction boundary 不等於整條 business flow boundary？

### Senior 面試怎麼回答

1. `分析過`：我會先確認 transaction 是否真的經過 Spring proxy，例如 self-invocation、private method、非 Spring bean、異常被 catch 掉，都可能讓預期中的 rollback 沒發生。面試時我不會只說加 annotation，而會先確認 proxy、exception propagation 與 rollback rule。
2. `分析過 / 可作為目標`：DB 成功但 MQ 失敗屬於 dual-write 風險。短期要有補償或人工修復方式，長期可以考慮 outbox pattern，讓 DB update 與 event record 在同一個 transaction 裡提交，再由 relay 發送 MQ。
3. `分析過`：local DB transaction 只能保護 DB mutation，不能保證 external provider、Redis、MQ、callback notification 全部一起 atomic。Senior 要能把 DB transaction、idempotency、retry、compensation、reconciliation 分開講。

### System Design 延伸思考

Trade-off：

- `直接 transaction + publish MQ`：簡單，但 DB success / MQ failed 有風險。
- `transaction + outbox`：增加 table / relay / retry 複雜度，但 failure window 更可控。
- `distributed transaction`：理論上更強，但成本、複雜度與可用性風險通常很高，不是高交易系統的預設答案。
- `補償 / reconciliation`：適合 provider timeout、callback 重送、projection lag 等不可避免的不確定狀態。

### 與我的面試材料如何連結

- Provider Integration Story：補強 callback / timeout / query fallback 的 transaction boundary。
- Wallet / Bet-Settle Story：補強 wallet mutation、bet / settle / rollback 的 state transition。
- Legacy Takeover Story：補強從 code / log / git history 找 transaction 風險的能力。
- 對應 30 題核心：第 11、12、13、15、18 題。
- 可講進自我介紹：只能說「我會從 production flow 角度分析 transaction boundary 與失敗窗口」，不要說「我設計過完整交易平台 transaction architecture」。

### 本週必看

1. [Declarative Transaction Management](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative.html)
   - 來源：Spring Framework 官方文件。
   - 為什麼值得看：建立 declarative transaction 的正確心智，不只背 `@Transactional`。
   - 對應：payment callback、wallet / bet-settle transaction boundary。

2. [Rolling Back a Declarative Transaction](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/rolling-back.html)
   - 來源：Spring Framework 官方文件。
   - 為什麼值得看：釐清 rollback rule，避免面試時把 checked / unchecked exception 講錯。
   - 對應：callback exception、wallet mutation、batch partial failure。

3. [Proxying Mechanisms](https://docs.spring.io/spring-framework/reference/core/aop/proxying.html)
   - 來源：Spring Framework 官方文件。
   - 為什麼值得看：理解 self-invocation 為什麼會繞過 proxy。
   - 對應：Spring transaction 失效、legacy code review、AI code review。

### 本週可執行任務

30 分鐘內完成：

```text
用 90 秒回答：DB transaction 成功，但 MQ publish 失敗怎麼辦？
```

回答骨架：

1. 先說這是 dual-write 風險。
2. 短期如何查與補。
3. 長期如何用 outbox / retry / reconciliation 降低風險。
4. 保守連回自己的經驗：分析過 payment / wallet / MQ flow，不誇大成完整 outbox owner。

### 本週 KB 維護建議

建議新增：

- 暫無。Week 01 先記在本檔，不回填正式 casebook。

建議補強：

- 若之後 QA 發現 transaction 題回答不穩，再回填 `19-interview-coaching-question-bank.md` 的第 18 題回答。

建議暫不處理：

- 不改 `05 / 08 / 17`。
- 不新增 outbox 專文。
- 不重寫 payment / wallet flow。

### 本週不建議做什麼

- 不要延伸學完整 JTA / distributed transaction。
- 不要重構整個 KB。
- 不要把 outbox 寫成已做過。
- 不要追日文。
- 不要開新 side project 來練 transaction。
