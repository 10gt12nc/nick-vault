# Nick 履歷與自傳 Master

> 這是唯一履歷 / 自傳 master。來源是待刪區舊履歷、專案 flow、career material 與 code-backed analysis 重新整理後的保守版。正式投遞前，所有「主導、獨立完成、改善百分比」都必須再由 Nick 本人確認或補 MR / commit / ticket / metric。

> 目前證據狀態：保守母稿，尚未完成所有 project / flow 的最終整合。之後正式更新本檔其他 project 前，仍必須先深掃 code 主分支、近期分支、path-specific history、重要 diff，以及 `projects/`、`archive/`、KB 內所有履歷自傳素材；每條 claim 需標註 `真實開發過` / `專案存在` / `分析素材` / `待確認`，不得腦補。

> 2026-05-19 payment consolidation，2026-05-20 重新覆核：已完成 `projects/iwin/payment/contribution-claim-consolidation.md`。Nick 本人確認加上 `10gt12nc` commits / branches / 重要 diff，可把 `iwin/payment` 升級為「部分真實開發過」：多個 provider request / callback / query / withdraw 對接或維護、provider sign / response parsing bugfix、payment / withdraw order consistency 修正；2026-05-20 另補入 GoldenPay skeleton / protocol / spec handling / k3s enable direct commits。仍不得寫成主導完整金流、全部 provider owner、完整 wallet / reconciliation owner 或量化改善。

> 2026-05-19 game_api consolidation，2026-05-20 重新覆核：已完成 `projects/iwin/game_api/contribution-claim-consolidation.md`。Nick / `10gt12nc` 在 `game_api` coupon Controller / Service / DAO / mapper / entity 與 `iwin_gameserver` bet target handler 有 path-specific commits，可保守寫「參與玩家優惠券兌換上分 / 打碼要求 flow 開發」。`partner-deposit-withdraw-bill` 與 `agent-bonus-receive-transfer` 目前只作 code-backed 面試素材；邀請好友轉盤活動有 direct commits，但尚未做完整 flow package，只作補充 evidence。仍不得寫成主導完整 coupon / reward system、Partner API、代理佣金、修復 production 雙領事故、設計 Redis lock 或完整玩家端 API owner。

> 2026-05-19 game_job consolidation，2026-05-20 重新覆核：已完成 `projects/iwin/game_job/contribution-claim-consolidation.md`。Nick / `10gt12nc` 在 `game_job` daily summary job / service / mapper / config path 有 #247 主體 commits，也有 PG / Antplay 時區修正、新增玩家 / 留存與備份 / 清理相關 commits，可保守寫「參與每日遊戲資料彙總 batch / BI projection 開發與維護」。仍不得寫成主導完整 BI pipeline、完整 game_job owner、負責上游 gameserver 到 app_bi 全鏈路或改善 X%。

> 2026-05-19 game_job consolidation 補充：Nick / `10gt12nc` 在 `game_job` GSC Mongo backup job path 有 `d11b1f4`、`bf92773` direct commits，可保守寫「參與 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與 batch size 調整」。仍不得寫成主導完整第三方遊戲紀錄備份、完整 Antplay / GSC / Oneapi retention policy owner、已驗證 production enable 或改善 X%。

> 2026-05-20 app_bi consolidation：已重新確認 `projects/iwin/app_bi/contribution-claim-consolidation.md`。結論是 rolling / scoped negative consolidation：`app_bi` 只作後台入口 / BI / control plane 的 code-backed 面試分析素材，不新增正式履歷主成果；不得寫成 Nick 主導完整後台、BI owner、payment repair owner 或 runtime config owner。

> 2026-05-20 bi_share consolidation：已完成 `projects/iwin/bi_share/contribution-claim-consolidation.md`。結論是 rolling / scoped negative consolidation：目前未掃到 Nick / `10gt12nc` direct production commits，`bi_share` 只作 legacy Laravel / 分享 / 佣金 / BI 報表 / GM repair 的 code-backed 面試分析素材，不新增正式履歷主成果；不得寫成 Nick 主導分享系統、佣金系統、BI 報表、Laravel 升級或 k3s 遷移。

> 2026-05-20 third_games_api consolidation：已完成 `projects/iwin/third_games_api/contribution-claim-consolidation.md`。結論是 rolling / scoped consolidation：`third_games_api` 本 repo 只掃到局部測試 / merge 線索，不新增 standalone 正式履歷主成果；GSC transfer / OneAPI / Antplay provider adapter 保留為 code-backed 面試素材。較強的 Antplay / GSC / PG direct evidence 位於下游 `iwin_gameserver`，應由 `iwin_gameserver` consolidation 正確歸位，不反向包裝成 `third_games_api` owner。

> 2026-05-20 iwin_gameserver consolidation：已完成 `projects/iwin/iwin_gameserver/contribution-claim-consolidation.md`。Nick / `10gt12nc` 在 Antplay / GSC / PG gameserver 第三方 provider 投派整合、money job、`GamePlayer` log dispatch 與 log reel path 有 direct commits；可保守寫「參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接」。`center-http-deposit-withdraw` 仍只作 code-backed 面試素材；不得寫成主導完整 gameserver、完整 wallet owner、完整 provider integration owner、完整上分 / 下分 owner 或已建立完整 idempotency / reconciliation。

> 2026-05-20 iwin-workspace consolidation：已完成 `projects/iwin/iwin-workspace/contribution-claim-consolidation.md`。Nick / `10gt12nc` 有大量 workspace KB / docs / environment index / tool commits，可支撐「跨 repo code reading、schema / Redis / MQ / 運維文件與 git history 梳理」這類工作方法；不新增 standalone 正式履歷主成果，不反向包裝成 payment / gameserver / DevOps 業務開發或 owner。

> 2026-05-20 ugsoft-admin-api consolidation：已完成 `projects/ugsoft/ugsoft-admin-api/contribution-claim-consolidation.md`。Nick / `10gt12nc` 在 `ugsoft-admin-api` 有大量 direct commits，可保守寫「參與 UGSoft 後台 API / control plane 開發與維護，處理 login / JWT / RBAC、商戶 / provider 白名單、超級代理、報表查詢、風控監控、RabbitMQ request log / bet record 與 Quartz / report job」。仍不得寫成主導完整 UGSoft 平台、完整 provider gateway、完整 wallet / money flow 或完整 RabbitMQ architecture owner。

> 2026-05-20 ugsoft-connector-api consolidation：已完成 `projects/ugsoft/ugsoft-connector-api/contribution-claim-consolidation.md`。Nick / `10gt12nc` 在 `ugsoft-connector-api` 有大量 direct commits，可保守寫「參與 UGSoft provider connector / gateway 開發維護，串接 AntPlay / DerPlay 等第三方遊戲 provider 的 login、balance、transfer in / out、bet-settle、callback、request / bet record MQ、transfer wallet compensation、分表與 provider fail-fast」。仍不得寫成主導完整 UGSoft connector architecture、全部 provider owner、完整 wallet / ledger / reconciliation owner 或 exactly-once / outbox owner。

## 一、工作經驗

### 後端工程師｜瀚鼎股份有限公司（前星元資訊，同團隊轉移）

`2023/10 - 至今｜台北｜博弈 / 遊戲平台後端、平台整合、營運後台、事件流與維運`

- 參與中大型博弈 / 遊戲平台後端系統維護與功能開發，工作範圍涵蓋 Java / Spring Boot API、後台營運功能、第三方 provider 串接、金流 / 錢包相關流程、排程報表、Redis / MQ / DB 資料流與部署環境理解。
- 接手文件不足、服務邊界複雜的既有系統，透過 code reading、log 追蹤、資料流梳理與文件化，重建核心 flow 的理解，降低後續維護與交接成本。
- 參與平台服務版本升級與相容性調整相關工作，包含 Java / Spring Boot 版本、相依套件、部署驗證與既有功能風險確認；若正式履歷要寫成完整主導升級，需再補 commit / MR / release evidence。
- 參與前台 REST API、服務間 gRPC / ProtoBuf 或類似契約式通訊維護，處理欄位定義、資料結構、版本相容與多模組整合情境。
- 參與第三方金流 provider 對接與維護，包含 Owenpay、HamBit、Wwwpago、BFPAY、Pay4z、NaNapay、GoldenPay 等 provider request / callback / query / withdraw flow，處理簽章驗證、金額單位、merchant order id、callback ack、查單與 provider response 異常；不寫主導完整金流或全部 provider owner。
- 修正或維護 payment / withdraw order 建單一致性與 provider callback / sign 類問題，例如 BeanUtil copied id 造成 order insert collision 的風險、Pay4z / NaNapay / SitoBank / OnePay 類 provider 驗簽與欄位格式問題；不寫完整 reconciliation 或量化事故改善。
- 參與玩家優惠券兌換上分 / 打碼要求 flow 開發，串接 `game_api` API、coupon setting / record、GM command 與 `iwin_gameserver` 下游上分 / 打碼處理；可面試延伸 transaction boundary、idempotency、partial success 與 reconciliation，但不寫主導完整活動獎勵系統或修復 production 雙領事故。
- 參與每日遊戲資料彙總 batch / BI projection 開發與維護，處理 `log_reel` 投注明細到 `log_game_daily_record` 的彙總、資料日 / 時區窗口、delete + insert 重跑、新增玩家 / 留存與歷史資料備份 / 清理；並參與 GSC 第三方遊戲紀錄 Mongo 備份 job 的分批查詢與 batch size 調整；不寫主導完整 BI pipeline、完整資料平台 owner 或完整第三方紀錄備份 owner。
- 參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接，處理 Antplay / GSC / PG 類 bet / settle / refund / transfer-in-out flow、center_http command、玩家餘額異動 hook、log reel / bet log projection 與 bet_result / refund 邊界；聚焦 provider transaction、玩家餘額與 round log 一致性，不寫主導完整 gameserver 或完整遊戲錢包 owner。
- 參與 UGSoft 後台 API / control plane 開發與維護，處理 login / JWT / RBAC、商戶 / provider 白名單、超級代理、報表查詢、風控監控、RabbitMQ request log / bet record 非同步資料處理與 Quartz / report job 維護；不寫主導完整 UGSoft 平台或完整 provider gateway。
- 參與 UGSoft provider connector / gateway 開發維護，串接 AntPlay / DerPlay 等第三方遊戲 provider 的 login、balance、transfer in / out、bet-settle、callback 與 request / bet record MQ，並處理 transfer wallet、分表與 provider fail-fast 相關維護；不寫主導完整 connector architecture、全部 provider 或完整 wallet / reconciliation。
- 維護或分析 Kafka / RabbitMQ / scheduled job 等非同步流程，理解 retry、DLT、補償、request log、資料重跑與營運查詢在 production 中的重要性。
- 參與後台控制面與營運工具維護，包含 RBAC / 權限、報表查詢、玩家 / 商戶 / provider 設定、白名單、quota、Quartz job 與操作稽核等場景。
- 參與或分析 K3s / Docker / CI / observability 相關部署與維運資料，理解 service rollout、probe、resource、log pipeline、release provenance 與事故排查需求。
- 參與或接觸 Game Server 新遊戲 / 活動、slot 類遊戲邏輯、RTP 模擬與 reel strip 驗證相關資料與流程，理解遊戲數學模組與 runtime loading 對遊戲平台的影響；若要寫成主要職責，需再確認實際參與程度。
- 曾處理大量資料批次與查詢效能問題，例如 MongoDB 分批查詢 / 批次搬移、cursor / stream、JVM / GC 觀察、Redis 熱點資料與 SQL 查詢調整等方向；正式履歷若要寫成量化改善，需再補前後指標。

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
- 已補較強 code evidence：iwin payment provider request / callback / query / withdraw 對接與維護可用「參與」口徑，包含 Owenpay、HamBit、Wwwpago、BFPAY、Pay4z、NaNapay、GoldenPay 等 path-specific commits；payment / withdraw order insert consistency bugfix 也可保守使用。game_api 已完成 project-level consolidation，coupon redeem / grant flow 可用「參與 / 開發」口徑，包含 `game_api` coupon API / service / DAO / mapper / entity 與 `iwin_gameserver` bet target handler；partner / agent bonus 只作面試素材，邀請好友轉盤只作補充 evidence。game_job 已完成 project-level consolidation；daily summary 可用「參與 / 開發 / 維護」口徑，包含 daily summary job / service / mapper / config、時區窗口、新增玩家 / 留存與備份 / 清理；third-party Mongo backup 可用「局部參與 / 維護」口徑，限 GSC backup 分批查詢與 batch size 調整。third_games_api 已完成 rolling consolidation，但不新增 standalone 正式履歷主成果；相關 provider adapter 僅作面試素材。iwin_gameserver 已完成 rolling consolidation，第三方 provider 投派整合與 gameserver 錢包 / 投注流水串接可用「參與 / 開發 / 維護」口徑，限 Antplay / GSC / PG 類 bet / settle / refund / transfer-in-out、center_http command、money job、log projection。iwin-workspace 已完成 rolling consolidation，只作 cross-repo KB / system reconstruction supporting evidence，不作 standalone 主成果。ugsoft-admin-api 已完成 rolling consolidation，可用「參與 UGSoft 後台 API / control plane 與 RabbitMQ / Quartz 非同步資料處理」口徑，限 login / JWT / RBAC、白名單、報表、風控監控、request log / bet record MQ 與 report job。ugsoft-connector-api 已完成 rolling consolidation，可用「參與 UGSoft provider connector / gateway、AntPlay / DerPlay adapter、transfer wallet、callback、request / bet record MQ、分表與 provider fail-fast」口徑。仍不可寫成主導完整金流、完整 reward system、完整 Partner API、完整代理佣金、完整 BI pipeline、完整第三方紀錄備份、完整 third_games_api provider adapter、完整 gameserver、完整遊戲錢包、完整 UGSoft 平台、完整 provider gateway / connector architecture、完整 RabbitMQ architecture、完整 wallet / ledger / reconciliation、exactly-once / outbox、全部 provider owner、完整 DevOps / workspace owner 或已建立完整 reconciliation。
