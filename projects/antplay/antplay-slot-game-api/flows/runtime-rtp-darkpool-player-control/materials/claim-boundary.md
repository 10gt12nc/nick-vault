# runtime-rtp-darkpool-player-control Claim Boundary

## 0. 狀態

Step 4 完成。這份是面試素材後的 claim boundary，不是 Step 5 claim gate。

## 1. 目前可以說

- 這條 flow 已完成 Level 2 初版深掃與 Step 4 面試案例，確認 `GameFacade#bet` 會串 RTP cache、dark pool、player control、jackpot 與 math result。
- Nick / `10gt12nc` 在 `GameFacade` runtime decision 周邊有 direct commits，包含 dark pool / respin loop / bet runtime failure window 附近。
- 這條 flow 可作「game API runtime decision」正式面試素材。

## 2. 目前只能保守說

- 參與或分析 AntPlay slot game-api runtime 維護。
- 理解 RTP / dark pool / player control / jackpot / math contract 在 `/game/bet` 的整合方式。
- 可面試講 failure window、owner boundary 與重構思路。

## 3. 目前不能說

- 不能說主導完整 RTP 策略。
- 不能說主導完整遊戲數學模型。
- 不能說主導完整 dark pool / player control / jackpot platform。
- 不能說完整風控 owner。
- 不能說 player control 初版由 Nick 完成；目前 evidence 顯示初版主要是 Derek，Nick 有後續修正 / 周邊 evidence。
- 不能說 RTP cache 完整 owner；現行 blame 主要是 Arnold / Eliot，Nick 有早期 target RTP 修正 context。
- 不能說完整 compensation 已落地；部分 deadlock compensation 呼叫在 current develop 被註解。

## 4. Step 5 前待補 evidence

- `git show` 細讀 Nick / `10gt12nc` commits：
  - `a2b2af5`
  - `54078fe`
  - `2708045`
  - `68ca116`
  - `3922cc0`
  - `def5073`
  - `d2eff9f`
  - `f382d73`
  - `168f951`
  - `e0921e7`
- 追 `math-core` / `*-math` result contract 是否能補 code-backed 邊界。
- 追 player control MQ consumer。
- 追 jackpot / dark pool reconciliation。

## 5. Step 3 履歷草稿

目前只可作 Step 4 草稿，不直接寫入 `05 / 08`：

> 參與 AntPlay slot game API runtime 維護，理解並整理 RTP cache、dark pool、player control、jackpot 與 math result contract 在 `/game/bet` 主流程中的一致性與 failure window。

## 6. Step 4 後可面試講

- 以 `/game/bet` 為入口，講 RTP cache、dark pool、player control、jackpot、math result contract 如何共同決定一局結果是否接受。
- 強調 game-api / math module / 營運策略三者的 owner boundary。
- 主動揭露風險：cache stale、Redis counter loss、respin timeout、PlayerControl MQ failure、Jackpot side effect failure、audit payload 截斷。
- 保守說是 code-backed runtime decision case，不直接說完整 RTP / math owner。

## 7. 下一步

```text
antplay antplay-slot-game-api runtime-rtp-darkpool-player-control Step 5
```
