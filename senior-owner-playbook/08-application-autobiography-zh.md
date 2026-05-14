# 投遞用自傳版本

這份是正式投遞履歷時使用的自傳版本，不取代 `05-resume-master-zh.md`。  
`05-resume-master-zh.md` 是母稿，這份是從母稿濃縮後的投遞版。

> 目前證據狀態：保守投遞稿，尚未完成所有 project / flow 的最終整合。之後正式更新本檔前，必須先深掃 code 主分支、近期分支、path-specific history、重要 diff，以及 `projects/`、`archive/`、KB 內所有履歷自傳素材；不能把專案存在或 AI 分析素材寫成 Nick 真實開發成果。

## 使用原則

- 不寫人生流水帳。
- 不從求學、轉職、興趣開始。
- 不寫主管、公司制度、團隊問題的負面描述。
- 不把分析過的內容寫成已主導上線成果。
- 不追求文筆漂亮，重點是定位清楚、經驗可信、方向一致。
- 正式投遞建議使用「一般投遞版」。

## 超精簡版：300-600 字

我是一名以 Java 後端為主的工程師，具 4 年以上博弈 / 遊戲平台相關經驗，主要工作集中在平台 API、營運後台、第三方 provider 串接、金流 / 錢包流程、事件流與既有系統維護。相較於單純 CRUD 開發，我更習慣從 production flow 的角度理解系統，關注資料如何流動、狀態如何轉換、失敗後如何補償，以及營運人員是否能查詢與追蹤問題。

過去在智湧科技期間，我負責博弈平台 API 與舊系統維護，累積線上問題排查、需求調整、跨部門協作與 JSP / SSM 舊系統維護經驗，也曾於內部分享 ActiveMQ + Redis + Quartz 的非同步快取處理思路。現職於瀚鼎後，我接觸更複雜的遊戲平台與微服務環境，包含第三方金流、遊戲 provider、錢包轉點、下注結算、Kafka / MQ、排程報表、後台權限與 K3s / observability 相關資料。

我目前希望往 Senior Java Backend / Platform Backend 方向發展，持續強化交易一致性、冪等、補償、對帳、Kafka / MQ、資料庫效能與系統設計能力。我的優勢是能接手文件不足、服務邊界複雜的既有系統，透過 code reading、log 追蹤與資料流梳理，建立可維護、可追蹤、可交接的系統理解。

## 一般投遞版：700-1200 字

我是一名以 Java 後端為主的工程師，具 4 年以上博弈 / 遊戲平台相關經驗，主要工作集中在平台 API、營運後台、第三方 provider 串接、金流 / 錢包流程、事件流、排程報表與既有系統維護。相較於只看單一 API 是否完成，我更習慣從 production flow 的角度理解系統：入口在哪裡、資料如何流動、狀態如何轉換、哪裡是 source of truth、哪些只是 cache 或 report，以及失敗後如何補償、對帳與追蹤。

早期在智湧科技期間，我主要負責博弈平台 API 與舊系統維護，使用 Java、SSM / Spring Boot、MySQL、Redis 等技術支援平台功能與營運需求。那段經驗讓我長期接觸線上問題、需求調整、測試環境排查與跨部門溝通，也累積 JSP / SSM 舊系統維護、局部重構、log 分析與資料狀態排查能力。我也曾於內部分享 ActiveMQ + Redis + Quartz 的非同步快取處理思路，用於高流量情境下降低 DB 壓力與改善回應穩定性。

現職於瀚鼎後，我接觸到更複雜的遊戲平台與微服務環境，工作範圍包含 Java / Spring Boot API、後台營運功能、第三方遊戲 provider、金流 / 錢包、下注 / 派彩 / rollback、Kafka / MQ、scheduled job、報表查詢、RBAC / 權限與部署維運相關資料。這類系統的挑戰通常不在單一功能，而在跨服務、跨資料狀態與異常重試的邊界。例如 callback 重送、provider timeout、MQ 消費失敗、retry 重複副作用、報表與交易真相不一致，都是我在整理與理解系統時會特別關注的風險。

我也具備接手文件不足或服務邊界複雜系統的經驗，會透過 code reading、log 追蹤、資料表與 Redis / MQ 流向梳理，重建核心 flow 的理解，讓後續維護、交接與問題排查更有依據。在效能與穩定性方面，我接觸過大量資料批次處理、MongoDB cursor / stream、JVM / GC 觀察、Redis 熱點資料與 SQL 查詢調整等方向，也理解這些問題背後真正要處理的是可恢復性、可觀測性與資料一致性。

未來我希望往 Senior Java Backend / Platform Backend / System Owner 方向發展。我的目標不是追逐新技術名詞，而是持續累積能支撐 production 的能力：交易一致性、冪等、補償、對帳、Kafka / MQ、資料庫效能、系統設計、部署風險控制與跨團隊溝通。若有機會加入重視穩定性、可維護性與工程品質的團隊，我希望以務實、可落地的方式參與核心服務維護與系統演進。

## 不建議寫法

不要寫：

- 從小對電腦有興趣。
- 因為朋友推薦，所以開始學程式。
- 過去主管、PM、QA、流程不好。
- 我主導整體架構、獨立完成所有模組。
- 我改善效能 80% 以上，但沒有指標。
- 我已具備架構師能力。
- 我什麼技術都想學、都可以做。

可以改成：

- 我具備平台型後端與既有系統接手經驗。
- 我重視 production flow 的正確性、可追蹤性與可恢復性。
- 我熟悉高交易場景常見的 callback、retry、MQ、補償、對帳與資料狀態問題。
- 我希望往 Senior Backend / Platform Backend / System Owner 方向發展。

## 自傳與 Case Study 的分工

自傳只負責建立背景：

- 你是誰
- 做過哪類系統
- 技術主軸是什麼
- 為什麼往 Senior / Owner 走
- 你的工作風格是否穩定可信

真正要證明能力，要靠 case study：

- `payment-provider-callback`
- `transfer-wallet-transfer-in-out`
- `bet-settlement`
- `settled-bets-kafka`
- `observability-pipeline`

面試時不要背自傳，要把自傳壓成 30 秒定位，然後用 3-5 個 production case 證明深度。

## 30 秒自我介紹

我是 Java 後端工程師，主要經驗在博弈 / 遊戲平台、營運後台、第三方 provider 串接、金流 / 錢包、MQ / Kafka 與既有系統維護。我的工作方式比較偏 production flow 角度，會關注資料狀態、冪等、retry、補償、對帳與線上問題追蹤。目前希望往 Senior Backend / Platform Backend 方向發展，持續強化高交易系統的一致性、可靠性與 owner decision 能力。
