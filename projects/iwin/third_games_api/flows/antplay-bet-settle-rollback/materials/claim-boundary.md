# Claim Boundary - Antplay bet / settle / rollback

## 結論

Step 5 結論：本 flow 只作 `專案存在 / code-backed` 與 `分析素材 / learning-only`。

不更新正式履歷 / 自傳。

## 可放履歷

目前不新增 `third_games_api` standalone 履歷 bullet。

原因：

- Step 5 已重新確認 `third_games_api` Antplay path 的 Nick / `10gt12nc` direct evidence 仍只有 `63e88f2`、`ec9d812` 兩筆測試 / diagnostic commit。
- 這兩筆不足以支撐 production Antplay adapter 開發、主導 provider 串接或 incident owner。
- 下游 `iwin_gameserver` Antplay direct commits 是另一個 project 的 evidence，不能反向歸到 `third_games_api`。

## 可面試講

可以作為 code-backed 面試案例：

- 第三方遊戲 provider 三段式 bet / settle / rollback state transition。
- Adapter 驗簽、Redis routing、gameserver wallet boundary。
- Mongo audit / transaction evidence 與 retry idempotency 風險。
- gameserver 成功但 adapter Mongo 寫失敗的 failure window。
- 舊三段式與新整合式 endpoint 的系統演進對照。

## 不可誇大

- 不說 Nick 主導 Antplay provider 串接。
- 不說 Nick 完整設計 `third_games_api` Antplay adapter。
- 不說 Nick 是完整第三方遊戲錢包 / 對帳 / reconciliation owner。
- 不把 `iwin_gameserver` Antplay direct commits 反向包裝成 `third_games_api` direct contribution。
- 不宣稱已確認 production 仍使用舊三段式 endpoint。

## Evidence 分層

`third_games_api`：

- Antplay path 有 code-backed production flow。
- Nick / `10gt12nc` 目前只掃到測試 / diagnostic 類 commit。
- 結論：不支撐正式履歷主成果。

`iwin_gameserver`：

- Antplay gameserver path 有 Nick / `10gt12nc` direct commits。
- 結論：可支撐 `iwin_gameserver` project-level third-party provider 投派整合 claim；不歸到本 repo。

## 後續升級條件

若要把本 flow 從 interview-only 升級，需要至少補到其中之一：

- Nick 本人確認其在 `third_games_api` Antplay adapter 的實際 production 開發範圍。
- 對應 MR / ticket / branch / commit 顯示 Nick 修改 Antplay bet / settle / rollback production path。
- production issue / incident evidence 顯示 Nick 處理過 Antplay adapter retry、金額、rollback、Mongo evidence 或 routing 問題。
- 之後重新追 path-specific history 時找到更強 direct evidence。

## 後續狀態

目前沒有預設下一步。
