# 投遞用自傳版本

這份是正式投遞履歷時使用的自傳版本，不取代 `05-resume-master-zh.md`。
`05-resume-master-zh.md` 是母稿，這份是從母稿濃縮後的投遞版。

> 目前證據狀態：保守投遞稿，尚未完成所有 project / flow 的最終整合。之後正式更新本檔其他 project 前，仍必須先深掃 code 主分支、近期分支、path-specific history、重要 diff，以及 `projects/`、KB 內所有履歷自傳素材；不能把專案存在或 AI 分析素材寫成 Nick 真實開發成果。`archive/` 已清空，不再列為必要來源。

> 2026-05-19 payment consolidation，2026-05-20 重新覆核：已完成 `projects/iwin/payment/contribution-claim-consolidation.md`。投遞自傳可保守寫 Nick 參與 iwin payment 多個第三方金流 provider request / callback / query / withdraw 對接與維護，以及 payment / withdraw order consistency bugfix；2026-05-20 補入 GoldenPay direct commits 作為多 provider integration evidence。仍不寫主導完整金流、全部 provider owner、完整 wallet / reconciliation owner 或量化改善。

> 2026-05-19 game_api consolidation，2026-05-20 重新覆核：已完成 `projects/iwin/game_api/contribution-claim-consolidation.md`。投遞自傳可保守寫 Nick 參與玩家優惠券兌換上分 / 打碼要求 flow 開發；`partner-deposit-withdraw-bill` 與 `agent-bonus-receive-transfer` 只作 code-backed 面試素材，邀請好友轉盤只作補充 evidence。仍不寫主導完整活動獎勵系統、Partner API、代理佣金、修復 production 雙領事故、設計 Redis lock 或完整玩家端 API owner。

> 2026-05-19 game_job consolidation，2026-05-20 重新覆核：已完成 `projects/iwin/game_job/contribution-claim-consolidation.md`。投遞自傳可保守寫 Nick 參與每日遊戲資料彙總 batch / BI projection 開發與維護；仍不寫主導完整 BI pipeline、完整資料平台 owner、負責上游 gameserver 到 app_bi 全鏈路或量化改善。

> 2026-05-19 game_job consolidation 補充：投遞自傳可保守寫 Nick 參與 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與 batch size 調整；仍不寫主導完整第三方紀錄備份、完整 retention policy owner 或量化改善。

> 2026-05-20 app_bi consolidation：已重新確認 `projects/iwin/app_bi/contribution-claim-consolidation.md`。結論是 rolling / scoped negative consolidation：`app_bi` 只作後台入口 / BI / control plane 的 code-backed 面試分析素材，不新增投遞自傳主成果。

> 2026-05-20 bi_share consolidation：已完成 `projects/iwin/bi_share/contribution-claim-consolidation.md`。結論是 rolling / scoped negative consolidation：目前未掃到 Nick / `10gt12nc` direct production commits，`bi_share` 只作 legacy Laravel / 分享 / 佣金 / BI 報表 / GM repair 的 code-backed 面試分析素材，不新增投遞自傳主成果。

> 2026-05-20 third_games_api consolidation：已完成 `projects/iwin/third_games_api/contribution-claim-consolidation.md`。結論是 rolling / scoped consolidation：`third_games_api` 本 repo 只掃到局部測試 / merge 線索，不新增投遞自傳主成果；GSC transfer / OneAPI / Antplay provider adapter 只作 code-backed 面試素材。下游 `iwin_gameserver` 的 Antplay / GSC / PG direct evidence 已由 `iwin_gameserver` consolidation 收口，不反向包裝成 `third_games_api` owner。

> 2026-05-22 rolling resume package 補充：`third_games_api` 本批代表 flows 已全部完成 Step 5，但結論不變：GSC transfer、OneAPI / PG bet_result、Antplay 三段式 bet / settle / rollback、GSC split withdraw / deposit / rollback / cancel 都只作 code-backed 面試素材，不新增投遞自傳主成果。

> 2026-05-20 iwin_gameserver consolidation：已完成 `projects/iwin/iwin_gameserver/contribution-claim-consolidation.md`。投遞自傳可保守寫 Nick 參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接，範圍限 Antplay / GSC / PG 類 bet / settle / refund / transfer-in-out、money job 與 log projection；仍不寫主導完整 gameserver、完整遊戲錢包、完整 provider integration、完整上分 / 下分或完整 idempotency / reconciliation。

> 2026-05-20 iwin-workspace consolidation：已完成 `projects/iwin/iwin-workspace/contribution-claim-consolidation.md`。投遞自傳不新增 workspace 主成果；只可作 supporting context，說明 Nick 有跨 repo code reading、schema / Redis / MQ / 運維文件與 git history 梳理能力。不得寫成完整 DevOps owner、workspace owner 或子 repo 業務開發。

> 2026-05-22 k3s-deploy Step 5 補充：`k3s-deploy gameserver-phased-rollout` 已完成 Step 5，可作 Platform / Lead Backend 面試素材，說明 legacy gameserver 上 K3s、ZK registration、phase rollout、Recreate 與 ConfigMap / Secret rollback discipline；但沒有 Nick direct evidence，不新增投遞自傳主成果，不寫 K3s migration / SRE owner。

> 2026-05-20 ugsoft-admin-api consolidation：已完成 `projects/ugsoft/ugsoft-admin-api/contribution-claim-consolidation.md`。投遞自傳可保守寫 Nick 參與 UGSoft 後台 API / control plane 開發與維護，處理 login / JWT / RBAC、商戶 / provider 白名單、超級代理、報表查詢、風控監控、RabbitMQ request log / bet record 與 Quartz / report job；仍不寫主導完整 UGSoft 平台、完整 provider gateway、完整 wallet / money flow 或完整 RabbitMQ architecture owner。

> 2026-05-20 ugsoft-connector-api consolidation：已完成 `projects/ugsoft/ugsoft-connector-api/contribution-claim-consolidation.md`。投遞自傳可保守寫 Nick 參與 UGSoft provider connector / gateway 開發維護，串接 AntPlay / DerPlay 等第三方遊戲 provider 的 login、balance、transfer in / out、bet-settle、callback、request / bet record MQ、transfer wallet compensation、分表與 provider fail-fast；仍不寫主導完整 connector architecture、全部 provider owner、完整 wallet / ledger / reconciliation owner 或 exactly-once / outbox owner。

> 2026-05-20 ugsoft-workspace consolidation：已完成 `projects/ugsoft/ugsoft-workspace/contribution-claim-consolidation.md`。投遞自傳不新增 workspace 主成果；只可作 supporting context，說明有跨 repo system reconstruction、工程規範、local / deploy harness、runbook、provider migration 與 SerialSyncJob 風險整理能力。不得寫成完整 workspace / DevOps / release owner，也不得反向包裝成 service code 貢獻。

> 2026-05-20 antplay-slot-admin-api consolidation：已完成 `projects/antplay/antplay-slot-admin-api/contribution-claim-consolidation.md`。投遞自傳可保守寫 Nick 參與 AntPlay 後台 API / 商戶控制面開發維護，範圍包含 admin / merchant auth、商戶白名單、Game API 白名單同步、超級代理、玩家 / 投注 / request log / 報表查詢、RTP / 暗池 / 活動風控監控、RabbitMQ request log / 風控通知與 Quartz job；仍不寫主導完整 AntPlay slot platform、完整 game runtime、完整 wallet / reconciliation、完整風控平台或完整 RabbitMQ architecture owner。

> 2026-05-21 antplay-slot-game-api consolidation refresh：已完成 `projects/antplay/antplay-slot-game-api/contribution-claim-consolidation.md`。投遞自傳可保守寫 Nick 參與 AntPlay slot 遊戲 API / runtime 開發維護，範圍包含 game init、bet / settle / rollback、轉帳錢包、bet record / request log / transfer transaction 分表、RabbitMQ request log、Quartz 補通知、schema routing 與 RTP / dark pool / player control 關聯修正；可用作 high-traffic table governance、async audit、runtime decision / result acceptance 的面試素材。仍不寫主導完整 AntPlay slot platform、完整遊戲數學 / RTP 策略、完整 wallet / ledger / reconciliation、完整 sharding platform、完整 RabbitMQ / Kafka architecture owner 或 exactly-once / outbox owner。

> 2026-05-25 antplay-slot-game-job consolidation refresh：已完成 `projects/antplay/antplay-slot-game-job/contribution-claim-consolidation.md`，五條代表 flows 均已 Step 5。投遞自傳可保守寫 Nick 參與 AntPlay slot job / event processing 開發維護，範圍包含 Kafka consumer / Quartz job、代理玩家報表 projection / summary、big-win notification、活動累積投注 supporting flow、bet record / request log / report 分表與 job config；settle pool / darkpool 只作 analysis-first 面試素材。仍不寫主導完整 Kafka event platform、完整 reward platform、完整 settle pool / risk / jackpot owner、完整 DB sharding / schema routing owner、完整 BI / report platform、完整遊戲數學 / RTP 策略或完整 AntPlay slot platform。

> 2026-05-21 antplay math consolidation refresh：已完成 `projects/antplay/math-core/contribution-claim-consolidation.md` 與 `projects/antplay/star-math/contribution-claim-consolidation.md`。`*-math` 五條代表 flow 已全部 Step 5 並回填 project-level claim。投遞自傳可保守寫 Nick 參與 AntPlay slot math core / 多個 math module 維護與驗證，範圍包含 SlotMath contract、debug bet、fixedMultiBet、currency、RTP / reel strip、buy free / purchasable free spin、jackpot / symbol、特殊 feature result contract 與模擬驗證調整；仍不寫主導完整遊戲數學模型、全部 math module、完整 RTP 策略、完整 simulator / certification owner、完整 jackpot pool 或完整單一遊戲 feature owner。`math-workspace` 只作 supporting evidence，`platform-mock` 只作 failure injection supporting evidence，`buffer-id` 不放 Nick 實作成果。

> 2026-05-20 舊 104 PDF 履歷補讀：可補入「缺乏完整交接文件時，協助主管梳理兩套既有平台並恢復可維護 / 可交接狀態」的 legacy takeover / system reconstruction 口徑。舊 PDF 中對主管、流程、PM / QA 缺位、假敏捷等負面描述不得放入正式投遞稿；Kafka 架構建置、後台 API 架構、RTP 工具、效能改善等較強 claim 仍需依目前 project consolidation / commit evidence 保守降調。

> 2026-05-20 AI-assisted workspace 補充：`*-workspace` 經驗可作工程方法加分項，保守寫成 AI-assisted engineering workflow / knowledge-base driven development：用 Codex 類工具輔助 code reading、需求拆解、diff review、文件同步、測試檢查、commit 收斂與 KB 回填。正式投遞可放在專長 / 自我推薦，不建議作工作經驗主 bullet；不得寫成 AI 自動完成 production 開發或 Nick 主導完整 AI 平台。

## 使用原則

- 不寫人生流水帳。
- 不從求學、轉職、興趣開始。
- 不寫主管、公司制度、團隊問題的負面描述。
- 不把分析過的內容寫成已主導上線成果。
- 不追求文筆漂亮，重點是定位清楚、經驗可信、方向一致。
- 正式投遞建議使用「一般投遞版」。

## Rolling 合併投遞版（目前可用）

> 狀態：2026-05-25 rolling refresh + 104 投遞欄位檢查完成。已吸收 AntPlay game-api 五條代表 flow Step 5 與 project-level refresh、`third_games_api` 本批 Step 5 收斂、`k3s-deploy gameserver-phased-rollout Step 5` interview-only 邊界，以及 `antplay-slot-game-job` 五條代表 flow Step 5 / contribution consolidation refresh。這版可作通用 Senior Java Backend / Platform Backend 104 投遞稿；後續 flow 深掃、Step 5 或新 evidence 仍要回填修正。

> 通用目標職缺假設：Senior Java Backend / Platform Backend，職務內容偏第三方金流 / 遊戲 provider gateway、wallet / bet-settle、MQ / batch、交易一致性、既有系統接手與 production troubleshooting。Nick 暫時沒有特定 JD 時，使用本通用版投遞；只有實際 JD 偏純後台、純 DevOps、純遊戲數學、純管理職，或 Nick 明確要求客製時，才另行改版。

> 104 欄位檢查結果：工作經驗可貼目前 7 + 4 bullets；專長可貼目前 6 bullets；自傳建議使用「一般投遞版：700-1200 字」；自我推薦可貼本節 `自我推薦`。目前沒有加入主導、完整 owner、量化改善或未證實事故修復；若投純後台 / 純 DevOps / 純管理職，再另做 JD-specific 降調。

### 104 欄位版

#### 工作經驗

後端工程師｜瀚鼎股份有限公司（前星元資訊，同團隊轉移）
2023/10 - 至今

- 參與中大型博弈 / 遊戲平台後端開發與維護，工作範圍包含 Java / Spring Boot API、第三方金流 / 遊戲 provider 串接、金流 / 錢包流程、下注結算、事件流、排程報表、後台控制面與既有系統維護。
- 參與多個第三方金流 provider request / callback / query / withdraw 對接與維護，處理 provider sign、response parsing、callback 重送、timeout、merchant order id、金額單位、訂單狀態一致性與 withdraw order 建單一致性等問題。
- 參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接，範圍包含 Antplay / GSC / PG 類 bet / settle / refund / transfer-in-out、money job、玩家餘額異動 hook 與 log projection。
- 參與 AntPlay slot 遊戲 API / runtime 與 UGSoft provider connector / gateway 維護，處理 game init、bet / settle / rollback、transfer wallet、provider login / balance / transfer、request log / bet record MQ、分表、schema routing 與 provider fail-fast 相關流程。
- 參與 RabbitMQ / Kafka / Quartz 等非同步與排程流程維護，包含 request log / bet record 非同步資料處理、代理玩家報表 projection / summary、big-win notification、活動累積投注 supporting flow、bet record / request log / report 分表、每日遊戲資料彙總 batch / BI projection 與第三方遊戲紀錄 Mongo 備份分批處理。
- 參與 slot math core / 多個 math module 維護與驗證，處理 SlotMath contract、debug bet、fixedMultiBet、currency、RTP / reel strip、buy free / free spin、jackpot / symbol、特殊 feature result contract 與模擬驗證調整；此項作為遊戲平台領域補充，不包裝成完整遊戲數學 owner。
- 在缺乏完整交接文件的情況下，協助主管梳理兩套既有平台的服務、部署環境、資料流與維運脈絡；透過 code reading、log 追蹤、git history、資料表、Redis / MQ 流向與文件化，重建核心 production flow，協助平台逐步恢復到可維護、可交接的狀態。

後端工程師｜智湧科技（前原繪美術設計，同團隊整併）
2020/10 - 2023/04

- 負責博弈平台 API 與既有系統維護，使用 Java、SSM / Spring Boot、MySQL、Redis 等技術支援平台功能、營運需求與線上問題排查。
- 曾在維運組支援 A / B 博弈平台與其他專案的高頻線上問題、需求調整與 Bug 排查；舊履歷記錄約每月 30 件線上問題、2 張需求單與 3 張 Bug 單，正式投遞可視篇幅保守寫成「高頻線上問題支援」。
- 維護 JSP / SSM 舊系統，處理需求調整、測試環境排查、log 分析、資料狀態確認與局部重構。
- 曾於內部分享 ActiveMQ + Redis + Quartz 的非同步快取處理思路，用於高流量情境下降低 DB 壓力與改善回應穩定性。

#### 專長

- 後端語言與框架：Java、Spring Boot、Spring MVC、SSM、MyBatis、Spring Data JPA、REST API、gRPC / ProtoBuf。
- 高交易與平台 flow：第三方金流 provider、payment / withdraw order、callback / query / withdraw、provider gateway / connector、transfer wallet、bet / settle / rollback、request log / bet record。
- 資料庫與快取：MySQL、MongoDB、Redis、分表、索引與查詢效能、cursor / stream、批次資料處理、資料狀態排查。
- 非同步與排程：Kafka、RabbitMQ、ActiveMQ、Quartz、batch job、report projection、retry / compensation、idempotency、consistency、reconciliation。
- 遊戲與平台領域：博弈 / 遊戲平台、第三方遊戲 provider、slot game API / runtime、slot math module、RTP / reel strip、buy free / free spin、jackpot / symbol。
- 工程與維運理解：Docker、K3s、rollout / rollback analysis、OpenObserve、log tracing、git history / diff 追蹤、legacy system takeover、跨 repo system reconstruction、AI-assisted engineering workflow、knowledge-base driven development。

#### 自傳

我是一名以 Java 後端為主的工程師，具 4 年以上博弈 / 遊戲平台相關經驗，主要工作集中在平台 API、第三方金流 / 遊戲 provider 串接、金流 / 錢包流程、遊戲下注結算、MQ / Kafka / Quartz、排程報表、後台控制面與既有系統維護。相較於只看單一 API 是否完成，我更習慣從 production flow 的角度理解系統：入口在哪裡、資料如何流動、狀態如何轉換、哪裡是 source of truth、哪些只是 cache 或 report，以及失敗後如何補償、對帳與追蹤。

早期在智湧科技期間，我主要負責博弈平台 API 與舊系統維護，使用 Java、SSM / Spring Boot、MySQL、Redis 等技術支援平台功能與營運需求。那段經驗讓我長期接觸線上問題、需求調整、測試環境排查與跨部門溝通，也累積 JSP / SSM 舊系統維護、局部重構、log 分析與資料狀態排查能力。現職於瀚鼎後，我接觸到更複雜的遊戲平台與微服務環境，參與過第三方金流 provider request / callback / query / withdraw 對接維護、payment / withdraw order consistency 修正、第三方遊戲 provider 投派整合、gameserver 錢包 / 投注流水串接、AntPlay slot game API / job、代理玩家報表 projection / summary、big-win notification、活動累積投注 supporting flow、UGSoft connector / gateway、RabbitMQ / Kafka / Quartz 非同步處理、每日遊戲資料彙總 batch / BI projection、第三方遊戲紀錄 Mongo 備份，以及 slot math core / math module 維護與驗證。

這類系統的挑戰通常不在單一功能，而在跨服務、跨資料狀態與異常重試的邊界。例如金流 callback 重送、provider timeout、下注扣款與派彩 rollback、MQ 消費失敗、retry 重複副作用、報表 projection 與交易真相不一致、slot math core contract 變更影響多個 game module，都是我在整理與理解系統時會特別關注的風險。因此我會透過 code reading、log 追蹤、git history、資料表、Redis / MQ 流向與文件化，重建核心 flow 的理解，讓後續維護、交接與問題排查更有依據。

未來我希望往 Senior Java Backend / Platform Backend / System Owner 方向發展。我的目標不是追逐新技術名詞，而是持續累積能支撐 production 的能力：交易一致性、冪等、補償、對帳、Kafka / MQ、資料庫效能、系統設計、部署風險控制與跨團隊溝通。若有機會加入重視穩定性、可維護性與工程品質的團隊，我希望以務實、可落地的方式參與核心服務維護與系統演進。

#### 自我推薦

我適合 Senior Java Backend / Platform Backend 類型職缺，尤其是需要接手複雜既有系統、串接第三方金流 / 遊戲 provider、維護高交易 flow、處理 MQ / Kafka / Quartz 非同步事件與排程報表的團隊。我的強項不是只完成單點功能，而是能把入口、資料狀態、交易邊界、失敗重試、補償、對帳與觀測串起來，讓系統問題可以被定位、被追蹤、被交接。

過去經驗讓我熟悉博弈 / 遊戲平台常見的 production 風險，例如金流 callback 重送、provider timeout、下注結算 rollback、wallet / transaction 半完成狀態、MQ 消費失敗、報表 projection 與交易真相不一致、legacy code 文件不足與跨 repo service boundary 不清楚。我也曾在缺乏完整交接文件的情況下，協助主管梳理兩套既有平台並恢復可維護狀態。我會用保守、可驗證的方式閱讀 code、追 git history、比對 log / DB / Redis / MQ 流向，先建立可靠的系統理解，再進一步處理維護、修正與優化。

我不會把尚未證實的分析成果包裝成主導經驗；但對於實際參與過的 provider 串接、金流 / 錢包、遊戲 API、slot job、math module、報表 batch 與 legacy system takeover，我能在面試中用 code-backed flow 說清楚實作邊界、風險判斷與取捨。我也具備 AI-assisted 開發閉環經驗，能用 Codex 類工具輔助 code reading、需求拆解、diff review、文件同步、測試檢查、commit 收斂與 KB 回填，讓複雜系統分析可追蹤、可交接。我期待加入重視高交易正確性、可維護性與長期工程品質的後端團隊，持續往能承擔 production owner decision 的方向成長。

### 300-600 字版

我是一名以 Java 後端為主的工程師，具 4 年以上博弈 / 遊戲平台相關經驗，主要工作集中在平台 API、第三方 provider 串接、金流 / 錢包流程、遊戲下注結算、事件流、排程報表、後台控制面與既有系統維護。相較於只完成單一 API，我更習慣從 production flow 的角度理解系統，關注資料如何流動、狀態如何轉換、失敗後如何補償，以及營運人員是否能查詢與追蹤問題。

現職於瀚鼎後，我參與過多個第三方金流 provider request / callback / query / withdraw 對接與維護，也參與第三方遊戲 provider 投派整合、gameserver 錢包 / 投注流水串接、AntPlay slot 遊戲 API / runtime、UGSoft provider connector / gateway、AntPlay slot job / event processing、代理玩家報表 projection / summary、big-win notification、分表 / report path 維護、每日遊戲資料彙總 batch / BI projection、第三方遊戲紀錄 Mongo 備份，以及 slot math core / 多個 math module 維護與驗證。這些工作讓我熟悉 callback 重送、provider timeout、下注結算與 rollback、MQ 消費失敗、retry 重複副作用、分表 / schema routing、報表與交易真相不一致，以及 slot math contract 變更影響多個 game module 等高交易 / 高正確性場景常見風險。

我目前希望往 Senior Java Backend / Platform Backend 方向發展，持續強化交易一致性、冪等、補償、對帳、Kafka / MQ、資料庫效能與系統設計能力。我的優勢是能接手文件不足、服務邊界複雜的既有系統，透過 code reading、log 追蹤、git history 與資料流梳理，建立可維護、可追蹤、可交接的系統理解。

### 700-1200 字版

我是一名以 Java 後端為主的工程師，具 4 年以上博弈 / 遊戲平台相關經驗，主要工作集中在平台 API、營運後台、第三方 provider 串接、金流 / 錢包流程、遊戲下注結算、事件流、排程報表、slot math module 與既有系統維護。相較於只看單一 API 是否完成，我更習慣從 production flow 的角度理解系統：入口在哪裡、資料如何流動、狀態如何轉換、哪裡是 source of truth、哪些只是 cache 或 report，以及失敗後如何補償、對帳與追蹤。

早期在智湧科技期間，我主要負責博弈平台 API 與舊系統維護，使用 Java、SSM / Spring Boot、MySQL、Redis 等技術支援平台功能與營運需求。那段經驗讓我長期接觸線上問題、需求調整、測試環境排查與跨部門溝通，也累積 JSP / SSM 舊系統維護、局部重構、log 分析與資料狀態排查能力。我也曾於內部分享 ActiveMQ + Redis + Quartz 的非同步快取處理思路，用於高流量情境下降低 DB 壓力與改善回應穩定性。

現職於瀚鼎後，我接觸到更複雜的遊戲平台與微服務環境，工作範圍包含 Java / Spring Boot API、第三方金流 provider request / callback / query / withdraw 對接維護、payment / withdraw order consistency 修正、第三方遊戲 provider 投派整合、gameserver 錢包 / 投注流水串接、玩家優惠券兌換上分 / 打碼要求 flow、AntPlay slot game init / bet / settle / rollback、transfer wallet、bet record / request log / transfer transaction 分表與 schema routing、UGSoft provider connector / gateway、request / bet record MQ、AntPlay slot Kafka / Quartz job、代理玩家報表 projection、big-win notification、每日遊戲資料彙總 batch / BI projection、第三方遊戲紀錄 Mongo 備份分批處理，以及 AntPlay slot math core / 多個 math module 維護與驗證，包含 SlotMath contract、debug bet、fixedMultiBet、currency、RTP / reel strip、buy free / purchasable free spin、jackpot / symbol、特殊 feature result contract 與模擬驗證調整。

這類系統的挑戰通常不在單一功能，而在跨服務、跨資料狀態與異常重試的邊界。例如金流 callback 重送、provider timeout、下注扣款與派彩 rollback、MQ 消費失敗、retry 重複副作用、報表 projection 與交易真相不一致、slot math core contract 變更影響多個 game module，都是我在整理與理解系統時會特別關注的風險。因此我會透過 code reading、log 追蹤、git history、資料表、Redis / MQ 流向與文件化，重建核心 flow 的理解，讓後續維護、交接與問題排查更有依據。

未來我希望往 Senior Java Backend / Platform Backend / System Owner 方向發展。我的目標不是追逐新技術名詞，而是持續累積能支撐 production 的能力：交易一致性、冪等、補償、對帳、Kafka / MQ、資料庫效能、系統設計、部署風險控制與跨團隊溝通。若有機會加入重視穩定性、可維護性與工程品質的團隊，我希望以務實、可落地的方式參與核心服務維護與系統演進。

## 超精簡版：300-600 字

我是一名以 Java 後端為主的工程師，具 4 年以上博弈 / 遊戲平台相關經驗，主要工作集中在平台 API、營運後台、第三方 provider 串接、金流 / 錢包流程、事件流與既有系統維護。相較於單純 CRUD 開發，我更習慣從 production flow 的角度理解系統，關注資料如何流動、狀態如何轉換、失敗後如何補償，以及營運人員是否能查詢與追蹤問題。

過去在智湧科技期間，我負責博弈平台 API 與舊系統維護，累積線上問題排查、需求調整、跨部門協作與 JSP / SSM 舊系統維護經驗，也曾於內部分享 ActiveMQ + Redis + Quartz 的非同步快取處理思路。現職於瀚鼎後，我接觸更複雜的遊戲平台與微服務環境，實際參與多個第三方金流 provider request / callback / query / withdraw 對接與維護，也參與玩家優惠券兌換上分 / 打碼要求 flow、每日遊戲資料彙總 batch / BI projection 開發維護、GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與批次調整、第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接，以及 UGSoft 後台 API / control plane、AntPlay / DerPlay provider connector、transfer wallet、AntPlay slot game API / job / event processing、bet record / request log 分表與 schema routing、slot math core / math module 維護與驗證、代理玩家報表 projection、big-win notification、RabbitMQ request / bet record 非同步資料處理與 Quartz / report job 維護，並接觸 Kafka / MQ、排程報表、後台權限與 K3s / observability 相關資料。

我目前希望往 Senior Java Backend / Platform Backend 方向發展，持續強化交易一致性、冪等、補償、對帳、Kafka / MQ、資料庫效能與系統設計能力。我的優勢是能接手文件不足、服務邊界複雜的既有系統，透過 code reading、log 追蹤與資料流梳理，建立可維護、可追蹤、可交接的系統理解。

## 一般投遞版：700-1200 字

我是一名以 Java 後端為主的工程師，具 4 年以上博弈 / 遊戲平台相關經驗，主要工作集中在平台 API、營運後台、第三方 provider 串接、金流 / 錢包流程、事件流、排程報表與既有系統維護。相較於只看單一 API 是否完成，我更習慣從 production flow 的角度理解系統：入口在哪裡、資料如何流動、狀態如何轉換、哪裡是 source of truth、哪些只是 cache 或 report，以及失敗後如何補償、對帳與追蹤。

早期在智湧科技期間，我主要負責博弈平台 API 與舊系統維護，使用 Java、SSM / Spring Boot、MySQL、Redis 等技術支援平台功能與營運需求。那段經驗讓我長期接觸線上問題、需求調整、測試環境排查與跨部門溝通，也累積 JSP / SSM 舊系統維護、局部重構、log 分析與資料狀態排查能力。我也曾於內部分享 ActiveMQ + Redis + Quartz 的非同步快取處理思路，用於高流量情境下降低 DB 壓力與改善回應穩定性。

現職於瀚鼎後，我接觸到更複雜的遊戲平台與微服務環境，工作範圍包含 Java / Spring Boot API、後台營運功能、第三方金流 provider request / callback / query / withdraw 對接維護、玩家優惠券兌換上分 / 打碼要求 flow、每日遊戲資料彙總 batch / BI projection、GSC 第三方遊戲紀錄 Mongo 備份分批處理、第三方遊戲 provider 投派整合、gameserver 錢包 / 投注流水串接、下注 / 派彩 / refund、UGSoft 後台 API / control plane、AntPlay / DerPlay provider connector、transfer wallet、AntPlay slot game API / Kafka / Quartz job、slot math core / math module 維護與驗證、代理玩家報表 projection、big-win notification、bet record / request log / transfer transaction 分表與 schema routing、RabbitMQ request log / bet record 非同步資料處理、Quartz / report job、報表查詢、RBAC / 權限與部署維運相關資料。我也處理過 payment / withdraw order 建單一致性與 provider sign / response parsing 類問題。這類系統的挑戰通常不在單一功能，而在跨服務、跨資料狀態與異常重試的邊界。例如 callback 重送、provider timeout、下注扣款與 rollback、MQ 消費失敗、retry 重複副作用、分表查寫路由、報表與交易真相不一致、slot math contract 變更影響多個 game module，都是我在整理與理解系統時會特別關注的風險。

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
- `antplay-slot-game-api/slot-bet-settle-rollback` 或 `iwin_gameserver/third-party-transfer-in-out`
- `antplay-slot-game-job/proxy-user-data-report-projection`
- `request-log-rabbitmq-async` 或 `bet-record-sharding-schema-route`
- `k3s-deploy/gameserver-phased-rollout`（interview-only 加分）

面試時不要背自傳，要把自傳壓成 30 秒定位，然後用 3-5 個 production case 證明深度。

## 30 秒自我介紹

我是 Java 後端工程師，主要經驗在博弈 / 遊戲平台、第三方金流 / 遊戲 provider 串接、金流 / 錢包、下注結算、MQ / Kafka / Quartz 與既有系統維護。我的工作方式比較偏 production flow 角度，會關注資料狀態、冪等、retry、補償、對帳與線上問題追蹤。目前希望往 Senior Backend / Platform Backend 方向發展，持續強化高交易系統的一致性、可靠性與 owner decision 能力。
