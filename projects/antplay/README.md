# antplay

用途: 整理 antplay 系列 repo 的 Senior Backend / Platform Backend 履歷與面試素材。

來源 repo:

```text
/Users/nick/Git/antplay
```

## Project Status

2026-05-28 `AntPlay system map v1` 已完成：新增 [architecture-map.md](architecture-map.md)、[integration-map.md](integration-map.md) 與 [career-interview.md](career-interview.md)，把 runtime、job、admin control plane、math contract 與 supporting repos 收成 domain-level 架構視角。這不新增履歷 claim，也不代表全 AntPlay 全量 code audit。

2026-06-22 `notion-export Step 1 + Step 2` 已完成：新增 [notion-export-source-notes.md](notion-export-source-notes.md)，把 `/Users/nick/Git/antplay/notion-export` 分成可整理、只可參考、不得寫入 vault 三類，並對照到 Game API contract、merchant onboarding、request log 排查、RTP / risk monitor、wallet / connector 切換等 supporting / learning evidence。這不新增正式履歷 claim，也不更新 `05 / 08`。

## 讀檔順序

1. [architecture-map.md](architecture-map.md)：先建立 AntPlay runtime / job / admin / math 的 domain-level 大圖。
2. [integration-map.md](integration-map.md)：再看下注結算、錢包、MQ、報表、風控與 math contract 的跨 repo 邊界。
3. [career-interview.md](career-interview.md)：最後把大圖轉成保守履歷 / 面試口徑。
4. [notion-export-source-notes.md](notion-export-source-notes.md)：需要使用 `/Users/nick/Git/antplay/notion-export` 時先看安全邊界；只可抽象成 supporting / learning evidence，不得搬敏感原文。
5. 各 project 的 `contribution-claim-consolidation.md` 與單條 flow：需要證據或追問時再回讀。

| Project | 類型 | 狀態 | 履歷判斷 | 下一步 |
| --- | --- | --- | --- | --- |
| `antplay-slot-admin-api` | Java / Spring Boot 後台 API、control plane、風控監控、報表、RabbitMQ / Quartz | contribution consolidation 已完成 / refreshed；`request-log-rabbitmq-admin-consumer Step 5` 已完成；`game-api-whitelist-sync Step 5` 已完成 / 2026-05-28 | 可保守放「後台 API / 商戶控制面 / 風控監控 / 非同步資料處理」；已回填 project-level claim | 已收斂 |
| `antplay-slot-game-api` | Java / Spring Boot 遊戲 API、slot runtime、轉帳錢包、下注結算、RabbitMQ / Quartz | contribution consolidation 已完成 / refreshed / 2026-05-21；五條代表 flow 均已 Step 5；05 / 08 已回填 | 可保守放「遊戲 API runtime / betting-settlement / transfer wallet / async log / high-traffic table governance / runtime decision」；不寫完整 slot platform / wallet / RTP owner | 已收斂 |
| `antplay-slot-game-job` | Java / Spring Boot job、Kafka consumer、Quartz、報表 projection、notification | contribution consolidation 已完成 / refreshed / 2026-05-25；五條代表 flow 均已 Step 5；05 / 08 已回填 | 可保守放「Kafka / Quartz job、代理玩家報表、活動累積投注 supporting flow、big-win notification、分表 / report path」；不寫完整 Kafka / settle pool owner | 已收斂 |
| `math-core` | Slot math core contract / debug / RTP / symbol library | contribution consolidation 已完成 / rolling | 可保守放「slot math core contract / debugBet / RTP / symbol 相容調整」；不寫完整 math framework owner | 已收斂；不單獨新增 Flow Track |
| `*-math` | 多個 slot game math module | contribution consolidation 已完成 / refreshed grouped / 2026-05-21；本批五條代表 flow 已全部 Step 5 並已回填 | 可保守放「slot math core / 多個 math module 維護與驗證」；強 evidence 在 `sph`、`spn`、`sfm`、`setl`、`sdt`、`slc` | 已收斂 |
| `math-workspace` | math KB / docs / sync workspace | contribution consolidation 已完成 / rolling | supporting evidence；可支撐 cross-math code reading / validation workflow，不作 standalone 主成果 | 已收斂 |
| `platform-mock` | Java / Spring Boot mock platform | contribution consolidation 已完成 / rolling | 局部真實開發過；只作 provider failure injection / rollback testing supporting evidence | 不優先 |
| `buffer-id` | Java ID buffer library | contribution consolidation 已完成 / rolling | 未見 Nick direct commits；只作 ID generator learning-only | 不優先 |
| `antplay-slot-admin` | 後台前端 | 未開始 | 通常只作入口 | 待 Nick 指定 |

## Claim Boundary

- `antplay-slot-admin-api` 有 Nick / `10gt12nc` 大量 direct commits，範圍包含 admin API 初版、JWT / refresh / 401、商戶登入與白名單、超級代理、商戶玩家 / 投注 / request log / 每日報表、RTP / 暗池 / 活動風控監控、Game API 白名單同步、玩家單點控制、RabbitMQ request log 與風控通知。
- 可以保守放履歷的是「後台控制面、商戶營運工具、風控監控與非同步資料處理開發維護」，不是「主導完整 AntPlay 遊戲 API / slot runtime / wallet / settlement」。
- `antplay-slot-game-api` 有 Nick / `10gt12nc` 大量 direct commits，範圍包含 game init、bet / settle / rollback、transfer wallet、bet record / request log 分表、RabbitMQ request log、Quartz 補通知、RTP / dark pool / player control 修正。可以保守放履歷的是「遊戲 API runtime、下注結算、轉帳錢包、分表與非同步 request log 維護」，不是「主導完整 AntPlay slot platform / wallet ledger / RTP 策略」。
- `antplay-slot-game-job` 有 Nick / `10gt12nc` direct commits，範圍包含 Kafka consumer / Quartz job、代理玩家報表聚合、activity accumulated bet、big-win notification、report currency / key 修正與 db partition / job config。可以保守放履歷的是「job / event processing、報表 projection、notification 維護」，不是「主導完整 Kafka event platform / settle pool / risk / jackpot owner」。
- `math-core` 與 `*-math` 有 Nick / `10gt12nc` direct commits。`math-core` 可保守放 slot math contract / debugBet / fixedMultiBet / currency / RTP / symbol 相容調整；`*-math` 可保守放多個 slot math module 維護與驗證，尤其 `sph-math`、`spn-math`、`sfm-math`、`setl-math`、`sdt-math`、`slc-math`。2026-05-21 已吸收五條代表 flow Step 5 evidence，可補強 RTP / reel strip、buy free / scatter、jackpot / symbol、特殊 feature result contract；不得寫成主導完整遊戲數學模型、全部 math module、完整 RTP 策略、完整 simulator / certification owner、完整 jackpot pool 或單一遊戲 feature owner。
- `math-workspace` 是 KB / docs / sync workspace，只作 cross-math code reading / GDD / RTP / reel strip / debug flow supporting evidence，不作 standalone 正式履歷主成果。
- `platform-mock` 只有局部 failure injection commits，可作 provider rollback / compensation 測試 supporting evidence，不作正式主成果。
- `buffer-id` 未掃到 Nick / `10gt12nc` direct commits，只作 ID generator learning-only。

## 2026-05-26 AntPlay Re-audit

- 已重新掃 `/Users/nick/Git/antplay` 下各 repo 的 git log、Nick / `10gt12nc` commits、主要 module 與既有 KB 狀態。
- `antplay-slot-admin-api` Flow Track 已完成本批收口；Step 1 / Step 2 已於 2026-05-28 完成，第一條代表 flow `request-log-rabbitmq-admin-consumer Step 5` 已完成，第二條代表 flow `game-api-whitelist-sync Step 5` 也已完成，且 project-level contribution refresh 已完成。
- 不新增 `antplay-push`、`platform-mock`、`math-core` 單獨 Flow Track；它們只能作 supporting evidence，沒有比既有 game-api / game-job / `*-math` 代表 flows 更值得。
- 履歷目前不需新增新 claim；`antplay-slot-admin-api` 的 project-level claim 已在 contribution consolidation refresh 中保守收斂，`request-log-rabbitmq-admin-consumer Step 5` 與 `game-api-whitelist-sync Step 5` 已作 supporting evidence 回填。若後續做 rolling resume package，再統一維護 05 / 08 的通用投遞稿。
