# antplay

用途: 整理 antplay 系列 repo 的 Senior Backend / Platform Backend 履歷與面試素材。

來源 repo:

```text
/Users/nick/Git/antplay
```

## Project Status

| Project | 類型 | 狀態 | 履歷判斷 | 下一步 |
| --- | --- | --- | --- | --- |
| `antplay-slot-admin-api` | Java / Spring Boot 後台 API、control plane、風控監控、報表、RabbitMQ / Quartz | contribution consolidation 已完成 / rolling | 可保守放「後台 API / 商戶控制面 / 風控監控 / 非同步資料處理」；不寫完整 slot platform owner | `antplay antplay-slot-admin-api Step 1` |
| `antplay-slot-game-api` | 遊戲 API / runtime 候選 | 未開始 | 高價值候選，需另掃 | 待 Nick 指定 |
| `antplay-slot-game-job` | 遊戲批次 / 報表 / projection 候選 | 未開始 | 高價值候選，需另掃 | 待 Nick 指定 |
| `antplay-slot-admin` | 後台前端 | 未開始 | 通常只作入口 | 待 Nick 指定 |
| `math-workspace` / `math-core` / `*-math` | 遊戲數學 / workspace | 未開始 | 需另行分流 | 待 Nick 指定 |

## Claim Boundary

- `antplay-slot-admin-api` 有 Nick / `10gt12nc` 大量 direct commits，範圍包含 admin API 初版、JWT / refresh / 401、商戶登入與白名單、超級代理、商戶玩家 / 投注 / request log / 每日報表、RTP / 暗池 / 活動風控監控、Game API 白名單同步、玩家單點控制、RabbitMQ request log 與風控通知。
- 可以保守放履歷的是「後台控制面、商戶營運工具、風控監控與非同步資料處理開發維護」，不是「主導完整 AntPlay 遊戲 API / slot runtime / wallet / settlement」。
- `antplay-slot-game-api` / `antplay-slot-game-job` 才更接近遊戲 runtime 與交易主線，需另做 Flow Track / Career Track。
