# 客製履歷 / 自傳

用途：糖蛙線上娛樂股份有限公司「資深JAVA後端工程師」JD-specific 版本。

原則：

- 以 `08-application-autobiography-zh.md` A 版為底，加入遊戲後端 / 維運監控 / 技術文件 / AI-assisted workflow。
- 對外定位為 `Senior Java Backend / Game Backend Engineer`。
- 不使用 Team Lead 強管理說法。
- 不寫完整架構師、完整 DevOps / SRE、完整遊戲平台 owner、完整 payment / wallet / MQ platform owner。
- 公司 / 產品 / provider 名稱正式投遞時建議替換成泛稱。

## 104 工作經驗

後端工程師｜瀚鼎股份有限公司（前星元資訊，同團隊轉移）
2023/10 - 至今

- 參與中大型遊戲 / 博弈平台後端系統開發與維護，使用 Java / Spring Boot、MyBatis、MySQL、Redis、MongoDB、Kafka / RabbitMQ / Quartz 等技術處理第三方 provider 串接、payment provider 對接、遊戲錢包 / 下注結算、非同步事件、排程報表、後台控制面與既有系統維護。
- 參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接，處理 bet / settle / refund / transfer-in-out、玩家餘額異動 hook、provider transaction、round log、request log 與 report projection 邊界，能從 state transition、idempotency、partial success 與 failure window 角度排查問題。
- 參與 Slot 遊戲 API / runtime、slot job / event processing 與 provider connector / gateway 維護，處理 game init、bet / settle / rollback、transfer wallet、provider login / balance / transfer、RabbitMQ request log / bet record、分表、schema routing、Quartz / report job 與 provider fail-fast 相關流程。
- 參與多個第三方金流 provider / 商戶 request / callback / query / withdraw 對接與維護，處理簽章驗證、金額單位、merchant order id、callback ack / 重送風險、查單、provider response parsing 與 payment / withdraw order consistency 類問題；此項定位為 provider integration，不包裝成完整金流 / wallet / reconciliation owner。
- 參與 RabbitMQ / Kafka / Quartz 等非同步與排程流程維護，包含 request log / bet record 非同步資料處理、代理玩家報表 projection / summary、big-win notification、每日遊戲資料彙總 batch、Mongo 分批備份與資料重跑邊界。
- 參與系統維運與問題排查，透過 log tracing、DB / Redis / MQ 狀態比對、git history、schema / index、batch window 與 source of truth 判斷定位 provider timeout、callback 重送、MQ 重複消費、report projection 與交易真相不一致等問題。
- 在缺乏完整交接文件的情況下，協助主管梳理兩套既有平台的服務、部署環境、資料流與維運脈絡；透過 cross-repo code reading、git history、DB / Redis / MQ / log flow 梳理與文件化，重建核心 production flow，讓系統逐步恢復到可維護、可追蹤、可交接的狀態。
- 建立 AI-assisted engineering workflow，用 Codex / Claude Code 類工具輔助 code reading、需求拆解、diff review、文件同步、測試檢查、commit 收斂與 KB 回填；AI 只作輔助，最後仍由自己判斷 production 風險、測試結果與交付品質。

後端工程師｜智湧科技（前原繪美術設計，同團隊整併）
2020/10 - 2023/04

- 負責博弈 / 平台型後端 API 與既有系統維護，使用 Java、SSM / Spring Boot、MySQL、Redis 支援平台功能、營運需求與線上問題排查。
- 長期支援線上問題、需求調整、測試環境排查與跨部門溝通，累積 log 分析、資料狀態確認、legacy system 維護與局部重構經驗。
- 曾於內部分享 ActiveMQ + Redis + Quartz 的非同步快取處理思路，用於高流量情境下降低 DB 壓力與改善回應穩定性。

## 104 專長

- 後端語言與框架：Java、Spring Boot、Spring MVC、SSM、MyBatis、Spring Data JPA、REST API、gRPC / ProtoBuf、Maven、Git。
- 資料庫與快取：MySQL、MongoDB、Redis、索引與查詢效能、分表 / partition、schema routing、cursor / stream、資料狀態排查。
- 非同步與排程：RabbitMQ、Kafka、ActiveMQ、Quartz、batch job、report projection、retry / compensation、idempotency、consistency、reconciliation risk analysis。
- 遊戲後端與平台 flow：第三方遊戲 provider gateway / connector、payment provider、transfer wallet、bet / settle / rollback、request log / bet record、provider transaction、source of truth 判斷。
- 系統維運與工程品質：Linux 基本操作、log tracing、structured log、audit log、Docker / K3s 基本概念、OpenObserve / log pipeline、Code Review、技術文件與 KB 維護。
- 工作方法：legacy system takeover、cross-repo code reading、git history / diff 追蹤、DB / Redis / MQ / log flow reconstruction、AI-assisted engineering workflow、knowledge-base driven development。

## 自傳 700-1200 字

我是一名以 Java 後端為主的工程師，具 5 年以上平台型後端與遊戲 / 博弈系統經驗，主要工作集中在 Java / Spring Boot API、第三方 provider 串接、payment provider 對接、遊戲錢包 / 下注結算、RabbitMQ / Kafka / Quartz、排程報表、後台控制面與既有系統維護。相較於只看單一 API 是否完成，我更習慣從 production flow 的角度理解系統：入口在哪裡、資料如何流動、狀態如何轉換、哪裡是 source of truth、哪些只是 cache / report / audit evidence，以及失敗後如何補償、追蹤與恢復。

早期在智湧科技期間，我主要負責平台 API 與舊系統維護，使用 Java、SSM / Spring Boot、MySQL、Redis 等技術支援平台功能與營運需求。那段經驗讓我長期接觸線上問題、需求調整、測試環境排查與跨部門溝通，也累積 JSP / SSM 舊系統維護、局部重構、log 分析與資料狀態排查能力。

現職於瀚鼎後，我接觸到更複雜的遊戲平台與高交易場景，參與過第三方遊戲 provider 投派整合、gameserver 錢包 / 投注流水串接、Slot 遊戲 API / runtime、provider connector / gateway、transfer wallet、request log / bet record MQ、RabbitMQ / Kafka / Quartz 非同步處理、代理玩家報表 projection / summary、big-win notification、每日遊戲資料彙總 batch、Mongo 分批備份，也參與第三方金流 provider request / callback / query / withdraw 對接維護與 payment / withdraw order consistency 修正。這些系統的共同特點是只看單一 service 很容易誤判，真正風險通常發生在跨服務、跨資料狀態與異常重試的邊界。

我特別重視線上服務穩定性與可維護性。面對 provider timeout、callback 重送、下注扣款與 rollback、MQ 消費失敗、retry 重複副作用、報表與交易真相不一致等問題，我會先拆清楚資料狀態、冪等 key、transaction boundary、retry / compensation、log / audit 與人工處理邊界，再決定要從 code、DB、Redis、MQ 或 log 哪一層排查。若要做效能或維運分析，我也會關注 SQL index、batch size、cursor / stream、Redis hot data、log / metrics 與 source of truth，而不是只看單一錯誤訊息。

我也有接手文件不足既有系統的經驗。曾在缺乏完整交接文件的情況下，協助主管梳理兩套既有平台的服務、部署環境、資料流與維運脈絡；透過 code reading、git history、DB / Redis / MQ / log flow 與文件化，重建核心 production flow，讓後續維護、問題排查與交接更有依據。近期我也建立 AI-assisted engineering workflow，用 Codex / Claude Code 類工具輔助 code reading、需求拆解、diff review、驗證檢查、文件同步與 KB 回填，但最後仍由自己判斷 production 風險與工程取捨。

我目前希望往 Senior Java Backend / Game Backend 方向發展。我的目標不是追逐新技術名詞，而是持續累積能支撐 production 的能力：狀態一致性風險判斷、冪等、補償、RabbitMQ / MQ、資料庫效能、系統設計、線上問題診斷、技術文件與工程流程改善。若有機會加入重視遊戲後端穩定性、可擴充性與工程品質的團隊，我希望以務實、可落地的方式參與核心後端服務維護與系統演進。

## 自我推薦

我適合 Senior Java Backend / Game Backend 類型職缺，尤其是需要維護遊戲後端穩定性、處理第三方金流 / 遊戲 provider 串接、遊戲錢包 / 下注結算、RabbitMQ 非同步流程、Redis / MySQL 資料狀態、既有系統接手與 production troubleshooting 的團隊。

我的強項不是只完成單點功能，而是能把入口、資料狀態、transaction boundary、失敗重試、補償、對帳風險與觀測串起來，讓系統問題可以被定位、被追蹤、被交接。對於實際參與過的 provider 串接、payment provider、遊戲 provider、wallet / bet-settle、MQ / batch、分表 / schema routing、legacy takeover 與 AI-assisted workflow，我能用 code-backed flow 說明做過什麼、分析過什麼，以及哪些不能誇大。

我不會把自己包裝成完整架構師或完整系統 owner；但我能參與架構討論、技術文件撰寫、維運問題排查與開發標準整理，並用保守、可驗證的方式推動系統可維護性與工程品質。
