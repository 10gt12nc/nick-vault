# Todo

## 已完成

- 已將外層舊整理資料歸入 `archive/` 參考區，之後可由 Nick 人工審查是否刪除。
- 從待刪區重新建立 `senior-owner-playbook/`。
- 建立新入口、維護規則、flow inventory、學習路線、flow 模板、面試案例與唯一履歷自傳。
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
- 已完成 `payment payment-provider-callback Step 5`，判定不更新正式履歷 / 自傳；本 flow 保留為金流 callback consistency / compensation 的保守面試素材。
- 已完成 `payment withdrawal-auto-review-refund Step 5`，判定不更新正式履歷 / 自傳；本 flow 保留為提款、自動審核 / 自動出款、失敗退款的一致性與補償面試素材。
- 已完成 `payment payment-order-provider-request Step 5`，完成 provider request claim gate；已確認 Nick 在 Pay4z / NaNapay / BFPAY / NimTestPay 等 provider request / query / callback 相關 code 有 path-specific commits，2026-05-20 consolidation 重新覆核後又補入 GoldenPay direct commits；正式履歷可用「參與」口徑，不寫主導完整金流。
- 已完成 `payment manual-order-review-repair Step 3`，建立人工審核 / 補單 / 訂單修復主學習包；目前只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment manual-order-review-repair Step 4`，轉成可面試 case；目前仍只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment manual-order-review-repair Step 5`，判定不更新正式履歷 / 自傳；人工審核 / 補單 / 修單只保留為面試分析素材。
- 已完成 `payment payment-channel-config-selection Step 3`，建立支付列表 / 商戶 / 玩家層級 / 提現設定選擇的 runtime config 主學習包；目前只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment payment-channel-config-selection Step 4`，轉成可面試 case；目前仍只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment payment-channel-config-selection Step 5`，判定不更新正式履歷 / 自傳；payment Top 5 代表 flow 已收斂，但不代表全 payment project 已完成。
- 已完成 `iwin payment contribution claim consolidation`；2026-05-20 已重新覆核並補入 GoldenPay direct evidence。Nick 本人確認加上 `10gt12nc` commits / branches / 重要 diff，可把 payment 升級為「部分真實開發過」，但這是履歷 claim 收斂，不是 payment 全量 flow 完成；仍不寫完整金流 owner。
- 已完成 `iwin_gameserver third-party-transfer-in-out Step 5`；原 Step 5 判定暫不更新正式履歷 / 自傳，2026-05-20 project-level consolidation 已用 Nick / `10gt12nc` direct commits 升級為可併入第三方 provider 投派整合保守 claim。
- 已完成 `iwin_gameserver center-http-deposit-withdraw Step 3`，建立 center_http 上分 / 下分主學習包；目前只作 code-backed 面試素材，不更新正式履歷 / 自傳。
- 已完成 `k3s-deploy gameserver-phased-rollout Step 4`，轉成 rollout / rollback / observability 的保守面試 case；目前仍不更新正式履歷 / 自傳。
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
- 已完成 `ugsoft ugsoft-workspace contribution claim consolidation`；2026-05-20 rolling 收口。這是 workspace / docs / harness / runbook supporting evidence，可支撐 cross-repo system reconstruction、工程規範、local / deploy harness、migration / release 風險整理；不新增 standalone 正式履歷主成果，也不反向包裝成 service runtime code。
- 已完成 `antplay antplay-slot-admin-api contribution claim consolidation`；2026-05-20 rolling 收口。Nick / `10gt12nc` 在 admin / merchant auth、商戶 / Game API 白名單、超級代理、玩家 / 投注 / request log / 報表查詢、RTP / 暗池 / 活動風控監控、RabbitMQ request log / 風控通知與 Quartz job 有大量 direct commits，可保守補入後台 API / risk ops / async data processing 經驗；不寫完整 AntPlay slot platform、game runtime、wallet / reconciliation 或完整 RabbitMQ owner。
- 已完成 `antplay antplay-slot-game-api contribution claim consolidation`；2026-05-20 rolling 收口。Nick / `10gt12nc` 在 game init、bet / settle / rollback、轉帳錢包、bet record / request log 分表、RabbitMQ request log、Quartz 補通知、RTP / dark pool / player control 有大量 direct commits，可保守補入 game runtime / betting-settlement / transfer wallet / async log 經驗；不寫完整 AntPlay slot platform、完整遊戲數學 / RTP 策略、wallet / ledger / reconciliation 或完整 RabbitMQ / Kafka owner。
- 已完成 `antplay antplay-slot-game-job contribution claim consolidation`；2026-05-20 rolling 收口。Nick / `10gt12nc` 在 Kafka consumer / Quartz job、代理玩家報表 projection、activity accumulated bet、big-win notification、report currency / key 修正與 db partition / job config 有 direct commits，可保守補入 job / event processing / report projection / notification 經驗；不寫完整 Kafka event platform、settle pool / risk / jackpot、BI / report platform 或完整 AntPlay slot platform owner。

## 下一步

### 0. 待辦事項優先規則

Nick 若先問「缺啥、待辦、優先順序、KB 要不要補」，AI 必須先維護本檔與相關 index，不直接開工 flow Step。

`contribution claim consolidation` 可以先做 rolling / scoped 版，用來支援近期履歷與面試材料；不必等 Step 2 定義的本批代表 flows 全部完成 Step 5。執行時除了掃 code，也要重讀該 project 已完成 flow KB 與目前可讀的 project 文件。未完成 flow 必須標為待深掃 / 待回填，不能宣稱 final 或全 project 已完整深掃；final consolidation 才等本批代表 flows 全部 Step 5 後再校正。

目前深掃 `nick-vault` 後，應放入待辦的缺口：

1. `iwin_gameserver center-http-deposit-withdraw Step 4`：Flow Track 的 project-local 下一步，把 center_http 上分 / 下分這條代表 flow 從 Step 3 往後收斂。
2. `third_games_api gsc-transfer-bet-settle-rollback Step 5`：這是 project-local flow 待辦，等 Nick 下 Step 再做；目前 contribution consolidation 已先完成，結論是不新增 standalone 正式履歷主成果。
3. `k3s-deploy gameserver-phased-rollout Step 5`：這是 flow 待辦，等 Nick 下 Step 再做；下次開工前要重新 fetch source repo refs，並記錄 `k3s-deploy` 本機 `main` 落後 `origin/main` 的狀態。
4. `game_api contribution claim consolidation`：已完成且 2026-05-20 已重新覆核；正式履歷只採 coupon 保守 claim，partner / agent bonus 只作面試素材，不因新規則重做。
5. `game_job contribution claim consolidation`：已完成且 2026-05-20 已重新覆核；保留為 project-level claim evidence，不因新規則重做。
6. `app_bi contribution claim consolidation`：已完成 rolling / scoped negative 收口；不放正式履歷主成果。
7. `bi_share contribution claim consolidation`：已完成 rolling / scoped negative 收口；不放正式履歷主成果。
8. `iwin-workspace contribution claim consolidation`：已完成 rolling / scoped 收口；只作 supporting evidence，不新增正式主成果。
9. `ugsoft-admin-api contribution claim consolidation`：已完成 rolling 收口；可保守補入履歷。若要強化 ugsoft provider integration，下一步先掃 `ugsoft-connector-api`。
10. `ugsoft-connector-api contribution claim consolidation`：已完成 rolling 收口；可保守補入履歷。下一步若繼續 ugsoft，應做 Flow Track Step 1 / Step 2，挑 provider transfer / callback / MQ 代表 flow。
11. `ugsoft-workspace contribution claim consolidation`：已完成 rolling 收口；只作 supporting evidence，不作 Flow Track 主題，不新增正式履歷主成果。
12. `antplay-slot-admin-api contribution claim consolidation`：已完成 rolling 收口；可保守補入履歷。下一步若繼續 antplay，應做 Flow Track Step 1 / Step 2，挑 RabbitMQ request log / 風控通知、RTP / 暗池風控監控、Game API 白名單同步代表 flow。
13. `antplay-slot-game-api contribution claim consolidation`：已完成 rolling 收口；可保守補入履歷。下一步若繼續 antplay，應做 Flow Track Step 1 / Step 2，挑 slot bet-settle-rollback、transfer-wallet-money-in-out、request-log-rabbitmq-async、bet-record-sharding-schema-route 代表 flow。
14. `antplay-slot-game-job contribution claim consolidation`：已完成 rolling 收口；可保守補入履歷。下一步若延續本 repo，應做 Flow Track Step 1 / Step 2，挑 proxy-user-data-report-projection、big-win-notification、activity-accumulated-bet-voucher、settle-pool-monitor 代表 flow。

### 1. iwin iwin_gameserver center-http-deposit-withdraw Step 4

建議下一步：

```text
iwin iwin_gameserver center-http-deposit-withdraw Step 4
```

原因：

- `iwin_gameserver contribution claim consolidation` 已完成。
- 第三方 provider 投派整合已可保守放履歷，下一步不用重做 consolidation。
- `center-http-deposit-withdraw` 已有 Step 3，應回 Flow Track 轉成 Step 4 面試 case。

### 2. iwin 各 project 局部下一步

目前 antplay 線已完成 `antplay-slot-admin-api`、`antplay-slot-game-api` 與 `antplay-slot-game-job` rolling contribution consolidation。若 Nick 要最大化交易主線，下一步優先做 `antplay-slot-game-api Step 1`；若要延續本輪 game-job，則做 `antplay-slot-game-job Step 1`，把 proxy-user-data-report-projection、big-win-notification、activity-accumulated-bet-voucher、settle-pool-monitor 拆成可面試的 Flow Track。若回 iwin 線，則回 `iwin iwin_gameserver center-http-deposit-withdraw Step 4`。

以下是近期各 project 的局部下一步：

1. `game_api`：`contribution claim consolidation` 已完成；正式履歷只採 coupon 保守 claim，不因新規則重做。
2. `game_job`：`contribution claim consolidation` 已完成，不因新規則重做。
3. `payment`：Top 5 代表 flow 與 contribution consolidation 已收斂，2026-05-20 已重新覆核並補入 GoldenPay direct evidence，不因新規則重做；若 Nick 指定可追加 provider-by-provider、transfer wallet、MQ / reconciliation、game lobby 上下分等 flow。
4. `app_bi`：rolling / scoped negative contribution claim consolidation 已完成；不放正式履歷主成果。
5. `bi_share`：rolling / scoped negative contribution claim consolidation 已完成；不放正式履歷主成果。
6. `third_games_api`：rolling / scoped contribution consolidation 已完成；project-local flow 下一步仍可做 `gsc-transfer-bet-settle-rollback Step 5`，但目前不搶履歷。
7. `iwin_gameserver`：Career Track consolidation 已完成；Flow Track 下一步是 `center-http-deposit-withdraw Step 4`。
8. `k3s-deploy`：project-local 下一步是 `gameserver-phased-rollout Step 5`。
9. `iwin-workspace`：contribution consolidation 已完成；不作 flow 主題，不因新規則重做。
10. `ugsoft-admin-api`：contribution consolidation 已完成；可作後台 API / async data processing 履歷素材。
11. `ugsoft-connector-api`：contribution consolidation 已完成；可作 provider connector / transfer wallet / MQ 履歷素材。下一步建議 Step 1 / Step 2。
12. `ugsoft-workspace`：contribution consolidation 已完成；只作 workspace / docs / harness / runbook supporting evidence，不作正式履歷主成果。
13. `antplay-slot-admin-api`：contribution consolidation 已完成；可作 AntPlay 後台 API / 商戶控制面 / 風控監控 / RabbitMQ 與 Quartz 非同步資料處理素材。下一步建議 Step 1 / Step 2。
14. `antplay-slot-game-api`：contribution consolidation 已完成；可作 AntPlay 遊戲 API runtime / 下注結算 / 轉帳錢包 / 分表 / RabbitMQ request log 素材。下一步建議 Step 1 / Step 2。
15. `antplay-slot-game-job`：contribution consolidation 已完成；可作 AntPlay job / event processing、Kafka / Quartz、報表 projection、activity accumulated bet 與 big-win notification 素材。下一步建議 Step 1 / Step 2。

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

若 Nick 問「所有 repo 排序 / 下一個 repo」，以 `01-senior-owner-flow-inventory.md` 的「跨 repo 優先排序」為準。這份排序只用來選題，不是 code evidence；真正開工前仍要做該 repo 的 Step 1 / Step 2。目前若目標是最快補 Senior Backend 主力素材，`payment`、`game_job`、`game_api`、`iwin_gameserver`、`antplay-slot-game-api`、`antplay-slot-game-job` 的履歷 claim 已先保守收斂，其中 `payment` 已於 2026-05-20 重新覆核並補入 GoldenPay direct evidence，`iwin_gameserver` 已把 third-party provider 投派整合 direct evidence 正確歸位；`third_games_api` rolling consolidation 也已完成但不新增 standalone 正式履歷主成果。下一步可回 Flow Track 補 `center-http-deposit-withdraw Step 4`，或延續 antplay 做 `antplay-slot-game-job Step 1`。

## 下一個 prompt

```text
antplay antplay-slot-game-job Step 1
```

AI 會依共用規則自動重讀 KB、既有 project 文件與相關 code repo 最新狀態，不需要 Nick 每次重貼完整規則。

## Senior 面試最低門檻

要開始比較有把握投 10 萬以上 Senior / Platform Backend 職缺，至少先完成：

1. `payment-provider-callback`
2. `transfer-wallet-transfer-in-out`
3. `settled-bets-kafka`

每條都要有：

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
