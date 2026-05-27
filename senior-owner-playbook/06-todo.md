# Todo

## 使用規則

本檔是候選待辦與收斂清單，不是自動開工授權。

- Nick 明確下 `flow Step N`、`project contribution claim consolidation`、`rolling resume package` 時，AI 才開始對應深掃與改 project 文件。
- Nick 只要求「KB、待辦、缺口、優先順序、專案先不下一步」時，AI 只能更新本檔與相關 KB，一律不自動執行下面任何 flow。
- `必做收口` 是收斂排序，不代表每次回答都要推專案下一步；若當下是 KB-only 模式，回報 KB 維護結果即可。

## 已完成

- 已將外層舊整理資料歸入 `archive/` 參考區，之後可由 Nick 人工審查是否刪除。
- 2026-05-25：Nick 已確認 `archive/` 不需要保留，已清空內容，只保留 `.gitkeep` 佔位；後續 KB 不再把 archive 當必要來源。
- 從待刪區重新建立 `senior-owner-playbook/`。
- 建立新入口、維護規則、flow inventory、學習路線、flow 模板、面試案例與唯一履歷自傳。
- 已補 `08-application-autobiography-zh.md` 的 104 欄位版：工作經驗、專長、自傳、自我推薦；`05` 維持母稿 / 證據池定位，`08` 維持投遞可貼定位。
- 已完成 2026-05-25 `104 投遞欄位檢查`：`08-application-autobiography-zh.md` 的工作經驗、專長、自傳、自我推薦已標示為通用 Senior Java Backend / Platform Backend 可貼版；沒有加入未證實主導、完整 owner、量化改善或事故修復 claim。
- 已新增 `17-salary-negotiation.md`：維護 104 / 面試用薪資談判定位、期望待遇區間、底線、口頭說法與不可誇大邊界；正式談 offer 前仍需重查當下行情。
- 已依 2026-05-20 最新 104 職缺快照與舊履歷補強後定位，重新上修 `17-salary-negotiation.md`：104 建議填月薪 115,000-135,000 / 年薪 160-190 萬，面試主談年薪 160-180 萬，底線月薪 100,000。
- 已補讀 Nick 舊 104 PDF 履歷，吸收可用素材：兩套缺乏交接文件平台的 legacy takeover / system reconstruction、早期高頻線上問題支援、ActiveMQ + Redis + Quartz 內部分享；負面主管 / 流程描述與未驗證強 claim 不放正式投遞稿。
- 已補 `*-workspace` 的 AI-assisted engineering workflow 定位：可支撐 code reading、需求拆解、diff review、文件同步、測試檢查、commit 收斂與 KB 回填的開發閉環，放在工程方法 / 自我推薦 / 談薪支撐，不作 standalone production 主成果。
- 已補求職現實判斷：`進取` 是特定對口職缺的談判上限，不是保證；Nick 可以投 10 萬以上職缺，但要靠 3 條以上 production flow case 證明。近期優先把 payment callback、third-party provider / wallet、batch / projection flow 練熟，並採邊待邊投節奏。
- 已補目前 `80,000` 薪資判讀：這是目前公司 / 組織 / 職級 / 薪資帶定價，不等於外部市場最終定價；是否能脫離 8 萬錨點，要靠投遞與面試驗證，若拿到 `100,000-120,000` offer 即可證明主要是組織定價問題。
- 已補對標 Senior Java Backend / Platform Backend / System Owner 的 8 週軟硬實力養成計劃：每週以 production flow 為主題，同時補硬實力、軟實力、3 分鐘 case、追問與 claim boundary。
- 已建立全 vault 共用自動維護規則：每次 project / flow / Step 任務前自動重讀 KB、既有文件與相關 code 最新狀態，完成後自動判斷是否維護 README、Step 文件、evidence、claim boundary、todo 或共用 KB。
- 已補上舊文件自動排查規則：AI 必須自己檢查既有 Step / flow 是否過舊、缺 evidence 或不符合目前 KB；上一個 Step 不乾淨時，優先重整舊文件，不直接跳下一步。
- 已補上技術硬底子與決策比較模組：每條高價值 flow 可新增 `decision-notes.md`，整理技術選型、差異比較、trade-off、owner decision 與面試追問。
- 已補上大專案 / 子專案地圖規則：地圖只用來定位 repo 與 flow，不取代 production flow。
- 已補上初階到資深的軟硬實力矩陣：作為定期檢查表，不作為新的發散學習主線。
- 已重整 `app_bi` Step 1 / Step 2，並把已完成 flow 收斂成新版 `flow.md + career-interview.md + materials/` 結構。
- 已修正 Step 主線規則：AI 不可自行把「下游定位 / 補 evidence / 補 decision-notes / 架構圖」升級成新下一步；Step 3 乾淨且未牽涉履歷 / claim 風險時，預設進 Step 4。
- 已完成 `app_bi point-control-admin-operation Step 4`，轉成保守面試 case，未更新履歷。
- 已完成 `app_bi point-control-admin-operation Step 5` 的「不更新履歷 / 自傳」判定；這不是履歷深掃，也不是 Nick 開發痕跡確認。未補 Nick 本人 MR / ticket / commit / production issue 前，只保留為面試分析 case，不放入正式履歷。
- 已完成 `app_bi admin-config-redis-sync Step 1-5`，已遷移為新版結構；目前只作後台設定同步 Redis 的分析與面試素材，不更新履歷。
- 已完成 `app_bi point-control-admin-operation Step 1-5`，已遷移為新版結構；目前只作後台控制操作的分析與面試素材，不更新履歷。
- 已補上 flow 可讀性規則：每份 `flow.md` 前半要先有白話導讀、Code 分層、最小架構圖、正常流程圖與逐步說明，後半才進 Senior / Owner 分析。
- 已補上 push approval 防錯規則：需要 push 時，AI 必須直接觸發 `git push` approval 視窗，不再只用文字回覆本地已提交、等待 Nick 另外要求推送。
- 已新增 `projects/source-repo-inventory.md`，記錄本機來源 repo 索引；這只是導航，不是 code evidence 或履歷 claim。
- 已重整 `01-senior-owner-flow-inventory.md` 為 flow dashboard，避免下一步亂跳；完整分析仍放各 flow 的 `flow.md`。
- 已完成 `app_bi daily-game-record-summary Step 3`，確認 app_bi 查詢端與 game_job producer；目前只作報表 projection / 批次一致性分析素材，不更新履歷。
- 已完成 `app_bi daily-game-record-summary Step 4`，轉成保守面試 case；目前仍不更新履歷 / 自傳。
- 已完成 `app_bi daily-game-record-summary Step 5` 的「不更新履歷 / 自傳」判定；沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認前，只保留為面試分析素材。
- 已完成 `app_bi game-round-record-query Step 3`，確認 app_bi 查詢端、每日 `log_reel` 分表與 iwin_gameserver log writer 線索；目前只作玩家申訴 / troubleshooting 分析素材，不更新履歷。
- 已完成 `app_bi game-round-record-query Step 4`，轉成保守面試 case；目前仍不更新履歷 / 自傳。
- 已完成 `app_bi game-round-record-query Step 5` 的「不更新履歷 / 自傳」判定；app_bi 查詢端未見 Nick author，iwin_gameserver writer 的 Nick commit 線索應另開後端 flow 深挖。
- 已依 2026-05-15 KB 深度檢查 `app_bi`，補齊 project-level `architecture-map.md` 與 `career-interview.md`，並把 app_bi 本地落後 `origin/main` 4 commit 的 source repo 狀態寫入 Step / evidence；正式履歷仍不更新。
- 已完成 `game_job Step 1`，建立 `projects/iwin/game_job/README.md` 與 `step1-candidate-flows.md`；目前只作 Java batch / BI projection / third-party record backup 的候選 flow 盤點，不更新履歷。
- 已完成 `third_games_api gsc-transfer-bet-settle-rollback Step 4`，轉成 GSC seamless wallet callback 的保守面試 case；目前仍不更新履歷 / 自傳。
- 已完成 `third_games_api antplay-bet-settle-rollback Step 4`，建立 Antplay 舊版 bet / settle / rollback 三段式正式面試 case；目前只作 code-backed 面試素材，不更新正式履歷 / 自傳。
- 已完成 `third_games_api antplay-bet-settle-rollback Step 5`，單條 flow claim gate 結論維持 interview-only；不新增 `third_games_api` standalone 正式履歷 / 自傳；後續同 project 第四候選 `gsc-seamless-withdraw-deposit-cancel Step 5` 也已完成。
- 已完成 `third_games_api gsc-seamless-withdraw-deposit-cancel Step 3`，建立 GSC split withdraw / deposit / rollback / cancel 主學習包；確認 split endpoint、Mongo step 1 / 2 / 3、gameserver `GSC_BET / GSC_SETTLE / GSC_REFUND / GSC_OTHER` 與 failure window。production 是否仍走 split endpoint 待 Step 4 / spec 確認；不更新正式履歷 / 自傳。
- 已完成 `third_games_api gsc-seamless-withdraw-deposit-cancel Step 4`，轉成 GSC split endpoint 正式面試 case；可講 split state transition、adapter Mongo evidence vs gameserver wallet boundary、gameserver success but Mongo fail failure window、cancel / rollback step 3 語意與 `/transfer` production 主線待確認。後續 Step 5 claim gate 已完成，仍不更新正式履歷 / 自傳。
- 已完成 `third_games_api gsc-seamless-withdraw-deposit-cancel Step 5`，claim gate 結論維持 interview-only；不新增 `third_games_api` standalone 正式履歷 / 自傳。`third_games_api` GSC split endpoint path 未掃到 Nick / `10gt12nc` direct production commit；下游 GSC direct evidence 歸屬 `iwin_gameserver`。
- 已完成 `iwin third_games_api contribution claim consolidation`；2026-05-20 rolling / scoped 收口。`third_games_api` 本 repo 只有局部測試 / merge 線索，不新增 standalone 正式履歷主成果；下游 `iwin_gameserver` Antplay / GSC / PG direct evidence 要在 `iwin_gameserver` consolidation 正確歸位。
- 已完成 `game_api coupon-redeem-credit-grant Step 5`，確認 `10gt12nc` 有 game_api / iwin_gameserver coupon path-specific commits；可作 game_api project contribution consolidation evidence，不寫主導完整 coupon / reward owner。
- 已完成 `game_job daily-game-data-summary Step 5`，確認 `10gt12nc` 有 daily summary / 時區 / 留存 / 備份相關 path-specific commits；可作 game_job project contribution consolidation evidence，不寫完整 BI pipeline owner。
- 已完成 `game_job third-party-record-mongo-backup Step 5`，確認 `10gt12nc` 有 GSC backup 分批查詢與 batch size 調整 direct commits；可作 game_job project contribution consolidation evidence，不寫完整第三方紀錄備份 owner。
- 已完成 `game_job coin-flow-batch-projection Step 4`，建立金幣流水 / 玩家行為 projection 主學習包並轉成正式面試 case；目前只作 code-backed 面試分析素材，不更新正式履歷 / 自傳。
- 已完成 `game_job coin-flow-batch-projection Step 5`，確認目前未見足夠 Nick / `10gt12nc` direct path evidence；正式履歷 / 自傳不更新，只保留為 code-backed 面試素材。
- 已完成 `game_job online-payment-data-cleaning Step 3`，建立充值 / 提現資料清洗與每日經濟資料主學習包；目前只作 code-backed 面試分析素材，不更新正式履歷 / 自傳。
- 已完成 `game_job online-payment-data-cleaning Step 4`，轉成 payment reporting projection 的正式面試 case；目前仍只作 code-backed 面試分析素材，不更新正式履歷 / 自傳。
- 已完成 `game_job online-payment-data-cleaning Step 5`，判定不更新正式履歷 / 自傳；本 flow 保留為 payment reporting projection / replay-safe 的 code-backed 面試案例。
- 已完成 `game_job partition-table-creation Step 3`，建立每日 / 每月分表建立主學習包；目前只作 code-backed 面試分析素材，不更新正式履歷 / 自傳。
- 已完成 `game_job partition-table-creation Step 4`，轉成 table rollover / schema rollout reliability 的正式面試 case；目前仍只作 code-backed 面試素材，不更新正式履歷 / 自傳。
- 已完成 `game_job partition-table-creation Step 5`，判定不更新正式履歷 / 自傳；本 flow 保留為 table rollover reliability 的 code-backed 面試案例。
- 已完成 `payment payment-provider-callback Step 5`，判定不更新正式履歷 / 自傳；2026-05-26 code-first audit 後，本 flow 改定位為 payment provider callback 狀態風險 / compensation 的保守面試素材，不包裝成完整 money correctness / wallet / reconciliation。
- 已完成 `payment withdrawal-auto-review-refund Step 5`，判定不更新正式履歷 / 自傳；本 flow 保留為提款、自動審核 / 自動出款、失敗退款的一致性與補償面試素材。
- 已完成 `payment payment-order-provider-request Step 5`，完成 provider request claim gate；已確認 Nick 在 Pay4z / NaNapay / BFPAY / NimTestPay 等 provider request / query / callback 相關 code 有 path-specific commits，2026-05-20 consolidation 重新覆核後又補入 GoldenPay direct commits；正式履歷可用「參與」口徑，不寫主導完整金流。
- 已完成 `payment manual-order-review-repair Step 3`，建立人工審核 / 補單 / 訂單修復主學習包；目前只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment manual-order-review-repair Step 4`，轉成可面試 case；目前仍只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment manual-order-review-repair Step 5`，判定不更新正式履歷 / 自傳；人工審核 / 補單 / 修單只保留為面試分析素材。
- 已完成 `payment payment-channel-config-selection Step 3`，建立支付列表 / 商戶 / 玩家層級 / 提現設定選擇的 runtime config 主學習包；目前只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment payment-channel-config-selection Step 4`，轉成可面試 case；目前仍只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment payment-channel-config-selection Step 5`，判定不更新正式履歷 / 自傳；payment Top 5 代表 flow 已收斂，但不代表全 payment project 已完成。
- 已完成 `iwin payment contribution claim consolidation`；2026-05-20 已重新覆核並補入 GoldenPay direct evidence，2026-05-26 再做 code-first claim audit。Nick 本人確認加上 `10gt12nc` commits / branches / 重要 diff，可把 payment 升級為「部分真實開發過」，但 claim 只限 provider / 商戶對接、payment / withdraw order consistency、查單與人工補償邊界；不是 payment 全量 flow 完成，也不寫完整金流 / wallet / ledger / reconciliation owner。
- 已完成 `iwin_gameserver third-party-transfer-in-out Step 5`；原 Step 5 判定暫不更新正式履歷 / 自傳，2026-05-20 project-level consolidation 已用 Nick / `10gt12nc` direct commits 升級為可併入第三方 provider 投派整合保守 claim。
- 已完成 `iwin_gameserver center-http-deposit-withdraw Step 3`，建立 center_http 上分 / 下分主學習包；目前只作 code-backed 面試素材，不更新正式履歷 / 自傳。
- 全域下一步狀態：目前沒有預設 project flow 下一步；請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
- 已完成 `iwin_gameserver center-http-deposit-withdraw Step 5`，追完 source repo refs、path-specific history、重要 diff 與 blame；結論維持 code-backed interview-only，不更新正式履歷 / 自傳。後續同 project `game-spin-settlement-log-reel` 與 `bet-target-set-query` 也已完成到 Step 5。
- 已完成 `iwin_gameserver game-spin-settlement-log-reel Step 3`，選 `slots-game40-sgj` 作代表 game，追一般 slot spin 到 center wallet sync 與 `log_reel` 寫入；目前只作 code-backed 面試素材，不更新正式履歷 / 自傳。
- 已完成 `iwin_gameserver game-spin-settlement-log-reel Step 4`，轉成正式面試 case；不更新正式履歷 / 自傳。
- 已完成 `iwin_gameserver game-spin-settlement-log-reel Step 5`，一般 Game40 spin 維持 interview-only；provider log reel / 投派整合 direct evidence 回填既有 project-level claim，不新增一般 slot spin 履歷 bullet。
- 已完成 `k3s-deploy gameserver-phased-rollout Step 5`，claim gate 結論維持 interview-only；可作 rollout / rollback / observability 的保守面試 case，不更新正式履歷 / 自傳。
- 已完成 `game_api partner-deposit-withdraw-bill Step 5`，判定不更新正式履歷 / 自傳；本 flow 保留為 Partner API 上分 / 下分 / 查單的 code-backed 面試案例。
- 已補上待辦事項優先規則：當 Nick 要的是「待辦、缺口、KB 維護、優先順序」時，AI 先維護 todo / KB / inventory，只列候選下一步並等待 Nick 下 Step；不得把缺口自動開工成 flow Step。
- 已完成 `game_api agent-bonus-receive-transfer Step 3`，建立代理佣金領取 / 轉帳主學習包；目前只作 code-backed 面試分析素材，不更新正式履歷 / 自傳。後續 `game_api contribution claim consolidation` 已完成，仍維持本 flow 不進正式履歷。
- 已完成 `game_api agent-bonus-receive-transfer Step 4`，轉成代理佣金領取 / 轉帳的正式面試 case；目前仍只作 code-backed 面試分析素材，不更新正式履歷 / 自傳。
- 已完成 `game_api agent-bonus-receive-transfer Step 5`，判定不更新正式履歷 / 自傳；本 flow 保留為代理佣金領取 / 轉帳的 code-backed 面試案例。
- 已完成 `iwin game_job contribution claim consolidation`；2026-05-20 已重新覆核。Nick / `10gt12nc` 在 daily summary 與 GSC backup path 有 direct commits，可把 game_job 升級為「部分真實開發過」，但不寫完整 game_job / BI pipeline / retention owner。
- 已完成 `iwin app_bi contribution claim consolidation`；已依 rolling / scoped 規則重新確認為 negative 收口，結論是不放正式履歷主成果，只作後台入口 / BI / control plane 的 code-backed 面試分析素材。
- 已完成 `iwin game_api contribution claim consolidation`；2026-05-20 已重新覆核。Nick / `10gt12nc` 在 coupon redeem / grant 與 `iwin_gameserver` bet target handler 有 direct commits，可把 `game_api` 升級為「部分真實開發過」，但正式履歷只採 coupon 保守 claim；partner / agent bonus 只作 code-backed 面試素材。
- 已完成 `iwin bi_share contribution claim consolidation`；未掃到 Nick / `10gt12nc` direct production commits，結論是不放正式履歷主成果，只作 legacy Laravel / 分享 / 佣金 / BI 報表 / GM repair 的 code-backed 面試分析素材。
- 已完成 `iwin iwin_gameserver contribution claim consolidation`；2026-05-20 rolling / scoped 收口。Nick / `10gt12nc` 在 Antplay / GSC / PG 第三方 provider 投派整合、gameserver money job、`GamePlayer` log dispatch 與 log reel path 有 direct commits，可保守放「參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接」；center-http 上分 / 下分仍是 code-backed 面試素材，不擴張成完整 gameserver / wallet owner。
- 已完成 `iwin iwin-workspace contribution claim consolidation`；2026-05-20 rolling / scoped 收口。Nick / `10gt12nc` 有大量 workspace KB / docs / environment index / tool commits，可支撐 cross-repo system reconstruction 與知識庫治理能力，但不新增 standalone 正式履歷主成果，不反向包裝成子 repo 業務開發。
- 已完成 `ugsoft ugsoft-admin-api contribution claim consolidation`；2026-05-20 rolling 收口。Nick / `10gt12nc` 在 login / JWT / RBAC、商戶 / provider 白名單、超級代理、報表查詢、風控監控、RabbitMQ request log / bet record 與 Quartz / report job 有大量 direct commits，可保守補入後台 API / control plane 與非同步資料處理經驗；不寫完整 UG 平台、完整 provider gateway、完整 wallet / money flow 或完整 RabbitMQ architecture owner。
- 已完成 `ugsoft ugsoft-connector-api contribution claim consolidation`；2026-05-20 rolling 收口。Nick / `10gt12nc` 在 AntPlay / DerPlay provider adapter、login / balance / transfer in-out / bet-settle / callback、request / bet record MQ、transfer wallet compensation、分表與 provider reliability 有 direct commits / code evidence，可保守補入 provider connector / gateway 經驗；不寫完整 connector architecture owner、全部 provider owner、完整 wallet / ledger / reconciliation owner 或 exactly-once / outbox owner。
- 已完成 `ugsoft ugsoft-connector-api provider-callback-bet-settle-to-mq Step 5`；2026-05-27 已完成 claim gate，可回填 project-level provider connector / callback / MQ evidence；不直接更新 `05 / 08 / 04 / 17`，不寫完整 exactly-once / outbox / reconciliation owner。
- 已完成 `ugsoft ugsoft-connector-api request-bet-record-mq-sync Step 5`；2026-05-27 已完成 claim gate，可回填 project-level job-driven bet record sync / late data / MQ evidence；不直接更新 `05 / 08 / 04 / 17`，不寫完整 reconciliation / outbox / exactly-once owner。`ugsoft-connector-api` 本批三條代表 flows 已全部 Step 5；若繼續 connector，下一步應做 `contribution claim consolidation refresh`。
- 已完成 `ugsoft ugsoft-workspace contribution claim consolidation`；2026-05-20 rolling 收口。這是 workspace / docs / harness / runbook supporting evidence，可支撐 cross-repo system reconstruction、工程規範、local / deploy harness、migration / release 風險整理；不新增 standalone 正式履歷主成果，也不反向包裝成 service runtime code。
- 已完成 `antplay antplay-slot-admin-api contribution claim consolidation`；2026-05-20 rolling 收口。Nick / `10gt12nc` 在 admin / merchant auth、商戶 / Game API 白名單、超級代理、玩家 / 投注 / request log / 報表查詢、RTP / 暗池 / 活動風控監控、RabbitMQ request log / 風控通知與 Quartz job 有大量 direct commits，可保守補入後台 API / risk ops / async data processing 經驗；不寫完整 AntPlay slot platform、game runtime、wallet / reconciliation 或完整 RabbitMQ owner。
- 已完成 `antplay antplay-slot-game-api contribution claim consolidation`；2026-05-21 refreshed 收口。Nick / `10gt12nc` 在 game init、bet / settle / rollback、轉帳錢包、bet record / request log / transfer transaction 分表、RabbitMQ request log、Quartz 補通知、RTP / dark pool / player control 有大量 direct commits，且五條代表 flows 均已 Step 5 並回填 project-level claim。可保守補入 game runtime / betting-settlement / transfer wallet / async log / high-traffic table governance / runtime decision 經驗；不寫完整 AntPlay slot platform、完整遊戲數學 / RTP 策略、wallet / ledger / reconciliation、完整 sharding platform 或完整 RabbitMQ / Kafka owner。
- 已完成 `antplay antplay-slot-game-api Step 1`；2026-05-21 已建立 candidate flows：`slot-bet-settle-rollback`、`transfer-wallet-money-in-out`、`request-log-rabbitmq-async`、`bet-record-sharding-schema-route`、`runtime-rtp-darkpool-player-control`。本 Step 只作 Flow Track 選題，不直接更新 05 / 08；source fetch 失敗一次後依本地 refs / 本地 working tree 保守分析。
- 已完成 `antplay antplay-slot-game-api Step 2`；2026-05-21 已比較候選 flows，第一條代表 flow 選定 `slot-bet-settle-rollback`，後續 Step 3 已完成。
- 已完成 `antplay antplay-slot-game-api slot-bet-settle-rollback Step 3`；2026-05-21 已建立新版 flow package，確認 `/game/bet` 主線、bet record state、single / transfer wallet settle、request log MQ、notify job 與 deadlock 補償風險。Step 3 不直接更新 05 / 08；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-game-api slot-bet-settle-rollback Step 4`；2026-05-21 已轉成正式面試 case，補 30 秒 / 90 秒 / 3 分鐘講法、STAR、failure scenarios、Senior / Lead 追問與 owner 改善方向。Step 4 不直接更新 05 / 08；後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-api slot-bet-settle-rollback Step 5`；2026-05-21 已追 `a2b2af5` / `54078fe` / `31d7a46` / `d3e0002` 等重要 diff，確認本 flow 可作 `antplay-slot-game-api` project-level 履歷 claim 強化 evidence；不單獨寫完整下注結算 / wallet owner，且不宣稱 deadlock compensation 已完整落地。
- 已完成 `antplay antplay-slot-game-api transfer-wallet-money-in-out Step 3`；2026-05-21 已建立 transfer wallet 轉入 / 轉出 / 全額轉出 / 查單學習包，確認簽名、防重、transaction、lookup、DB / Redis balance 與 failure window。Step 3 先作 code-backed 面試素材，不直接更新 05 / 08；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-game-api transfer-wallet-money-in-out Step 4`；2026-05-21 已整理 30 秒 / 90 秒 / 3 分鐘講法、STAR、failure scenarios、Senior / Lead 追問與 owner 改善方向。
- 已完成 `antplay antplay-slot-game-api transfer-wallet-money-in-out Step 5`；2026-05-21 已追 `54078fe`、`718a207`、transfer table path commits、current blame 與 final behavior。結論是可回填 project-level transfer wallet / DB + Redis consistency / transaction lookup evidence；不單獨寫完整 transfer wallet owner，也不直接更新 05 / 08。
- 已完成 `antplay antplay-slot-game-api request-log-rabbitmq-async Step 3`；2026-05-21 已建立 request log RabbitMQ 非同步化學習包，確認 game-api producer、RabbitMQ exchange / queue / routing key、admin-api consumer 與落庫 SQL。Nick / `10gt12nc` 有 #774 producer / consumer direct evidence；Step 3 只作 async audit / observability 面試素材，不直接更新 05 / 08；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-game-api request-log-rabbitmq-async Step 4`；2026-05-21 已轉成正式面試 case，補 30 秒 / 90 秒 / 3 分鐘說法、STAR、failure scenarios、Senior / Lead 追問與 owner 改善方向。後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-api request-log-rabbitmq-async Step 5`；2026-05-21 已追 #774 producer / consumer 重要 diff、current code 與 blame，確認可回填 project-level async audit / observability claim；不單獨寫完整 RabbitMQ owner、exactly-once、outbox、DLQ / retry / alert，且 routing key 最終格式修正屬他人 context evidence。
- 已完成 `antplay antplay-slot-game-api bet-record-sharding-schema-route Step 3`；2026-05-21 已建立 bet record / request log / transfer transaction 分表與 schema routing 學習包，確認 `@UseSchema` schema route、`pt_day` / `agent_id` partition key、table creator service 與 current create-table job disabled caveat。Step 3 先作 high-traffic data governance 面試素材，不直接更新 05 / 08；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-game-api bet-record-sharding-schema-route Step 4`；2026-05-21 已補 30 秒 / 90 秒 / 3 分鐘講法、STAR、Senior / Lead 追問與正式面試邊界。後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-api bet-record-sharding-schema-route Step 5`；2026-05-21 已追 schema route、#167 bet record 分表、db_partition v2、table creator、current job disabled 與 logical-to-physical mapping evidence。結論是可回填 project-level high-traffic table governance / schema route / partition key evidence；不單獨寫完整 sharding owner，不直接更新 05 / 08。
- 已完成 `antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 3`；2026-05-21 已追 `/game/bet` runtime path、RTP cache、dark pool total bet / win、player control、jackpot force respin 與 `SlotMathFacade` math contract。結論是可作 game API runtime decision 面試素材初版；後續 Step 4 / Step 5 已完成。
- 已完成 `antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 4`；2026-05-21 已補 30 秒 / 90 秒 / 3 分鐘講法、STAR、Senior / Lead 追問、可用反問與正式面試邊界。後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 5`；2026-05-21 已追 `GameFacade#bet` respin loop、dark pool `maxWin`、timeout refund branch、player control setData caller、deadlock / afterBet failure window、RTP cache / player control / jackpot current blame。結論是可回填 project-level runtime decision claim；不寫完整 RTP / math / jackpot owner，不直接更新 05 / 08。
- 已完成 `antplay antplay-slot-game-job contribution claim consolidation refresh`；2026-05-25 refreshed 收口。五條代表 flows 均已 Step 5；Nick / `10gt12nc` 在 Kafka consumer / Quartz job、代理玩家報表 projection、big-win notification、report currency / key 修正與 db partition / job config 有 direct commits，activity accumulated bet 為 Nick merge + code-backed supporting evidence，settle pool / darkpool 只作 analysis-first。可保守補入 job / event processing / report projection / notification / partition 經驗；不寫完整 Kafka event platform、reward platform、settle pool / risk / jackpot、DB sharding / schema routing、BI / report platform 或完整 AntPlay slot platform owner。
- 已完成 `antplay antplay-slot-game-job Step 1`；2026-05-22 已建立 candidate flows：`proxy-user-data-report-projection`、`activity-accumulated-bet-voucher`、`big-win-notification`、`settle-pool-monitor-darkpool-sync`、`db-partition-job-report-routing`、`kafka-job-foundation-platform-mock-rollback`。本 Step 只作 Flow Track 選題，不直接更新 05 / 08；source fetch 失敗一次後依本地 refs / 本地 working tree 保守分析。
- 已完成 `antplay antplay-slot-game-job Step 2`；2026-05-22 已比較候選 flows，第一條代表 flow 選定 `proxy-user-data-report-projection`，因 Nick / `10gt12nc` direct evidence 最集中，且最能代表 Kafka event -> DB projection -> Quartz summary / backup / delete 的 Senior Backend 題材。
- 已完成 `antplay antplay-slot-game-job proxy-user-data-report-projection Step 3`；2026-05-22 已建立 Kafka `settled_bets` -> `ag_report_player` projection -> Quartz summary / backup / delete 學習包，補 report key、currency、in-memory map、batch insert / update、summary partial failure 與 claim boundary。Step 3 不直接更新 05 / 08；下一步是同 flow Step 4。
- 已完成 `antplay antplay-slot-game-job proxy-user-data-report-projection Step 4`；2026-05-22 已整理成正式面試 case，補 30 秒 / 90 秒 / 3 分鐘說法、STAR、failure scenarios、Senior / Lead 追問、常見陷阱、反問與不可誇大邊界。Step 4 不直接更新 05 / 08；後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-job proxy-user-data-report-projection Step 5`；2026-05-25 已追 current blame、important diffs、Kafka retry / dead-letter-topic config 與 DB constraint 缺口。結論是可回填 project-level report projection / Quartz summary evidence；不單獨更新 05 / 08。後續 Rank 2 `activity-accumulated-bet-voucher Step 3` 已完成。
- 已完成 `antplay antplay-slot-game-job activity-accumulated-bet-voucher Step 3`；2026-05-25 已建立活動累計投注送 voucher / free spin 學習包，確認 Kafka `settled_bets` -> activity config -> Redis accumulate / awarded count -> `BetVoucherService` 發放路徑。此 flow 目前是 code-backed + Nick merge evidence，implementation 主要為 Gill / Arnold / Eliot context，不直接更新 05 / 08；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-game-job activity-accumulated-bet-voucher Step 4`；2026-05-25 已整理成正式 reward correctness 面試 case，補 30 秒 / 90 秒 / 3 分鐘說法、STAR、failure scenarios、Senior / Lead 追問與 owner 改善方向。Step 4 不直接更新 05 / 08；後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-job activity-accumulated-bet-voucher Step 5`；2026-05-25 已補查 `BetVoucherService#addVoucher` 下游 idempotency / unique key、Nick merge evidence 與 current blame。結論是本 repo 沒有下游 implementation / DB unique key evidence，`refId` 每次 UUID 不足以證明 deterministic idempotency；本 flow 只作 reward correctness 面試素材與 project-level supporting evidence，不單獨更新 05 / 08。後續 Rank 3 `big-win-notification Step 3` 已完成。
- 已完成 `antplay antplay-slot-game-job big-win-notification Step 3`；2026-05-25 已建立中大獎通知 flow learning package，確認 `#303` direct evidence、Kafka `settled_bets` -> big-win rule -> `push_user` topic、玩家遮罩、game translation、producer async failure 與 privacy boundary。Step 3 不直接更新 05 / 08；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-game-job big-win-notification Step 4`；2026-05-25 已整理正式 derived notification 面試 case，補 30 秒 / 90 秒 / 3 分鐘說法、STAR、failure scenarios、Senior / Lead 追問、常見陷阱、反問與不可誇大邊界。Step 4 不直接更新 05 / 08；後續 Step 5 已完成。
- 已完成 `antplay antplay-slot-game-job big-win-notification Step 5`；2026-05-25 已補查 `_id` / bet id 去重、`BetIdPersistence` 用途與下游 `antplay-push` bridge / privacy 邊界。結論是可作 project-level big-win notification supporting evidence，但 `_id` 不是 deterministic key、`BetIdPersistence` 不是通知去重、下游未見 `fullPlayerName` filtering，不單獨更新 05 / 08；後續 Rank 4 `settle-pool-monitor-darkpool-sync Step 5` 已完成。
- 已完成 `antplay antplay-slot-game-job settle-pool-monitor-darkpool-sync Step 5`；2026-05-25 已建立 Kafka `settled_bets` -> pool grouping -> `settled_pool` increment -> Redis reset snapshot -> alert 正式面試 case 與 claim gate。結論是 code-backed / analysis-first，未找到 Nick / `10gt12nc` direct path-specific evidence，不回填正式履歷；後續 Rank 5 `db-partition-job-report-routing Step 3` 已完成。
- 已完成 `antplay antplay-slot-game-job db-partition-job-report-routing Step 3`；2026-05-25 已建立 DB partition / report schema routing 學習包，確認 `@UseSchema` / `SchemaRouteAspect` / `SchemaContextHolder`、`pt_bet_record` / `pt_request_log` partition query、`ag_report_player` report path repair，以及 `b754dae db_partition v2`、`6866866 fix ag_report_player` direct evidence。此 flow 可作 project-level 分表 / report path 維護 supporting evidence，但不單獨更新 `05 / 08`；後續 Step 4 已完成。
- 已完成 `antplay antplay-slot-game-job db-partition-job-report-routing Step 4`；2026-05-25 已整理成正式面試 case，補 30 秒 / 90 秒 / 3 分鐘說法、STAR、failure scenarios、Senior / Lead 追問、常見陷阱、反問與不可誇大邊界。Step 4 不直接更新 `05 / 08`；下一步是同 flow Step 5 claim gate。
- 已完成 `antplay antplay-slot-game-job db-partition-job-report-routing Step 5`；2026-05-25 已補 current data source routing config、report call sites、path-specific blame 與 important commit stat。結論是 `db_partition v2`、`fix`、`fix ag_report_player` 可作分表 / report path 維護 direct evidence；current `@UseSchema` framework 是多人後續，G3 data source mapping 未確認，不單獨更新 `05 / 08`。後續 `antplay-slot-game-job contribution claim consolidation refresh` 已完成。
- 已完成 `antplay math-core contribution claim consolidation`；2026-05-20 rolling 收口。Nick / `10gt12nc` 在 SlotMath contract、debugBet VO、currency / fixedMultiBet、RTP type、jackpot / symbol 有 direct commits，可保守補入 slot math core / contract / debug tooling 經驗；不寫完整 slot math framework 或完整 RTP 策略 owner。
- 已完成 `antplay *-math contribution claim consolidation`；2026-05-21 refreshed / grouped 收口。71 個 `*-math` repo 中 49 個有 Nick / `10gt12nc` direct commits，強 evidence 是 `sph-math`、`spn-math`、`sfm-math`、`setl-math`、`sdt-math`、`slc-math`；五條代表 flow 已全部 Step 5 並回填 project-level claim。可保守補入 slot math core / 多個 slot math module 維護與驗證經驗；不寫主導全部 math module、完整遊戲數學模型、完整 RTP 策略、完整 simulator / certification owner、完整 jackpot pool 或單一遊戲 feature owner。
- 已完成 `antplay math-workspace contribution claim consolidation`；2026-05-20 rolling 收口。Nick 有 workspace KB / docs commits，只作 cross-math code reading / validation workflow supporting evidence，不作 standalone 正式履歷主成果。
- 已完成 `antplay platform-mock contribution claim consolidation`；2026-05-20 rolling 收口。Nick / `10gt12nc` 有局部 bet / money_inout failure injection commits，只作 provider rollback / compensation testing supporting evidence，不作正式主成果。
- 已完成 `antplay buffer-id contribution claim consolidation`；2026-05-20 rolling 收口。未掃到 Nick / `10gt12nc` direct commits，只作 ID generator learning-only。
- 已完成 `rolling resume package`；2026-05-25 已依目前所有 Contribution Claim Consolidation 與最新 Step 5 收口 refresh `05-resume-master-zh.md`、`08-application-autobiography-zh.md` 與 `17-salary-negotiation.md`。已吸收 `antplay-slot-game-api` 五條代表 flows Step 5 與 project-level consolidation refresh、`third_games_api` 本批 Step 5、`k3s-deploy gameserver-phased-rollout Step 5` 的 interview-only 邊界，以及 `antplay-slot-game-job` 五條代表 flows Step 5 / contribution consolidation refresh。這是可先投遞的草稿，不是 final；後續 flow 深掃、Step 5 或新 consolidation 必須回填修正。
- 已完成 `04 / 面試 case 對齊檢查`；2026-05-25 已把 `08` 的 104 主打 bullet 對應到 `04` 的 8-10 條可切換 case，並標出證據層級、3 分鐘主軸與不可誇大邊界。下一步不再是補履歷或平均掃 repo，而是把三條主力 case 練成 90 秒 / 3 分鐘口說版本。
- 已完成 `三條主力 case 90 秒 / 3 分鐘口說打磨` 草稿；2026-05-25 已在 `04-interview-casebook.md` 補 payment provider、wallet / bet-settle、Kafka / report projection 三條主力 case 的 90 秒版本、3 分鐘版本、常見追問與「不需要全會」判斷。這只是稿件完成；面試口說練習目前先暫停，等 Nick 明確要求才開始互動練習。
- 已補 2026 / 2027 台灣轉職月份策略；主旺季是 2-4 月，2026 因春節較晚與轉職季拉長可延到 4-5 月；9-10 月是第二波，11-1 月適合準備 / 卡位。對 Nick 的節奏是 2026 下半年整理履歷與低壓 market check，2027/2-4 正式主攻，2027/9-10 作備案。
- 已補面試準備 70 / 20 / 10 邊界：70% 放 production case / system design / claim boundary，20% Java / SQL / transaction / Redis / MQ 基本判斷遇到再補，10% LeetCode / coding test 只作投遞前保險。不得把 AI 時代 coding 焦慮變成刷題主線。
- 已補面試題 / 複習包規則：面試題必須從三條主力 production case 長出來，不建立泛用 Java / SQL / LeetCode 300 題題庫。第一輪只圍繞 payment provider、wallet / bet-settle、Kafka / report projection 產 90 秒版、3 分鐘版、追問題庫、回答要點、誇大陷阱與 case-specific 基本功。

## 下一步

### 0. 待辦事項優先規則

Nick 若先問「缺啥、待辦、優先順序、KB 要不要補」，AI 必須先維護本檔與相關 index，不直接開工 flow Step。

`contribution claim consolidation` 可以先做 rolling / scoped 版，用來支援近期履歷與面試材料；不必等 Step 2 定義的本批代表 flows 全部完成 Step 5。執行時除了掃 code，也要重讀該 project 已完成 flow KB 與目前可讀的 project 文件。未完成 flow 必須標為待深掃 / 待回填，不能宣稱 final 或全 project 已完整深掃；final consolidation 才等本批代表 flows 全部 Step 5 後再校正。

2026-05-25 更新：`antplay *-math fixed-multi-bet-currency-math-core-compatibility Step 5` 已完成，`rtp-reel-strip-simulation-validation Step 5` 已完成，`buy-free-scatter-rtp3-result-contract Step 5` 已完成，`jackpot-symbol-hit-and-prize-scaling Step 5` 已完成，`special-wild-feature-state-transform Step 5` 已完成，且 `*-math contribution claim consolidation` refresh 已完成。`antplay-slot-game-api slot-bet-settle-rollback Step 5`、`transfer-wallet-money-in-out Step 5`、`request-log-rabbitmq-async Step 5`、`bet-record-sharding-schema-route Step 5`、`runtime-rtp-darkpool-player-control Step 5`、`antplay-slot-game-api contribution claim consolidation` refresh、`antplay-slot-game-job` 五條代表 flows Step 5 / contribution consolidation refresh 與 `rolling resume package` 也已完成。`iwin iwin_gameserver center-http-deposit-withdraw Step 5`、`game-spin-settlement-log-reel Step 5`、`bet-target-set-query Step 5`、`third_games_api gsc-transfer-bet-settle-rollback Step 5`、`third_games_api oneapi-wallet-bet-result Step 5`、`third_games_api antplay-bet-settle-rollback Step 5`、`third_games_api gsc-seamless-withdraw-deposit-cancel Step 5` 與 `k3s-deploy gameserver-phased-rollout Step 5` 已完成；2026-05-25 rolling resume package、104 投遞欄位檢查、`04 / 面試 case 對齊檢查` 與三條主力 case 口說稿已回填，`08 / 17` 已可作通用 Senior Java Backend / Platform Backend 投遞版。Nick 暫時沒有特定 JD 時，不要一直要求貼 JD；面試口說練習先暫停，等 Nick 明確要求才開始；有 JD 時才客製。

來源 repo 若是內網 GitLab 或 remote 不可達，fetch 失敗一次後不要反覆重試；改用本地 refs / 本地工作樹保守分析，並標示未確認最新遠端。不要把內網 URL、IP 或敏感 remote 細節寫進 vault。

### 0.1 Senior 對標收斂終點

這份 todo 不是無限 backlog。對標 Senior Java Backend / Platform Backend 的收斂終點是：3-5 個可放履歷的 project-level claim、8-10 條可講 3 分鐘且能抗追問的 production flow、乾淨的 claim boundary，以及 `05 / 08 / 04 / 17` 對齊。

方向校正：這個收斂終點不是「Nick 一個人把整個公司大系統全量掃完」。完整大系統本來是團隊工作；個人準備要做到的是：代表 flows 能講清楚、project claims 保守可信、跨系統邊界能畫出主要協作與風險。domain-level 大地圖是可選架構補強，不是目前投遞前必做。

目前回答「還剩多少」時，優先用這個分類：

必做收口：

1. 已完成 `iwin k3s-deploy gameserver-phased-rollout Step 5`
2. 已完成 2026-05-25 `rolling resume package`，最新 claim / case 已對齊 `05 / 08 / 17`
3. 已完成 2026-05-25 `104 投遞欄位檢查`，`08` 可作通用 104 投遞稿
4. 已完成 2026-05-25 `04 / 面試 case 對齊檢查`，104 主打 bullet 已對應到 8-10 條可切換 case 與 claim boundary

資深補強，可選：

1. `ugsoft-connector-api contribution claim consolidation refresh`；第一條 `transfer-wallet-in-out-query`、第二條 `provider-callback-bet-settle-to-mq`、第三條 `request-bet-record-mq-sync` 均已完成 Step 5，後續可刷新 project-level claim
2. `iwin system map v1`：補 `projects/iwin/architecture-map.md` / `integration-map.md`，把 game_api、game_job、payment、third_games_api、iwin_gameserver、app_bi、bi_share、k3s-deploy 的協作關係與 claim boundary 收成 domain-level 大地圖。
3. 依目標 JD 補 1 條 payment / provider / MQ 類缺口 flow；沒有 JD 時先不新增。

暫不建議做：

- 官網、前端、workspace、mock、低價值 legacy repo 平均整理。
- 71 個 `*-math` repo 全量逐一深掃；目前只保留代表 flow 與 grouped claim。
- 已完成 contribution consolidation 且沒有新 evidence 的 project 反覆重做。
- 為了追求「全量完整」而把所有 domain-level map、所有 repo、所有 flow 都排成必做。這會把團隊級知識管理誤當成個人求職前置條件。

完成必做收口，且視需要補 2 個非 iwin project 代表 flow 後，就可以停止大規模整理，改成投遞、面試練習與依職缺補洞。

目前深掃 `nick-vault` 後，應放入待辦的缺口：

0. `Completeness Audit 規則補強`：2026-05-26 發現 `ugsoft` 已有 contribution consolidation，但當時沒有任何 Step 1 / Step 2 / `flows/`，先前「全掃 / 都完整」判斷混淆了 Career Track 與 Flow Track。`ugsoft-connector-api Step 1 / Step 2` 已補上，且本批三條代表 flow 已全部 Step 5；但 `ugsoft-admin-api` 仍只有 Career Track，connector 也尚未做 Step 5 後的 contribution refresh。之後凡是回答「全掃、都完整、還缺什麼、flow 都完整嗎」，必須逐 project 分列 Flow Track / Career Track / Domain Map。Career Track 完成只能表示履歷 claim 可用；不能表示 flow 完整。
0.1 `四 domain flow completeness audit`：已建立 `projects/source-repo-flow-audit.md`，盤點 `/Users/nick/Git/antplay`（排除 `*-math`）、`/Users/nick/Git/iwin`、`/Users/nick/Git/ugsoft`、`/Users/nick/Git/DevOps` project folders。結論：不要全 repo 開工；只把缺口記入待辦。`ugsoft-connector-api Step 1 / Step 2` 已完成，本批三條代表 flow `transfer-wallet-in-out-query Step 5`、`provider-callback-bet-settle-to-mq Step 5`、`request-bet-record-mq-sync Step 5` 均已完成；下一步若繼續 connector 是 `contribution claim consolidation refresh`。其次 `ugsoft-admin-api Step 1 / Step 2`，再其次 `antplay-slot-admin-api Step 1 / Step 2`。`payment-thirdparty-simulator` 經 iwin re-audit 後降為 payment 測試 supporting evidence，不列 project Flow Track 主待辦；`openobserve`、`antplay-api-deploy` 只作可選 Platform 加強；官網、前端、workspace、bot、notify、tool web、mock 與無 Nick direct commits repo 暫不建議。
0.2 `AntPlay re-audit`：2026-05-26 已重新掃 `/Users/nick/Git/antplay` 各 repo 的 remote refs、Nick / `10gt12nc` commits、主要 module 與既有 KB。結論：AntPlay 沒有新的必做缺口；真正值得補的只有 `antplay-slot-admin-api` Flow Track Step 1 / Step 2，而且是可選補強，不是投遞前必做。`antplay-push`、`platform-mock`、`math-core` 不新增主待辦，只作 supporting / 已收斂素材。
0.3 `iwin re-audit`：2026-05-26 已重新掃 `/Users/nick/Git/iwin` 各 repo 的 remote refs、Nick / `10gt12nc` commits、主要 module / path history、`payment-thirdparty-simulator` code path 與既有 `projects/iwin` KB。結論：iwin 沒有新的「真正值得補」的 project Flow Track 必做缺口；`payment`、`game_api`、`game_job`、`third_games_api`、`iwin_gameserver`、`app_bi` 已有足夠代表 flows / consolidation。`payment-thirdparty-simulator` 只作 payment provider contract / callback 測試 supporting evidence，不升級主線、不放主履歷。唯一保留的 iwin 可選補強是 `iwin system map v1`，屬於 domain-level architecture / integration map，不是投遞前必做。
0.4 `UGSoft re-audit`：2026-05-26 已重新掃 `/Users/nick/Git/ugsoft` 各 repo 的 remote refs、Nick / `10gt12nc` commits、主要 module / path history 與既有 `projects/ugsoft` KB。結論：UGSoft 仍有真正值得補的 Flow Track，但不需要全 repo 平均掃。`ugsoft-connector-api` 本批三條代表 flow `transfer-wallet-in-out-query Step 5`、`provider-callback-bet-settle-to-mq Step 5`、`request-bet-record-mq-sync Step 5` 均已於 2026-05-27 完成。若繼續 connector，下一步是 `contribution claim consolidation refresh`。第二個 project 方向是 `ugsoft-admin-api Step 1 / Step 2`。`official-web-v3`、`ugsoft-admin-web`、`ugsoft-workspace` 不列主待辦；workspace 只作 supporting evidence。這些是待辦，不代表本輪已授權開工。
0.5 `DevOps re-audit`：2026-05-26 已重新掃 `/Users/nick/Git/DevOps/primestar` 各 repo 的 remote refs、Nick / `10gt12nc` / `arnold` commits、manifests / docker-compose / CI / observability docs 與 path history。結論：DevOps 沒有新的 Senior Backend 主履歷 Flow Track 必做缺口。Nick 已確認 `arnold` 是主管帳號，不是 Nick direct evidence；`antplay-docker-deploys` 只能作主管 / 團隊 deployment context 或 learning / supporting，不作 Nick Flow Track、不放主履歷。`openobserve` / `kafka` 無 Nick / `arnold` commits，且 source 含敏感設定，只作 learning-only，不列待辦。

1. `rolling resume package`：已於 2026-05-25 回填 `third_games_api`、`k3s-deploy` 與 `antplay-slot-game-job` 最新 Step 5 / case 狀態到 `05 / 08 / 17`，並標明 `third_games_api`、`k3s-deploy` 維持 interview-only，`antplay-slot-game-job` 可保守寫 job / event processing、report projection / summary、big-win notification、activity supporting flow 與 partition / report path。
3. `game_api contribution claim consolidation`：已完成且 2026-05-20 已重新覆核；正式履歷只採 coupon 保守 claim，partner / agent bonus 只作面試素材，不因新規則重做。
4. `game_job contribution claim consolidation`：已完成且 2026-05-20 已重新覆核；保留為 project-level claim evidence，不因新規則重做。
5. `app_bi contribution claim consolidation`：已完成 rolling / scoped negative 收口；不放正式履歷主成果。
6. `bi_share contribution claim consolidation`：已完成 rolling / scoped negative 收口；不放正式履歷主成果。
7. `iwin-workspace contribution claim consolidation`：已完成 rolling / scoped 收口；只作 supporting evidence，不新增正式主成果。
8. `ugsoft-admin-api contribution claim consolidation`：已完成 rolling 收口；可保守補入履歷。但 Flow Track 尚未建立 Step 1 / Step 2 / `flows/`，不得宣稱 flow 完整。若 Nick 指定 admin-api flow，應先做 `ugsoft ugsoft-admin-api Step 1`；若補非 iwin 廣度，connector 本批三條代表 flow 已全部 Step 5，下一步可選 connector `contribution claim consolidation refresh`。
9. `ugsoft-connector-api contribution claim consolidation`：已完成 rolling 收口；可保守補入履歷。Flow Track Step 1 / Step 2 已完成，已選 `transfer-wallet-in-out-query`、`provider-callback-bet-settle-to-mq`、`request-bet-record-mq-sync` 為本批代表 flows；三條均已於 2026-05-27 完成 Step 5，不得宣稱全 project flow 完整或 final consolidation。若 Nick 要繼續 UGSoft 非 iwin 廣度，下一步做 `ugsoft ugsoft-connector-api contribution claim consolidation refresh`。
10. `ugsoft-workspace contribution claim consolidation`：已完成 rolling 收口；只作 supporting evidence，不作 Flow Track 主題，不新增正式履歷主成果。
11. `antplay-slot-admin-api contribution claim consolidation`：已完成 rolling 收口；可保守補入履歷。2026-05-26 re-audit 後仍是 AntPlay 目前唯一真正值得補的 Flow Track 缺口。若 Nick 要補 AntPlay 後台廣度，可選 Flow Track Step 1 / Step 2，挑 RabbitMQ request log / 風控通知、RTP / 暗池風控監控、Game API 白名單同步代表 flow。
12. `antplay-slot-game-api contribution claim consolidation`：已完成 refreshed 收口，且已回填 `slot-bet-settle-rollback Step 5`、`transfer-wallet-money-in-out Step 5`、`request-log-rabbitmq-async Step 5`、`bet-record-sharding-schema-route Step 5` 與 `runtime-rtp-darkpool-player-control Step 5`。本批代表 flows 已全部 Step 5；05 / 08 已由 `rolling resume package` 回填。
13. `antplay-slot-game-job contribution claim consolidation`：已完成 refreshed 收口；可保守補入履歷。Flow Track Step 1 / Step 2 已完成，五條代表 flows 均已 Step 5 並已由 2026-05-25 `rolling resume package` 回填通用履歷 / 自傳 / 談薪邊界。
14. `math-core / *-math contribution claim consolidation`：已完成 refreshed / grouped 收口；可保守補入履歷。`antplay *-math fixed-multi-bet-currency-math-core-compatibility Step 5` 已完成，`rtp-reel-strip-simulation-validation Step 5` 已完成，`buy-free-scatter-rtp3-result-contract Step 5` 已完成，`jackpot-symbol-hit-and-prize-scaling Step 5` 已完成，`special-wild-feature-state-transform Step 5` 已完成，且 contribution claim consolidation refresh 已完成。後續除非 Nick 指定 Level 3 final，不再平均掃 71 repo。
15. `math-workspace / platform-mock / buffer-id contribution claim consolidation`：已完成 rolling 收口；只作 supporting / learning，不作正式主成果。
16. `domain-level system map`：2026-05-25 確認 KB 已補規則。`projects/iwin/` 目前尚缺 domain-level `architecture-map.md` / `integration-map.md`；之後若 Nick 問 iwin 整體大地圖或代表 project 收斂後要總結，應先做 `iwin system map v1`，整理 game_api、game_job、payment、third_games_api、iwin_gameserver、app_bi、bi_share、k3s-deploy 的協作與 claim boundary。`antplay` / `ugsoft` 也要依同規則檢查，不得只口頭說應該有。
17. `0 到 1 system design template`：若 Nick 想補完整系統架構能力，應從已完成的 payment / wallet / bet-settle / MQ / batch flows 萃取 template，而不是重掃全部 repo。此項是可選加強，用於 Platform / Lead 候選面試，不是目前投遞前必做。

### 1. 收斂後狀態

目前狀態：

```text
通用投遞包可用；沒有預設下一步；可以自由提問或彈性指定
```

原因：

- `gsc-transfer-bet-settle-rollback Step 5` 已完成，結論維持 code-backed interview-only，不更新正式履歷 / 自傳。
- `oneapi-wallet-bet-result Step 5` 已完成，結論維持 code-backed interview-only，不更新正式履歷 / 自傳。
- `antplay-bet-settle-rollback Step 5` 已完成，claim gate 結論維持 interview-only。
- `gsc-seamless-withdraw-deposit-cancel Step 5` 已完成，claim gate 結論維持 interview-only。
- `k3s-deploy gameserver-phased-rollout Step 5` 已完成，claim gate 結論維持 interview-only。
- 2026-05-25 rolling resume package、104 投遞欄位檢查與 `04 / 面試 case 對齊檢查` 已完成，`08 / 17` 已先調整為通用 Senior Java Backend / Platform Backend 投遞版。
- 目前已不是「履歷能不能寫」或「case 能不能對上」，三條主力 case 的 90 秒 / 3 分鐘草稿也已完成；下一個缺口是 Nick 能不能實際講出來並抗追問。
- 轉職月份與面試題準備規則已收進 KB。後續若 Nick 問「要不要補 Java / SQL / LeetCode」，預設回答是：只保留最小檢查表與遇到再補，不取代 70% production case 主線。
- 但練習線先暫停。Nick 暫時沒有特定 JD 時，AI 不再大規模補資料，也不要求貼 JD；除非 Nick 明確說要開始練，AI 不主動進入互動式口說問答。若 Nick 問下一步，先回答收斂狀態：目前沒有預設下一步，可以自由提問或彈性指定；只有 Nick 要補架構視角時，才推薦 `iwin system map v1`。

### 2. iwin 各 project 局部下一步

目前 antplay 線已完成 `antplay-slot-admin-api`、`antplay-slot-game-api`、`antplay-slot-game-job`、`math-core`、`*-math`、`math-workspace`、`platform-mock`、`buffer-id` contribution consolidation。`antplay *-math fixed-multi-bet-currency-math-core-compatibility Step 5` 已完成，`rtp-reel-strip-simulation-validation Step 5` 已完成，`buy-free-scatter-rtp3-result-contract Step 5` 已完成，`jackpot-symbol-hit-and-prize-scaling Step 5` 已完成，`special-wild-feature-state-transform Step 5` 已完成，且 `*-math contribution claim consolidation` refresh 已完成；`slot-bet-settle-rollback Step 5`、`transfer-wallet-money-in-out Step 5`、`request-log-rabbitmq-async Step 5`、`bet-record-sharding-schema-route Step 5`、`runtime-rtp-darkpool-player-control Step 5`、`antplay-slot-game-api contribution claim consolidation` refresh、`antplay-slot-game-job contribution claim consolidation refresh`、2026-05-25 `rolling resume package`、`104 投遞欄位檢查`、`04 / 面試 case 對齊檢查` 與三條主力 case 口說稿已完成。通用投遞包已可用；沒有實際 JD 時，面試口說練習先暫停，等 Nick 明確要求。

以下是近期各 project 的局部下一步：

1. `game_api`：`contribution claim consolidation` 已完成；正式履歷只採 coupon 保守 claim，不因新規則重做。
2. `game_job`：`contribution claim consolidation` 已完成，不因新規則重做。
3. `payment`：Top 5 代表 flow 與 contribution consolidation 已收斂，2026-05-20 已重新覆核並補入 GoldenPay direct evidence，2026-05-26 已降回 provider / 商戶對接口徑；不因新規則重做。若 Nick 指定，可追加 provider-by-provider、MQ / reconciliation 邊界、game lobby 上下分等 flow，但這是可選加強。
4. `app_bi`：rolling / scoped negative contribution claim consolidation 已完成；不放正式履歷主成果。
5. `bi_share`：rolling / scoped negative contribution claim consolidation 已完成；不放正式履歷主成果。
6. `third_games_api`：rolling / scoped contribution consolidation 已完成；`gsc-transfer-bet-settle-rollback Step 5`、`oneapi-wallet-bet-result Step 5`、`antplay-bet-settle-rollback Step 5`、`gsc-seamless-withdraw-deposit-cancel Step 5` 已完成；本 project 本批代表 flow claim gate 已收斂。
7. `iwin_gameserver`：Career Track consolidation 已完成；`center-http-deposit-withdraw Step 5`、`game-spin-settlement-log-reel Step 5` 與 `bet-target-set-query Step 5` 已完成，Flow Track 已回到 `third_games_api`。
8. `k3s-deploy`：`gameserver-phased-rollout Step 5` 已完成；維持 interview-only，不更新正式履歷。
9. `iwin-workspace`：contribution consolidation 已完成；不作 flow 主題，不因新規則重做。
10. `ugsoft-admin-api`：contribution consolidation 已完成；可作後台 API / async data processing 履歷素材。
11. `ugsoft-connector-api`：contribution consolidation 已完成；可作 provider connector / transfer wallet / MQ 履歷素材。Step 1 / Step 2 已完成；本批三條代表 flow `transfer-wallet-in-out-query`、`provider-callback-bet-settle-to-mq`、`request-bet-record-mq-sync` 均已完成 Step 5，目前 connector 下一步可選 `contribution claim consolidation refresh`。
12. `ugsoft-workspace`：contribution consolidation 已完成；只作 workspace / docs / harness / runbook supporting evidence，不作正式履歷主成果。
13. `antplay-slot-admin-api`：contribution consolidation 已完成；可作 AntPlay 後台 API / 商戶控制面 / 風控監控 / RabbitMQ 與 Quartz 非同步資料處理素材。Step 1 / Step 2 是可選加強，不是預設下一步。
14. `antplay-slot-game-api`：refreshed contribution consolidation 已完成，Step 1 / Step 2 / `slot-bet-settle-rollback Step 5` / `transfer-wallet-money-in-out Step 5` / `request-log-rabbitmq-async Step 5` / `bet-record-sharding-schema-route Step 5` / `runtime-rtp-darkpool-player-control Step 5` 已完成。可作 AntPlay 遊戲 API runtime / 下注結算 / 轉帳錢包 / 分表 / RabbitMQ request log / high-traffic table governance / runtime decision 素材；05 / 08 已回填。
15. `antplay-slot-game-job`：contribution consolidation refresh 已完成，Flow Track Step 1 / Step 2 已完成，`proxy-user-data-report-projection Step 5` 已完成並回填 project-level report projection / Quartz summary evidence，`activity-accumulated-bet-voucher Step 5` 已完成且只作 reward correctness 面試素材 / supporting evidence，`big-win-notification Step 5` 已完成並回填 project-level supporting evidence，`settle-pool-monitor-darkpool-sync Step 5` 已完成且只作 analysis-first 面試素材，`db-partition-job-report-routing Step 5` 已完成且作分表 / report path supporting evidence；可作 AntPlay job / event processing、Kafka / Quartz、報表 projection / summary、activity accumulated bet supporting flow、big-win notification 與分表 / report path 維護素材。2026-05-25 rolling resume package 已回填。
16. `math-core`：contribution consolidation 已完成；可作 slot math core / contract / debug tooling 素材。Step 1 / Step 2 是可選加強，不是預設下一步。
17. `*-math`：contribution consolidation 已完成 refreshed / grouped，`fixed-multi-bet-currency-math-core-compatibility Step 5` 已完成，`rtp-reel-strip-simulation-validation Step 5` 已完成，`buy-free-scatter-rtp3-result-contract Step 5` 已完成，`jackpot-symbol-hit-and-prize-scaling Step 5` 已完成，`special-wild-feature-state-transform Step 5` 已完成；可作多個 slot math module / RTP / reel strip / debug / fixedMultiBet / buy free / jackpot / feature state transform 素材。已收斂，不平均掃 71 repo。
18. `math-workspace`：contribution consolidation 已完成；只作 cross-math KB / validation workflow supporting evidence。
19. `platform-mock`：contribution consolidation 已完成；只作 provider failure injection supporting evidence。
20. `buffer-id`：contribution consolidation 已完成；未見 Nick direct commits，只作 learning-only。
21. `rolling resume package`：已完成 2026-05-25 refresh 目前可用版；已吸收 `antplay-slot-game-job contribution claim consolidation refresh`，回填 `05 / 08 / 17`。

### 3. 每條完成後自動判斷是否更新

每條 flow 完成後，AI 要自動判斷是否需要更新：

- project README
- Step 文件
- flow evidence / claim-boundary
- `01-senior-owner-flow-inventory.md`
- `04-interview-casebook.md`
- `05-resume-master-zh.md`
- `08-application-autobiography-zh.md`
- `06-todo.md`

但不要把履歷寫太滿。只有完成 flow 並補 evidence 後，才升級履歷說法。

### 4. 跨 repo 選題參考

若 Nick 問「所有 repo 排序 / 下一個 repo」，以 `01-senior-owner-flow-inventory.md` 的「跨 repo 優先排序」為準。這份排序只用來選題，不是 code evidence；真正開工前仍要做該 repo 的 Step 1 / Step 2。目前若目標是最快補 Senior Backend 主力素材，`payment`、`game_job`、`game_api`、`iwin_gameserver`、`antplay-slot-game-api`、`antplay-slot-game-job`、`math-core`、`*-math` 的履歷 claim 已先保守收斂，其中 `payment` 已於 2026-05-20 重新覆核並補入 GoldenPay direct evidence，`iwin_gameserver` 已把 third-party provider 投派整合 direct evidence 正確歸位；`math-core` / `*-math` 是目前差異化最高的 slot math 素材且 contribution claim refresh 已完成，`slot-bet-settle-rollback Step 5`、`transfer-wallet-money-in-out Step 5`、`request-log-rabbitmq-async Step 5`、`bet-record-sharding-schema-route Step 5`、`runtime-rtp-darkpool-player-control Step 5`、`antplay-slot-game-api contribution claim consolidation` refresh、`antplay-slot-game-job contribution claim consolidation` refresh、2026-05-25 `rolling resume package`、`104 投遞欄位檢查`、`04 / 面試 case 對齊檢查` 與三條主力 case 口說稿也已完成。`center-http-deposit-withdraw Step 5`、`game-spin-settlement-log-reel Step 5`、`bet-target-set-query Step 5`、`third_games_api gsc-transfer-bet-settle-rollback Step 5`、`third_games_api oneapi-wallet-bet-result Step 5`、`third_games_api antplay-bet-settle-rollback Step 5`、`third_games_api gsc-seamless-withdraw-deposit-cancel Step 5` 與 `k3s-deploy gameserver-phased-rollout Step 5` 已完成；面試口說練習先暫停，有實際 JD 時才客製 `08 / 17`。

## 當前收斂狀態

```text
通用投遞包可用；沒有預設下一步；可以自由提問或彈性指定
```

AI 會依共用規則自動重讀 KB、既有 project 文件與相關 code repo 最新狀態，不需要 Nick 每次重貼完整規則。

## Senior 面試對標門檻

不要再用「最低能投」當標準。2026-05-25 後，履歷 / 自傳 / 談薪包已可先投遞，`04 / 面試 case 對齊檢查` 與三條主力 case 口說稿也已完成；接下來要把 Senior 面試準備度提高到 `中等可面 -> 穩過可抗追問 -> 完全對標 Senior / Platform`。但口說練習先暫停，等 Nick 明確要求再開始。已完成素材包含：

1. `payment-provider-callback` 或 `payment-order-provider-request`
2. `antplay-slot-game-api/slot-bet-settle-rollback` 或 `iwin_gameserver/third-party-transfer-in-out`
3. `antplay-slot-game-job/proxy-user-data-report-projection` 或 `game_job/daily-game-data-summary`

### 中等可面

- 3 條主力 case 能講 3 分鐘。
- 每條都有 evidence、claim boundary 與 5 個常見追問。
- 能說清入口、DB / Redis / MQ / provider、source of truth、failure window。
- 可以投 Senior / Platform Backend，但遇到強追問仍可能需要回來補洞。

### 穩過可抗追問

- 5 條 case 覆蓋 payment、wallet / bet-settle、MQ / projection、partition / high-traffic data、rollout / observability。
- 每條都有 90 秒、3 分鐘、STAR、failure scenarios、owner decision 與不可誇大邊界。
- 能回答「如果你重做會怎麼設計」、「先修哪裡」、「如何監控 / rollback / reconciliation」。
- 面試不靠背稿，能依 JD 重排案例順序。

### 完全對標 Senior / Platform

- 8-10 條 production flow 可切換使用，且 `05 / 08 / 04 / 17` 全部對齊。
- 每條 claim 都能分清：真實開發過、本人確認、code-backed、interview-only、不可誇大。
- 能把 project-level claim、單條 flow、薪資談判與自我介紹互相對上。
- 達到後停止大規模整理，改成投遞、面試練習、面試後補洞，不再平均掃 repo。

每條主力 case 都要有：

- `flow.md`
- `career-interview.md`
- `materials/evidence.md`
- `materials/decision-notes.md`
- `materials/interview.md`
- `materials/claim-boundary.md`

詳細檢查看：

```text
senior-owner-playbook/11-senior-interview-readiness.md
```

不同職缺定位怎麼準備，看：

```text
senior-owner-playbook/12-role-target-readiness-matrix.md
```

本地專案能力地圖與外部案例標注規則看：

```text
senior-owner-playbook/13-code-capability-map.md
projects/CONVENTIONS.md
```
