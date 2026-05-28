# AntPlay domain career / interview map

更新日期：2026-05-28

本檔把 AntPlay domain-level 大地圖轉成履歷與面試口徑。正式履歷仍以 `senior-owner-playbook/05-resume-master-zh.md` 與 `08-application-autobiography-zh.md` 為準；本檔只提供 AntPlay 系統視角與 claim boundary。

## 可以怎麼定位 AntPlay 經驗

保守主軸：

```text
參與 AntPlay slot 系列後端與 math module 維護，範圍包含遊戲 API runtime、下注 / 派彩 / rollback、轉帳錢包、RequestLog / BetRecord 非同步處理、Kafka / Quartz job、報表 projection、後台商戶控制面 / 風控監控，以及 slot math core / 多個 math module 的 RTP、reel strip、buy free、jackpot / symbol 與 feature result contract 維護。能分清 runtime source of truth、async audit、report projection、control plane 與 math contract 的邊界。
```

這是 domain-level 面試定位。正式履歷要拆成 project-level claim，不要寫成完整 AntPlay owner。

## 可放履歷的 AntPlay 素材

| Project | 可放履歷口徑 | 證據層級 | 不可擴張 |
| --- | --- | --- | --- |
| `antplay-slot-game-api` | 參與遊戲 API runtime、下注結算、轉帳錢包、request log MQ、bet record 分表與 runtime decision 維護 | 真實開發過 + code-backed | 不寫完整 slot platform、wallet / ledger / RTP strategy owner |
| `antplay-slot-game-job` | 參與 Kafka / Quartz job、代理玩家報表、activity supporting flow、big-win notification、分表 / report path 維護 | 真實開發過 + code-backed | 不寫完整 Kafka event platform、settle pool / risk / jackpot owner |
| `antplay-slot-admin-api` | 參與後台 API / 商戶控制面 / 風控監控 / RabbitMQ 與 Quartz 非同步資料處理 | 真實開發過 + code-backed | 不寫完整 security platform、complete admin control plane owner |
| `math-core` / `*-math` | 參與 slot math core / 多個 math module 維護與驗證，包含 RTP / reel strip、debug / fixedMultiBet、buy free、jackpot / symbol、特殊 feature result contract | 真實開發過 + grouped code-backed | 不寫完整遊戲數學模型、全部 math repo、完整 simulator / certification owner |

## Supporting 素材

| Project | 面試價值 | 語氣 |
| --- | --- | --- |
| `math-workspace` | cross-math code reading / validation workflow / GDD / RTP / reel strip docs | supporting evidence |
| `platform-mock` | provider failure injection / rollback testing | supporting evidence |
| `buffer-id` | ID generator learning-only | learning-only |
| `antplay-push` | push encryption / message delivery 小型 supporting case | optional supporting，不主打 |

## 面試大圖回答

```text
AntPlay 我會分成四層看：第一層是 game-api runtime，處理 game init、bet / settle / rollback、transfer wallet、bet record、request log 和 runtime decision；第二層是 game-job，處理 Kafka / Quartz、報表 projection、activity supporting flow、big-win notification 和分表；第三層是 admin-api，提供商戶 / 後台控制面、白名單、request log / bet record consumer 和風控監控；第四層是 math-core / *-math，提供 slot math contract、RTP / reel strip、buy free、jackpot 和 feature result。我的重點不是說我 owner 全平台，而是能對核心 flow 分清 source of truth、async audit、projection、control plane 和 math contract。
```

## 追問防線

| 追問 | 回答方向 |
| --- | --- |
| 你是不是 AntPlay slot platform owner？ | 不是。我能講代表性 runtime / job / admin / math flows 的風險與邊界，不把它包裝成全平台 owner。 |
| 你是不是 wallet owner？ | 不是完整 wallet owner。可講 transfer wallet / bet-settle 的 code-backed consistency、deadlock / compensation 與 lookup 邊界。 |
| 你是不是 RTP / math owner？ | 不是完整 RTP 策略 owner。可講 math core / module contract、RTP / reel strip、feature result 的維護與驗證。 |
| MQ 是否做到 exactly-once？ | 不誇大。只講 producer / consumer / duplicate / audit / retry 邊界，沒有 evidence 不說 exactly-once 或 outbox owner。 |
| Admin API 是否代表 runtime？ | 不代表。Admin 是 control plane / query / consumer / config，同一問題要回到 game-api runtime 或 job projection 判斷 source of truth。 |

## 推薦讀法

1. 先讀 [architecture-map.md](architecture-map.md)，建立 runtime / job / admin / math 分層。
2. 再讀 [integration-map.md](integration-map.md)，理解下注、MQ、報表、風控、math contract 的跨 repo 邊界。
3. 想補履歷 claim，回到各 project 的 `contribution-claim-consolidation.md`。
4. 想練面試 case，回到單條 flow 的 `flow.md` 與 `career-interview.md`。
