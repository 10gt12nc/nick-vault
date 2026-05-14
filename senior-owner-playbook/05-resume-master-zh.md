# Nick 履歷與自傳 Master

> 這是唯一履歷 / 自傳 master。來源是待刪區舊履歷、專案 flow、career material 與 code-backed analysis 重新整理後的保守版。正式投遞前，所有「主導、獨立完成、改善百分比」都必須再由 Nick 本人確認或補 MR / commit / ticket / metric。

> 目前證據狀態：保守母稿，尚未完成所有 project / flow 的最終整合。之後正式更新本檔前，必須先深掃 code 主分支、近期分支、path-specific history、重要 diff，以及 `projects/`、`archive/`、KB 內所有履歷自傳素材；每條 claim 需標註 `真實開發過` / `專案存在` / `分析素材` / `待確認`，不得腦補。

## 一、工作經驗

### 後端工程師｜瀚鼎股份有限公司（前星元資訊，同團隊轉移）

`2023/10 - 至今｜台北｜博弈 / 遊戲平台後端、平台整合、營運後台、事件流與維運`

- 參與中大型博弈 / 遊戲平台後端系統維護與功能開發，工作範圍涵蓋 Java / Spring Boot API、後台營運功能、第三方 provider 串接、金流 / 錢包相關流程、排程報表、Redis / MQ / DB 資料流與部署環境理解。
- 接手文件不足、服務邊界複雜的既有系統，透過 code reading、log 追蹤、資料流梳理與文件化，重建核心 flow 的理解，降低後續維護與交接成本。
- 參與平台服務版本升級與相容性調整相關工作，包含 Java / Spring Boot 版本、相依套件、部署驗證與既有功能風險確認；若正式履歷要寫成完整主導升級，需再補 commit / MR / release evidence。
- 參與前台 REST API、服務間 gRPC / ProtoBuf 或類似契約式通訊維護，處理欄位定義、資料結構、版本相容與多模組整合情境。
- 參與第三方金流與錢包相關流程，包含 provider request、callback、簽章驗證、訂單狀態、入出金副作用、補償與對帳風險分析；正式履歷若要寫成主導或量化改善，需再補實作證據。
- 參與第三方遊戲 provider integration 與遊戲結算相關流程，涵蓋登入、轉入轉出、下注 / 派彩、rollback、交易同步、紀錄保存與報表鏈路，聚焦玩家餘額、provider transaction 與 round log 的一致性。
- 維護或分析 Kafka / RabbitMQ / scheduled job 等非同步流程，理解 retry、DLT、補償、request log、資料重跑與營運查詢在 production 中的重要性。
- 參與後台控制面與營運工具維護，包含 RBAC / 權限、報表查詢、玩家 / 商戶 / provider 設定、白名單、quota、Quartz job 與操作稽核等場景。
- 參與或分析 K3s / Docker / CI / observability 相關部署與維運資料，理解 service rollout、probe、resource、log pipeline、release provenance 與事故排查需求。
- 參與或接觸 Game Server 新遊戲 / 活動、slot 類遊戲邏輯、RTP 模擬與 reel strip 驗證相關資料與流程，理解遊戲數學模組與 runtime loading 對遊戲平台的影響；若要寫成主要職責，需再確認實際參與程度。
- 曾處理大量資料批次與查詢效能問題，例如 MongoDB cursor / stream、批次搬移、JVM / GC 觀察、Redis 熱點資料與 SQL 查詢調整等方向；正式履歷若要寫成量化改善，需再補前後指標。

### 後端工程師｜智湧科技（前原繪美術設計，同團隊整併）

`2020/10 - 2023/04｜台北｜博弈平台 API、舊系統維護、線上問題排查`

- 負責博弈平台後端 API 開發與既有系統維護，使用 Java、SSM / Spring Boot、MySQL、Redis 等技術支援平台功能與營運需求。
- 長期處理線上問題、需求調整與測試環境支援，累積 log 分析、資料狀態排查、跨部門溝通與舊系統維護經驗。
- 維護 JSP / SSM 等舊系統，進行局部重構、查詢調整與功能修補，降低既有功能變更風險。
- 曾於內部分享 ActiveMQ + Redis + Quartz 的非同步快取處理思路，用於高流量情境下降低 DB 壓力與改善回應穩定性。

## 二、專長

### 後端與平台

- Java、Spring Boot、Spring MVC、MyBatis、Spring Data JPA
- REST API、gRPC / ProtoBuf 或契約式通訊、服務間整合、provider gateway、後台營運系統
- Spring Security / JWT / RBAC / role-based control plane
- 既有系統逆向、code reading、log analysis、production flow reconstruction

### 資料與一致性

- MySQL：索引、分表 / partition、交易狀態、查詢優化、報表資料
- MongoDB：大量資料查詢、cursor / stream、批次搬移、記憶體壓力排查
- Redis：cache、runtime projection、白名單 / quota / session 類資料
- Kafka / RabbitMQ / MQ：event flow、consumer、retry、DLT、request log、非同步邊界
- Quartz / scheduled job：資料彙總、重跑、補償、營運工具

### 金流 / 錢包 / 遊戲平台

- 第三方金流 provider request / callback / sign verification
- Transfer wallet、provider transfer in / out、ledger、compensation retry
- 遊戲登入、下注 / 派彩 / rollback、provider transaction、round settlement
- Game Server 新遊戲 / 活動、slot 邏輯、RTP 模擬、reel strip 驗證
- BI / admin reporting、record retention、營運查詢與對帳

### 維運與工程品質

- Docker、K3s / Kubernetes 基本部署概念、service rollout、probe、resource
- OpenObserve / Fluent Bit / log pipeline / observability 基本理解
- 全域例外、錯誤碼、結構化 log、audit log
- JUnit / Mockito 基礎測試、code review、知識庫整理與交接文件

## 三、自傳

我是一名以 Java 後端為主的工程師，工作經驗主要集中在博弈 / 遊戲平台、後台營運系統、第三方 provider 串接、金流 / 錢包流程與既有系統維運。相較於只做單點 API，我更習慣從 production flow 的角度理解系統：入口在哪裡、資料如何流動、哪裡是 source of truth、哪些只是 cache 或 report、失敗時怎麼補償、營運人員如何查詢狀態。

早期在智湧時，我主要負責平台 API 與舊系統維護，長期支援線上問題、需求變更、測試環境排查與跨部門協作。那段經驗讓我理解到，後端工程不只是把功能寫完，更重要的是讓系統在真實營運情境下能查、能修、能維護。

現職於瀚鼎後，我接觸到更複雜的遊戲平台與微服務環境，包含第三方金流、第三方遊戲 provider、錢包轉點、下注結算、非同步事件、排程報表、後台控制面、服務間通訊、版本升級與 K3s / observability 相關資料。這些系統的共同特點是：只看單一 service 很容易誤判，真正的風險通常發生在跨服務、跨資料狀態與異常重試的邊界。

因此我逐漸把自己的定位從一般功能開發，調整為平台型 / 整合型後端工程師。我會把複雜系統拆成可理解的 flow，整理 code path、資料表、Redis、MQ、外部 API、失敗情境與 owner decision，讓自己和團隊能更清楚知道系統如何運作、風險在哪裡、下一步應該先補哪個缺口。

未來我希望往 Senior Backend Engineer、Platform Engineer、System Owner 方向發展。我的目標不是追逐新技術名詞，而是累積能支撐 production 的能力：交易一致性、冪等、補償、對帳、觀測性、部署風險控制、系統邊界設計與跨團隊溝通。長期希望成為能主導核心服務設計、可靠性改善與技術帶人的後端工程師。

## 四、自我推薦

我具備 4 年以上 Java 後端與博弈 / 遊戲平台相關經驗，熟悉 Spring Boot、MyBatis、Redis、MySQL、MQ / Kafka、Quartz、後台權限與第三方系統串接等場景。我的優勢在於能接手文件不足、服務邊界複雜的既有系統，透過 code reading、log 追蹤與資料流梳理，快速建立可維護的系統理解。

我特別重視 production flow 的正確性與可恢復性。面對金流 callback、錢包轉點、下注結算、報表彙總、大量資料批次處理、Redis cache rebuild、MQ retry 等流程時，我不只看 API 是否成功，也會關注冪等、狀態轉移、失敗窗口、補償、對帳、記憶體壓力與營運查詢。這讓我能用較成熟的方式處理平台型後端常見的真實問題。

我目前希望往月薪 10 萬以上的 Senior Java Backend / Platform Backend 方向前進，並持續補強系統設計、分散式一致性、Kafka / MQ、資料庫效能、JVM / GC、K8s / observability 與 owner decision 能力。若有機會加入重視穩定性、可維護性與工程品質的團隊，我能以務實、可落地的方式協助系統持續演進。

## Claim Boundary

- 可使用：參與、維護、分析、梳理、協助、優化、整理、提出改善方向。
- 謹慎使用：主導、負責整體架構、獨立完成、改善 X%、帶領團隊。
- 需要補證據：Java 版本升級全程、完整 RBAC 重構、Kafka outbox / exactly-once、gRPC 實作範圍、RTP / 遊戲數學主要職責、效能量化、事故改善數字、正式 Lead / Architect 職責。
