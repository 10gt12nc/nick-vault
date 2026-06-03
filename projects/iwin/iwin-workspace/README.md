# iwin iwin-workspace

本資料夾整理 `/Users/nick/Git/iwin/iwin-workspace` 的 project-level 履歷 / 面試邊界。

`iwin-workspace` 是 iwin 多 repo 工作區與 KB / docs / 運維索引 repo，不是單一 production service。它底下包含 `Projects/`、`.work/` 本機 repo 快取、`docs/` 專案分析、跨專案關聯、dev-k3s / 舊 dev-linux 運維資料、schema dump 與工具腳本。

## 讀檔順序

1. [contribution-claim-consolidation.md](contribution-claim-consolidation.md)：rolling / scoped contribution claim 收口。

## 目前狀態

| 文件 | 狀態 | 說明 |
| --- | --- | --- |
| `README.md` | 已建立 | project 入口與履歷邊界 |
| `contribution-claim-consolidation.md` | 已完成 / 2026-06-03 已重查 payment workflow | `iwin-workspace` 不新增 standalone 正式履歷主成果；可作知識庫治理、跨 repo flow reconstruction、環境 / 運維索引與 payment 開發 / 驗證閉環的 supporting evidence |

## Project 定位

已確認：

- workspace repo 本身不是業務 service，不是 payment / gameserver / game_api 的 source of truth。
- repo 內含多個 service 的本機快取與文件索引，但真正的 production code evidence 必須回到各 source repo 與對應 project consolidation。
- Nick / `10gt12nc` 在此 repo 有大量 KB / docs / environment / verification commits，可支撐「建立與維護跨 repo knowledge base、整理 payment / gameserver / k3s / third-party integration evidence」的工作方法。
- 2026-06-03 已重查 payment KB catalog，確認它可支撐 payment provider 類修改的開發 / 驗證 SOP：讀 KB / 規格 / 相似商戶、feature branch、本地 / SIT / simulator 驗證、payment + third-party / simulator + app_bi / center / DB / log / balance 對照與 KB 回填。

不可誤用：

- 不把 `iwin-workspace` 寫成 Nick 主導某個業務系統。
- 不把 workspace docs 裡的 service 分析，反向包裝成 Nick 開發所有 service。
- 不把 JumpServer / dev 環境 / SQL dump / `.env` 類資料搬進 `nick-vault`。

## 履歷邊界

可放履歷：

- 不建議新增 standalone 主成果 bullet。
- 可在自我推薦或面試中作 supporting evidence：接手文件不足、跨 repo code reading、建立 KB / 索引、梳理 payment / gameserver / k3s / provider integration flow。

可面試講：

- 可以說明如何用 workspace 建立跨 repo 地圖、把 source repo、schema、運維文件、KB catalog 串成可查證的 system understanding。
- 可以說明 GoldenPay / NimTestPay / dev-k3s / gameserver / payment 類記錄如何支撐真正的 project-level consolidation，但正式 claim 要回到 `payment`、`iwin_gameserver`、`game_api`、`game_job` 等 project。
- 可以說明 `iwin-workspace` 的 payment KB 如何把 provider integration 從 code reading、需求拆解、diff review、local / SIT / simulator verification、DB / log / app_bi / center check 到 KB 回填串成閉環；但要說這是 supporting workflow，不是 production service 本體。

不可誇大：

- 不寫主導完整 iwin 平台。
- 不寫完整 DevOps / SRE owner。
- 不寫完整 k3s owner、JumpServer owner、workspace owner 導致 production 改善。
- 不寫任何環境機器、credential、internal URL、secret。

## 收斂狀態

原本的下一步已完成：

```text
- 全域下一步狀態：目前沒有預設 project flow 下一步；請以 senior-owner-playbook/01-senior-owner-flow-inventory.md 與 senior-owner-playbook/06-todo.md 為準。
```

原因：

- `iwin-workspace` contribution consolidation 已完成，結論是不新增 standalone 正式履歷主成果。
- 目前真正更有履歷 / 面試價值的是回到 gameserver money flow，把 `center-http-deposit-withdraw` 從 Step 3 轉成 Step 4。
