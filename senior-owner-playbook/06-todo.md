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
- 已修正 Step 主線規則：AI 不可自行把「下游定位 / 補 evidence / 補 decision-notes / 架構圖」升級成新下一步；Step 3 乾淨後預設進 Step 4。
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
- 已完成 `game_api coupon-redeem-credit-grant Step 5`，確認 `10gt12nc` 有 game_api / iwin_gameserver coupon path-specific commits；可保守更新正式履歷 / 自傳，不寫主導完整 coupon / reward owner。
- 已完成 `game_job daily-game-data-summary Step 5`，確認 `10gt12nc` 有 daily summary / 時區 / 留存 / 備份相關 path-specific commits；可保守更新正式履歷 / 自傳，不寫完整 BI pipeline owner。
- 已完成 `game_job third-party-record-mongo-backup Step 5`，確認 `10gt12nc` 有 GSC backup 分批查詢與 batch size 調整 direct commits；可保守更新正式履歷 / 自傳為局部 GSC Mongo backup 分批處理經驗，不寫完整第三方紀錄備份 owner。
- 已完成 `game_job coin-flow-batch-projection Step 4`，建立金幣流水 / 玩家行為 projection 主學習包並轉成正式面試 case；目前只作 code-backed 面試分析素材，不更新正式履歷 / 自傳。
- 已完成 `payment payment-provider-callback Step 5`，判定不更新正式履歷 / 自傳；本 flow 保留為金流 callback consistency / compensation 的保守面試素材。
- 已完成 `payment withdrawal-auto-review-refund Step 5`，判定不更新正式履歷 / 自傳；本 flow 保留為提款、自動審核 / 自動出款、失敗退款的一致性與補償面試素材。
- 已完成 `payment payment-order-provider-request Step 5`，完成 provider request claim gate；已確認 Nick 在 Pay4z / NaNapay / BFPAY / NimTestPay 等 provider request / query / callback 相關 code 有 path-specific commits，正式履歷可用「參與」口徑，不寫主導完整金流。
- 已完成 `payment manual-order-review-repair Step 3`，建立人工審核 / 補單 / 訂單修復主學習包；目前只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment manual-order-review-repair Step 4`，轉成可面試 case；目前仍只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment manual-order-review-repair Step 5`，判定不更新正式履歷 / 自傳；人工審核 / 補單 / 修單只保留為面試分析素材。
- 已完成 `payment payment-channel-config-selection Step 3`，建立支付列表 / 商戶 / 玩家層級 / 提現設定選擇的 runtime config 主學習包；目前只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment payment-channel-config-selection Step 4`，轉成可面試 case；目前仍只作面試素材，不更新正式履歷 / 自傳。
- 已完成 `payment payment-channel-config-selection Step 5`，判定不更新正式履歷 / 自傳；payment Top 5 flow 已收斂。
- 已完成 `iwin payment contribution claim consolidation`；Nick 本人確認加上 `10gt12nc` commits / branches / 重要 diff，可把 payment 升級為「部分真實開發過」，但仍不寫完整金流 owner。
- 已完成 `iwin_gameserver third-party-transfer-in-out Step 5`，判定暫不更新正式履歷 / 自傳；下一條回到同 project ranking。
- 已完成 `k3s-deploy gameserver-phased-rollout Step 4`，轉成 rollout / rollback / observability 的保守面試 case；目前仍不更新正式履歷 / 自傳。

## 下一步

### 1. iwin game_job coin-flow-batch-projection Step 5

建議下一步：

```text
iwin game_job coin-flow-batch-projection Step 5
```

原因：

- payment project-level contribution consolidation 已完成，履歷 / 自傳已保守同步。
- `game_api coupon-redeem-credit-grant` 已完成 Step 5，履歷 / 自傳已保守同步。
- `game_job daily-game-data-summary` 已完成 Step 5，履歷 / 自傳已保守同步。
- `coin-flow-batch-projection` Step 4 已完成，已轉成正式面試 case。
- 同 flow 下一步最值得做 Step 5 claim gate，確認是否更新履歷；目前預期未補 direct evidence 前不更新。

### 2. iwin 各 project 局部下一步

目前總優先是 `iwin game_job coin-flow-batch-projection Step 5`。以下是近期各 project 的局部下一步：

1. `game_job`：`coin-flow-batch-projection Step 5`。
2. `iwin_gameserver`：判斷是否做 `center-http-deposit-withdraw Step 3`。
3. `third_games_api`：`gsc-transfer-bet-settle-rollback Step 5`。
4. `k3s-deploy`：`gameserver-phased-rollout Step 5`。
5. `game_api`：`coupon-redeem-credit-grant Step 5` 已收斂；下一條需回 Step 2 ranking 選。
6. `payment`：Top 5 flow 與 contribution consolidation 已收斂；之後除非 Nick 指定新 payment flow，先不重做。
7. `app_bi`：主要 flow 已收斂；不回 app_bi 搶履歷 claim。

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

若 Nick 問「所有 repo 排序 / 下一個 repo」，以 `01-senior-owner-flow-inventory.md` 的「跨 repo 優先排序」為準。這份排序只用來選題，不是 code evidence；真正開工前仍要做該 repo 的 Step 1 / Step 2。目前若目標是最快補 Senior Backend 主力素材，payment、game_api coupon、game_job daily summary 與 game_job third-party Mongo backup 都已收斂，`game_job coin-flow-batch-projection Step 4` 已完成，下一步回到 `game_job coin-flow-batch-projection Step 5`。

## 下一個 prompt

```text
iwin game_job coin-flow-batch-projection Step 5
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
