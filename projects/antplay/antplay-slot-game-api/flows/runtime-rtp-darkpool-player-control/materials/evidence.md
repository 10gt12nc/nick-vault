# runtime-rtp-darkpool-player-control Evidence

## 0. 掃描狀態

| 項目 | 內容 |
| --- | --- |
| Step | Step 4 |
| 掃描深度 | Level 2 Flow 深掃 |
| source repo | `/Users/nick/Git/antplay/antplay-slot-game-api` |
| branch | `develop` |
| local HEAD | `079aa66` |
| local `origin/develop` | `079aa66` |
| ahead / behind | `0 / 0` |
| source working tree | clean |
| remote fetch | 失敗一次，依 KB 不反覆重試 |
| 最新性 | 未確認最新遠端；本輪依本地 refs / 本地 working tree 保守分析 |

注意：不記錄內網 remote URL / IP / sensitive detail。

## 1. Vault 重讀

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `projects/antplay/antplay-slot-game-api/README.md`
- `projects/antplay/antplay-slot-game-api/step1-candidate-flows.md`
- `projects/antplay/antplay-slot-game-api/step2-flow-comparison.md`
- `projects/antplay/antplay-slot-game-api/contribution-claim-consolidation.md`

既有 Step / flow 狀態：

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `README.md` | 可沿用 / 需同步 | 已指向本 flow Step 3，完成後需更新狀態 |
| `step1-candidate-flows.md` | 可沿用 | 已把本 flow 排為 Rank 5 |
| `step2-flow-comparison.md` | 可沿用 / 需同步 | 已推薦本 flow Step 3，完成後需改下一步 |
| `contribution-claim-consolidation.md` | 可沿用 | 已有 RTP / dark pool / player control rolling claim，但仍標不可誇大 |

## 2. Source Code 路徑

| 類別 | Path / Method | 判斷 |
| --- | --- | --- |
| bet orchestration | `GameFacade#bet` | 主路徑 |
| math contract | `SlotMathFacade#normalBet` / `freeSpinBet` | game-api 對 math layer 的呼叫邊界 |
| RTP cache | `RtpCache#exec` / `getRtpFromCache` | 從 `agent_game_rtp` 載入並 fallback default |
| dark pool counters | `DarkpoolService` | Redis total bet / total win |
| player control | `PlayerControlService` / `PlayerControlCacheService` | config 查詢、respin 判斷、diff amount、MQ record |
| jackpot | `JackpotService#getJackpotRtp` / `isForceRespin` / `setData` | jackpot RTP、force respin、balance / record |
| agent RTP fallback | `AgentGameFacade#getRTP` | math bet call 的 RTP source |
| entity / repository | `AgentGameRtp` / `AgentGameRtpRepository` | RTP config schema |

## 3. 重要 Code Evidence

### RTP selection

- `GameFacade#bet` 讀 normal / free / activity normal / activity free / jackpot RTP。
- `useVoucher` 且 voucher type `_ns_` 用 activity normal RTP；其他 voucher 用 activity free RTP。
- `boughtFreeSpin` 用 free RTP；一般 bet 用 normal RTP。
- `JackpotService#getJackpotRtp` 只對 jackpot game 回傳 jackpot RTP，非 jackpot game 回 0。

### Math contract

- `GameFacade#getBetResult` 封裝 `SlotMathFacade#normalBet` / `freeSpinBet`。
- Jackpot fixed multi bet game 會走帶 `fixedMultiBet` 的 overload。
- `SlotMathFacade` 透過 `SlotMathOperatorServiceManager#find(gameCode)` 找 math service。

### Dark pool decision

- `DarkpoolService` 以 Redis key 記錄 agent / game / currency 的 total bet / total win。
- `GameFacade#bet` 讀取 total bet / total win 後，在 respin loop 裡用本局結果計算：
  - `singleBet`
  - `singleWin`
  - `singleBetDp`
  - `singleWinDp`
  - `maxWin = (agentTotalBet + singleBet) * targetRTP / 100 - agentTotalWin`
- 若 `singleWinDp` 大於 `maxWin`，且 force respin 啟用，會重新呼叫 math layer。

### Jackpot decision

- `JackpotService#isForceRespin` 依 jackpot amount、jackpot RTP、jackpot total bet / win 與各 jackpot type balance 判斷是否重轉。
- `JackpotService#setData` 對 jackpot reward 做查重、扣 jackpot balance、寫 jackpot record、更新 jackpot dark pool win。
- `JackpotService#jackpotDpControl` 顯示 player control 或 voucher 會影響 jackpot dark pool control 是否生效。

### Player control decision

- `PlayerControlService#initPlayerControl` 先判斷 agent 是否允許啟用，才讀 control config。
- `PlayerControlCacheService#getEnabledPlayerControl` 查 `ag_player_control` 有效 config。
- `PlayerControlService#processPlayerControl` 依 kill / gift compare type、Redis diff amount、本局 win / bet 判斷是否 respin。
- `PlayerControlService#setData` 會更新 Redis diff amount、publish RabbitMQ record、完成時更新 control status。

## 4. Git History Evidence

Nick / `10gt12nc` path-specific evidence:

| Commit | Author | 日期 | 摘要 | 本 flow 意義 |
| --- | --- | --- | --- | --- |
| `a2b2af5` | `10gt12nc` | 2025-12-30 | 轉帳錢包 deadlock 補償 | 大幅改 `GameFacade#bet`，目前 blame 顯示 respin loop / dark pool maxWin / jackpot amount / bet result 附近多段由此 commit 引入 |
| `54078fe` | `10gt12nc` | 2025-12-30 | 轉帳錢包 deadlock 補償 | 下注後 `afterBet` 與 deadlock failure window 相關 |
| `31d7a46` | `10gt12nc` | 2025-12-31 | deadlock 補償 | 補強 transfer wallet failure logging |
| `2708045` | `10gt12nc` | 2025-12-12 | fix ag_player_control | player control / schema path direct evidence |
| `68ca116` | `10gt12nc` | 2025-11-27 | todo : ag_player_control | player control path direct evidence |
| `3922cc0` | `10gt12nc` | 2025-05-19 | Revert "不拿 darkpool 設定" | dark pool 設定讀取取捨 context |
| `def5073` | `10gt12nc` | 2025-05-19 | 不拿 darkpool 設定 | dark pool 設定讀取取捨 context |
| `d2eff9f` | `10gt12nc` | 2025-05-15 | fix get targetRTP null | target RTP fallback context |
| `f382d73` | `10gt12nc` | 2025-05-15 | fix get targetRTP 直接用db的 沒有設再用預設 | target RTP source / default context |
| `168f951` | `10gt12nc` | 2025-05-15 | fix get targetRTP | target RTP 修正 context |
| `e0921e7` | `10gt12nc` | 2025-04-28 | getTargetRTP 98 | early target RTP context |

重要他人 context:

| Commit / blame | Author | 意義 |
| --- | --- | --- |
| `237fde9` / `aaee1c4` | arnold | activity RTP / fallback 補強 |
| `88d7f2a` / `7206373` | arnold | jackpot RTP / jackpot service 整合 |
| `121a436` / player control series | Derek | player control 初版主要由 Derek 貢獻 |
| `1ec63e0` / `d9de587` / `29e537a` | arnold | additional_info、jackpot / player control 條件後續調整 |

## 5. Blame 摘要

- `GameFacade` RTP selection 現行主要由 Arnold / Eliot 後續整理，Nick 早期 targetRTP 修正出現在已註解 dark pool config block 與歷史 commits。
- `GameFacade` respin loop、dark pool `maxWin`、`singleBetDp` / `singleWinDp`、部分 jackpot amount 與 player control 呼叫周邊，多數 blame 到 `a2b2af5` / `10gt12nc`。
- `PlayerControlService#processPlayerControl` 初版主要 Derek，2026-01 後續 compare type / return value adjustment 主要 Arnold。
- `RtpCache#getRtpFromCache` 現行主要 Arnold / Eliot，不宜把 RTP cache 完整 owner 寫成 Nick。

## 6. 已確認 / 推測 / 待確認

已確認:

- 本 flow 在 `/game/bet` 主流程內，且會影響是否接受 math result。
- RTP cache、dark pool、player control、jackpot 都會在一局 bet 中參與 runtime decision。
- Nick / `10gt12nc` 有 `GameFacade` runtime decision 周邊 direct commits，尤其 respin loop / dark pool / bet failure window 附近。

合理推論:

- 本 flow 可作 `antplay-slot-game-api` project-level runtime decision / game-result-control 素材的補強。
- 若 Step 5 補到更完整 direct evidence，可回填 contribution consolidation。

待確認:

- 最新遠端是否有更新。
- math-core / `*-math` 對 fixed multi bet、jackpot reward list、RTP input 的 contract 細節。
- Player control MQ consumer。
- Jackpot / dark pool reconciliation job。
- 實際 production config 中哪些 agent 啟用 dark pool / player control。

## 7. Step 4 補充

本輪 Step 4 未新增 source code 實質深掃；沿用 Step 3 的 Level 2 code-backed evidence，將內容整理成正式面試素材：

- 30 秒 / 90 秒 / 3 分鐘講法。
- STAR 案例。
- Senior 追問：RTP cache miss、Redis counter loss、respin timeout、PlayerControl MQ failure、Jackpot side effect failure。
- Lead / Architect 追問：runtime evaluator、game-api / math module 邊界、policy service 拆分、可觀測與可回放。

## 8. 本輪未做

- 未改 source repo。
- 未更新 `05-resume-master-zh.md` / `08-application-autobiography-zh.md`。
- 未把本 flow 升級成完整履歷 claim。
- 未掃 production DB、live Redis 或內網服務。
- 未做 Step 5 重要 diff / current blame claim gate。

## 9. 下一步

```text
antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 5
```
