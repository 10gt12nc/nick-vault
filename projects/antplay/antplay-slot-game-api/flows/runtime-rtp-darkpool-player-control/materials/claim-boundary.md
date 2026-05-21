# runtime-rtp-darkpool-player-control Claim Boundary

## 0. 狀態

Step 5 完成。這份是單條 flow claim gate，仍不直接代表整個 project 履歷結論。

## 1. 目前可以說

- 這條 flow 已完成 Level 2 深掃、Step 4 面試案例與 Step 5 claim gate。
- Nick / `10gt12nc` 在 `GameFacade` runtime decision 周邊有 direct commits，包含 respin loop、2 分鐘 timeout / refund branch、dark pool `maxWin`、player control setData caller、deadlock / afterBet failure window。
- 本 flow 可作「game API runtime decision」正式面試素材，也可回填 project-level consolidation。

## 2. 目前只能保守說

- 參與 AntPlay slot game-api runtime decision flow 維護。
- 處理或參與分析 RTP / dark pool / player control / jackpot / math contract 在 `/game/bet` 的整合方式與 failure window。
- 可面試講 failure window、owner boundary 與重構思路。

## 3. 目前不能說

- 不能說主導完整 RTP 策略。
- 不能說主導完整遊戲數學模型。
- 不能說主導完整 dark pool / player control / jackpot platform。
- 不能說完整風控 owner。
- 不能說 player control 初版由 Nick 完成；目前 evidence 顯示初版主要是 Derek，Nick 有後續修正 / 周邊 evidence。
- 不能說 RTP cache 完整 owner；現行 blame 主要是 Arnold / Eliot，Nick 有早期 target RTP 修正 context。
- 不能說完整 compensation 已落地；部分 deadlock compensation 呼叫在 current develop 被註解。

## 4. Step 5 已補 evidence

- `a2b2af5`: current `GameFacade#bet` respin loop、timeout refund branch、math result call、jackpot amount、dark pool `maxWin` / `singleWinDp` / `singleBetDp`、player control result 周邊有 Nick direct evidence。
- `54078fe` / `31d7a46`: deadlock / afterBet failure window、transfer wallet compensation attempt 與 logging direct evidence；但 current develop 後續把實際 refund / fail-state 呼叫註解。
- `2708045` / `718a207` 周邊：player control table / schema path direct evidence。
- `e0921e7` / `168f951` / `f382d73` / `d2eff9f` / `def5073` / `3922cc0`: target RTP / dark pool setting 歷史修正 evidence；多數已是 context，不能升級成現行 RTP cache owner。
- current blame 已確認 `RtpCache`、player control 初版、jackpot service 主體多為他人。

## 5. Step 5 履歷候選

可回填 project-level consolidation，不直接寫入 `05 / 08`：

> 參與 AntPlay slot game API runtime decision flow 維護，處理 `/game/bet` 中 RTP / dark pool / player control / jackpot 與 math result contract 的整合邊界，協助釐清遊戲結果、池控與下注記錄間的一致性風險。

## 6. Step 4 後可面試講

- 以 `/game/bet` 為入口，講 RTP cache、dark pool、player control、jackpot、math result contract 如何共同決定一局結果是否接受。
- 強調 game-api / math module / 營運策略三者的 owner boundary。
- 主動揭露風險：cache stale、Redis counter loss、respin timeout、PlayerControl MQ failure、Jackpot side effect failure、audit payload 截斷。
- 保守說是 code-backed runtime decision case，不直接說完整 RTP / math owner。

## 7. 下一步

```text
rolling resume package
```
