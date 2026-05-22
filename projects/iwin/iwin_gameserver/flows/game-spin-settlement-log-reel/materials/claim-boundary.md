# Claim Boundary：game-spin-settlement-log-reel

## 證據層級

目前結論：`專案存在 / code-backed`

本 flow 已完成 Step 3 code reading，可以作面試分析素材。尚不能作 Nick 真實開發過的正式履歷 claim。

## 可說

- 我追過 iwin_gameserver 一條典型 slot spin / settle / log_reel flow。
- 我能說明 `Game40SpinJob -> Game40SpinUtil -> GamePlayer.addMoney -> GameToCenterSpinResultJob -> LogReelJob`。
- 我能說明 game runtime、center wallet、log server 的一致性邊界。
- 我能分析 `addMoneyQueue`、saveDataVersion、async log、center timeout、log missing 的 failure window。

## 可作面試素材，但要保守

- `slots-game40-sgj` 作為代表 game，不代表已掃全部 game module。
- `log_reel` 可連到戰績 / 報表 / 客服排查，不代表 Nick 寫過 app_bi 查詢端。
- Nick / `10gt12nc` 在 gameserver 的強 evidence 目前仍以第三方 provider integration / log reel 優化 / money job / log projection 為主。

## 不可說

- Nick 主導一般 slot spin / settle 主流程。
- Nick 實作全部 27 個 game module。
- Nick 是 iwin_gameserver runtime owner。
- Nick 設計完整 wallet / log_reel consistency architecture。
- Nick 解過這條 flow 的 production incident。
- 本 flow 可以直接更新 `05` / `08`。

## Step 5 前需要補的 evidence

若未來要升級 claim，至少需要：

- Nick 本人確認具體負責哪個 game / 哪個 bug / 哪個 ticket。
- MR / ticket / production issue。
- path-specific commit 直接命中 `GameXXSpinUtil`、`GamePlayer.addMoney`、`GameToCenterSpinResultJob` 或一般 `LogReelJob` 主流程。
- 重要 diff 能證明 Nick 修改了 spin / settle / log consistency，而不是只做第三方 provider 對接。

## 目前履歷處理

不更新正式履歷。

仍沿用 project-level claim：

```text
參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接。
```

## 下一步

```text
iwin iwin_gameserver game-spin-settlement-log-reel Step 4
```
