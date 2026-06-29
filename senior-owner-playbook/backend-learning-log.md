# Backend Learning Log

狀態：weekly checkpoint，不是每日 / 每週流水帳。

用途：記錄每週學習摘要、資料來源、面試題、Production 思考與是否需要回頭補 KB。只保留可支撐面試、production thinking 或 KB 維護判斷的摘要，不保存文章全文、不累積未完成債務。

## 使用規則

- 每週最多新增一個 checkpoint。
- 沒讀完不用補，不回填為學習債務。
- 若主題重複，下一次必須加深 incident、production、trade-off 或 interview depth，不重講基礎。
- 只標註：`已做過`、`參與過`、`分析過`、`可作為目標`、`待驗證`。
- 不改履歷、自傳、三個故事稿；若真的產生好素材，只列為 KB 建議。

## 目前狀態

Week 01-04 試跑內容已清除。舊的 Week 01-04 automation run / commit / memory 都不作為已學過紀錄。

## Week 01 - Spring Transaction

日期：2026-06-29

Weekly Mode：Concept Mode

核心主題：Spring Transaction 不是「加上 `@Transactional` 就安全」，而是要能說清楚 transaction boundary、rollback 條件、外部副作用與觀測點。

本週重點：

1. Production transaction boundary 應該包住必須一起成功 / 一起失敗的本地 DB 狀態變更，不應把 provider API、MQ publish、長時間 IO 當成預設同一段 transaction。
2. `@Transactional` 的重點不是語法，而是 rollback rule、proxy 生效條件、exception 類型與 side effect 順序。
3. Senior 面試回答要能從 ACID 進到 failure window：DB commit 成功但外部副作用失敗、callback retry 導致重複入帳、查單 timeout unknown。

參考資料：

- Spring Framework Reference - Transaction Management：`https://docs.spring.io/spring-framework/reference/data-access/transaction.html`
- Transactional Outbox Pattern：`https://microservices.io/patterns/data/transactional-outbox.html`

Production / interview 連結：

- 對應 30 題核心：第 11 題 DB transaction 成功但 MQ publish 失敗、第 12 題 callback 重送、第 18 題 Spring transaction 失效。
- 自然連到 payment / wallet / bet-settle / MQ projection 類 production case，但本週沒有掃公司 repo，也不新增履歷 claim。

30 分鐘行動：挑一段熟悉的 service method，畫出 transaction 開始、DB write、外部 call / MQ / cache、commit、log / metric 的順序，標出一個 failure window。

KB 建議：本週只更新 weekly log / plan；不回填 `04 / 05 / 08 / 17`、三個故事稿或 flow claim。
