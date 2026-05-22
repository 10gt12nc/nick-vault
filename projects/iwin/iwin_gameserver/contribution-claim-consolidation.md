# iwin_gameserver Contribution Claim Consolidation

更新時間：2026-05-20
掃描等級：Level 3-oriented rolling / scoped consolidation
證據層級：部分真實開發過 + code-backed；未完成全 project final consolidation

## 結論

`iwin_gameserver` 不能再只標成「分析素材」。本輪重掃 Nick / `10gt12nc` commits、branches、path-specific history 與代表性 diff 後，已確認 Nick / `10gt12nc` 在第三方遊戲 provider 投派整合、gameserver wallet mutation hook 與 log projection 相關路徑有直接開發 evidence。

可放履歷的保守範圍：

- 參與 iwin gameserver 第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接。
- 範圍可包含 Antplay / GSC / PG 類 provider 的 center_http command、transfer-in-out job、`GamePlayer` log dispatch、`PlayerData` 餘額異動 hook、bet / settle / refund / bet_result 與 log reel projection。
- 語氣用「參與 / 開發 / 維護」，不寫成「主導完整 gameserver / 完整 wallet owner / 全部 provider owner」。

仍不能放大的範圍：

- 不能宣稱 Nick 主導完整 `iwin_gameserver`。
- 不能宣稱 Nick 是完整遊戲錢包 owner、dbproxy owner、所有遊戲結算 owner。
- 不能宣稱已完成 exactly-once、完整 idempotency、outbox、reconciliation 架構。
- 不能宣稱改善錯帳率、效能 X%、修復 production duplicate callback incident。
- `center-http-deposit-withdraw` 已完成 Step 5，結論仍是 code-backed 面試素材；不能由第三方投派 commits 或局部 `onDeposit()` 周邊觸碰自動擴張成完整上分 / 下分 owner。

## Source repo 狀態

### iwin_gameserver

- 路徑：`/Users/nick/Git/iwin/iwin_gameserver`
- 已執行：`git fetch --all --prune`
- local branch：`main`
- local HEAD：`30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- remote HEAD：`origin/main` = `30a9fcb95bfda33b582deeb4e149eb06bed4afe3`
- ahead / behind：`0 / 0`
- working tree：乾淨
- remote refs：已看到 `origin/main`、`origin/k3s`、`origin/Nick-GSC_PG`、`origin/AntPlay-transferInOut`、`origin/Nick-review`、`origin/beta`、`origin/feature/RD-162`、`origin/feature/RD-188`、`origin/nick-LocalDevTest`、`origin/rate-script`

## 本輪掃描範圍

已掃：

- `git log --all --author='10gt12nc|Nick|nick'`。
- path-specific history：`HttpService.java`、`slots-center` money jobs、`slots/sql/job` wrappers、`GamePlayer.java`、`slots-game-log` log reel jobs、`GameEnum.java`。
- 代表性 diff / stat：Antplay、GSC、PG、coupon bet target 相關 commits。
- 既有 KB：`README.md`、`step1-candidate-flows.md`、`step2-flow-comparison.md`、`third-party-transfer-in-out` flow / evidence / claim boundary / career material、`center-http-deposit-withdraw` evidence / claim boundary、project-level `career-interview.md`。

未掃 / 待確認：

- 未 checkout 每個遠端 branch 做逐 branch 完整差異收斂。
- 未掃 production ticket、MR 討論、線上 incident、provider 官方 spec。
- 未掃全部 game modules、dbproxy 落庫細節、DB unique index。
- `center-http-deposit-withdraw` Step 5 已完成但維持 interview-only；完整 project final consolidation 仍需等後續代表 flows 校正。

## 直接 evidence

| 範圍 | 代表 commits | 代表 files | 判斷 |
| --- | --- | --- | --- |
| Antplay 投派整合 | `4843791`、`bb6bb9e`、多個 `feat(#144)` commits | `HttpService.java`、`AntplayTransferInOutJob.java`、`HttpAntplayTransferInOut.java`、`AddCenterCoinAP.java`、`GamePlayer.java` | 真實開發過 + code-backed |
| GSC 投注 / 派彩 / log | `116e8ec`、`deee1b8`、`053e2be` | `GSCTransferInOutJob.java`、`HttpGSCTransferInOut.java`、`LogReelGSCBetJob.java`、`LogReelGSCSettleJob.java`、`AddCenterCoinGSC.java`、`GamePlayer.java` | 真實開發過 + code-backed |
| PG 投派 / refund / bet_result | `b58eb58`、`a5fbcb3`、`a889980`、`571b6fa`、`73b2524` | `PGTransferInOutJob.java`、`HttpPGTransferInOut.java`、`LogReelPGBetJob.java`、`LogReelPGSettleJob.java`、`LogReelPGRefundJob.java`、`AddCenterCoinPG.java` | 真實開發過 + code-backed |
| Log reel / bet log projection | `da7dc4b`、`053e2be` 與 `#144` 系列 log / query commits | `GamePlayer.java`、`ClientActionManager.java`、`LogReel*Job.java`、`GameEnum.java`、`LogJobCrons.java` | 真實開發過 + code-backed |
| Coupon / bet target supporting evidence | `6c99dd3`、`30a9fcb` | `HttpService.java`、`SpinBetTargetConst.java` | 支援 game_api coupon flow；不單獨擴張成完整 gameserver claim |
| center_http 上分 / 下分 | `10gt12nc` 有局部觸碰 `onDeposit()` 周邊與 provider wallet hook，但主路徑多數仍是初始 commit author | `HttpService.onDeposit/onWithdraw`、`HttpNewBill`、`NewBillJob`、`PlayerData.modifyAndGetCoinFromBill()` | code-backed / 面試素材；仍非完整真實開發 claim |

## 可放履歷

建議放入履歷 master 的保守句：

```text
參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接，處理 Antplay / GSC / PG 類 bet / settle / refund / transfer-in-out flow、center_http command、玩家餘額異動 hook、log reel / bet log projection 與 bet_result / refund 邊界；聚焦 provider transaction、玩家餘額與 round log 一致性。
```

更短版本：

```text
參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接，處理 Antplay / GSC / PG 類 bet / settle / refund / transfer-in-out flow 與 log projection。
```

## 可面試講

- 可以把 `third-party-transfer-in-out` 從「只分析過」升級成「有直接開發 evidence 的面試案例」，但仍要避免講成完整 owner。
- 可以談 provider callback / adapter command 進 gameserver 後，如何由 `HttpService` 分派、wrapper 查玩家、money job 丟入 game pool、`PlayerData` 修改餘額、`GamePlayer` 送 log。
- 可以談 wallet mutation、HTTP response、log projection 不在同一 ACID transaction，因此要關注 idempotency、failure window、reconciliation 與 observability。
- `center-http-deposit-withdraw` 可以用來補強 money flow 思考；Step 5 後仍只能說 code-backed 分析過，不說 Nick 實作完整上分 / 下分。

## 不可誇大

- 不寫「主導 iwin_gameserver」。
- 不寫「負責完整遊戲錢包 / 完整第三方遊戲 provider integration」。
- 不寫「設計完整防重 / 對帳 / outbox」。
- 不寫「解決重複扣款或錯帳 production incident」。
- 不寫「負責所有 Antplay / GSC / PG provider adapter」，因為 `third_games_api` 本 repo 的 direct evidence 較弱，本輪較強 evidence 是 downstream gameserver。

## 後續回填規則

這份是 rolling / scoped consolidation。`center-http-deposit-withdraw` Step 5 已回填為 interview-only；後續若新增其他 `iwin_gameserver` flow，必須回頭校正本檔、project README、`05-resume-master-zh.md` 與 `08-application-autobiography-zh.md`。

## 下一步建議

只推薦一件事：

```text
iwin iwin_gameserver game-spin-settlement-log-reel Step 4
```

原因：

- Career Track 的 `iwin_gameserver` rolling / scoped consolidation 已先收口，正式履歷可以保守補第三方 provider 投派整合。
- Flow Track 仍未完成本批代表 flows；`center-http-deposit-withdraw` 已完成 Step 5 且維持 interview-only。
- `game-spin-settlement-log-reel` Step 3 已完成，下一步回同 flow Step 4，避免完成 Step 3 後自行跳到其他 project。
