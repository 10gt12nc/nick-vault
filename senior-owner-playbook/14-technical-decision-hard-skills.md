# 技術硬底子與決策比較

這份文件補足 flow 學習的第二層。

Flow 讓 Nick 看懂「資料怎麼跑、哪裡會壞」。但 Senior / Owner 還要能回答：

- 為什麼這裡用 DB transaction，不用 Redis lock？
- 為什麼這裡用 Kafka，不直接同步呼叫？
- 為什麼 callback 要 idempotency key？
- 為什麼 retry 不能無腦重送？
- 為什麼報表不能當 source of truth？
- 為什麼這個方案比較穩，但開發成本比較高？

所以每條重要 flow 讀完後，要視情況補一份「技術決策 / 硬底子學習」。

注意：這份不是新的主線，也不是技術大全。

使用順序固定是：

```text
先讀 production flow
-> flow 裡真的遇到某個技術問題
-> 才補對應硬底子
-> 寫成 materials/decision-notes.md
```

如果目前還沒有一條清楚的 flow，不要先讀這份當作學科大綱。

## 核心原則

- 先從真實 flow 出發，不抽象背八股。
- 只補跟目前 flow 相關的底層知識，不一次開太多坑。
- 技術比較要講代價，不只講優點。
- 外部通用知識可以補，但必須標成「通用模式 / 外部案例」，不能混成專案既有事實。
- 沒有 code evidence 的地方，要寫「專案未確認」。

## 什麼時候需要補硬底子

以下任一情況，就應該補：

- flow 涉及錢、注單、錢包、訂單狀態。
- flow 有 DB + Redis + MQ / callback / external API 的跨資源一致性。
- flow 有 retry、補償、對帳、人工修復。
- flow 有 high traffic、批量處理、排程、非同步 consumer。
- flow 牽涉技術選型：同步 vs 非同步、DB vs cache、lock vs idempotency、event vs request log。
- 面試時可能被追問「為什麼這樣設計」。

## 硬底子主題地圖

### 1. Transaction / Consistency

要會回答：

- DB transaction 能保什麼，不能保什麼。
- 跨 DB / Redis / MQ / HTTP 時，transaction boundary 在哪裡斷掉。
- eventual consistency 和 strong consistency 差在哪。
- source of truth 怎麼定義。
- reconciliation 是補救還是主流程的一部分。

常見比較：

- 單 DB transaction vs distributed transaction。
- outbox pattern vs 直接 publish MQ。
- local transaction + retry vs saga compensation。
- 狀態機 vs 多個 boolean / status flag。

### 2. Idempotency / Retry / Compensation

要會回答：

- 重複 callback 為什麼一定會發生。
- retry-safe 的條件是什麼。
- idempotency key 放哪裡、怎麼查、怎麼過期。
- retry、補償、人工修復、對帳的界線。
- 重送成功但下游已處理過怎麼辦。

常見比較：

- request idempotency key vs business unique key。
- sync retry vs async retry。
- fixed delay vs exponential backoff。
- retry queue vs dead letter queue。
- 自動補償 vs 人工審核修復。

### 3. DB / Index / Lock / Performance

要會回答：

- slow query 怎麼查。
- index 怎麼判斷是否有效。
- lock wait / deadlock 怎麼定位。
- 查詢型報表和交易型資料要怎麼切。
- 批量更新為什麼會拖垮 production。

常見比較：

- OLTP table vs report projection table。
- 即時計算 vs 預聚合。
- pessimistic lock vs optimistic lock。
- DB lock vs Redis lock。
- offset pagination vs cursor pagination。

### 4. Redis / Cache / Runtime Projection

要會回答：

- Redis 是 cache、projection，還是 runtime source。
- DB 改了但 Redis 沒同步怎麼辦。
- Redis 先寫、DB rollback 的風險。
- cache rebuild、stale data、TTL、版本號怎麼設計。
- runtime service 是讀 Redis 還是讀 DB。

常見比較：

- cache-aside vs write-through vs write-behind。
- Redis lock vs DB unique constraint。
- TTL cache vs explicit invalidation。
- config projection vs direct DB read。

### 5. Kafka / MQ / Async Architecture

要會回答：

- 什麼時候該用 MQ，什麼時候不該用。
- at-least-once 代表什麼風險。
- consumer 重複消費怎麼處理。
- ordering、partition key、offset commit 怎麼影響一致性。
- MQ 成功但 DB 失敗，或 DB 成功但 MQ 失敗怎麼辦。

常見比較：

- synchronous API vs async event。
- Kafka vs RabbitMQ / Redis stream。
- event log vs command queue。
- consumer idempotency vs producer exactly-once。
- outbox relay vs direct publish。

### 6. Observability / Troubleshooting

要會回答：

- production issue 怎麼從 log 追到 transaction。
- correlation id / request id / order id 怎麼串。
- metric、log、trace 各自解什麼問題。
- alert 是偵測症狀還是 root cause。
- 面對玩家申訴、金流卡單、報表不一致時怎麼排查。

常見比較：

- log search vs metric dashboard vs trace。
- technical alert vs business alert。
- request log vs audit log vs event log。
- symptom dashboard vs owner runbook。

### 7. Deployment / Reliability / Rollback

要會回答：

- deployment 會怎麼影響正在處理的交易。
- migration、feature flag、backward compatibility 怎麼控風險。
- rollout、rollback、canary 的差異。
- config / secret / resource / probe 怎麼檢查。

常見比較：

- big bang release vs phased rollout。
- feature flag vs branch deploy。
- rollback code vs forward fix。
- blue-green vs rolling update。

## 每條 flow 的硬底子輸出格式

建議放在：

```text
projects/{domain}/{project}/flows/{flow-name}/materials/decision-notes.md
```

內容格式：

```text
# Decision Notes - {flow-name}

## 本 flow 牽涉的硬底子

- 主題：
- 為什麼和本 flow 有關：
- 專案已確認 evidence：
- 專案未確認：
- 外部通用模式：

## 技術選型比較

### Option A
- 適合情境：
- 優點：
- 缺點：
- production 風險：
- rollback / recovery：

### Option B
- 適合情境：
- 優點：
- 缺點：
- production 風險：
- rollback / recovery：

## Senior / Owner 判斷

- 如果我是 owner，我會先確認什麼：
- 我會先修什麼：
- 我會暫時不修什麼：
- 需要哪些監控 / log / reconciliation：

## 面試講法

- 30 秒版：
- 3 分鐘版：
- 可能追問：
```

## 提示詞

```text
請針對這條 flow 補「技術硬底子 / 決策比較」。

請先重讀：
- flow.md
- career-interview.md
- materials/evidence.md
- materials/interview.md
- materials/claim-boundary.md
- 若是舊格式，讀同層舊檔並標註待遷移
- 相關 code path

請不要重寫 flow。
請不要更新履歷。

請只補與本 flow 直接相關的技術底層，不要變成教科書大全。

請輸出：
1. 本 flow 牽涉哪些硬底子
2. 每個技術點的核心概念
3. 專案 code 已確認 evidence
4. 專案未確認
5. 外部通用模式，需標明不是專案事實
6. 技術選型比較
7. Senior / Owner 決策
8. 面試追問與回答

請輸出到：
projects/{domain}/{project}/flows/{flow-name}/materials/decision-notes.md
```

## 學習順序

不要先啃全部硬底子。建議順序：

1. 先讀一條 flow。
2. 讀懂資料流與 failure window。
3. 只補這條 flow 需要的硬底子。
4. 寫 `materials/decision-notes.md`。
5. 用面試追問測試自己是否真的懂。

這樣才會從「知道資料流」升級成「能做技術判斷」。
