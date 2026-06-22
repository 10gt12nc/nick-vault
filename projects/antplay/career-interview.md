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

## Notion export supporting cases

來源：[notion-export-source-notes.md](notion-export-source-notes.md)。以下只作面試口說補強，不新增正式履歷 claim；不得搬入帳密、IP、正式環境 URL、key、token、商戶實例或可直接操作環境的指令。

### Case 1：Game API 對接模式怎麼講

30 秒版：

```text
AntPlay 的 Game API 對接我會先分清楚錢包與投派模式：單一錢包、轉帳錢包，以及投派整合 / 投派分離。這會影響 balance、bet、settle、rollback 或 bet-settle callback 怎麼走，也會影響問題排查時要先看 merchant callback、game-api runtime，還是 transfer transaction / bet record。
```

追問時怎麼接：

- 先說 `單一錢包` 偏向遊戲端即時向商戶查餘額與投派 callback；`轉帳錢包` 偏向先轉入 / 轉出，再由平台內部錢包承接遊戲流程。
- `投派分離` 要分開看 bet、settle、rollback；`投派整合` 則看 bet-settle callback 的 request / response 與錯誤處理。
- 排查時不只看 API status code，也要看簽章、timestamp、金額格式、JSON header、callback response contract 與 runtime source of truth。

不要這樣講：

- 不說自己主導完整 Game API 對接規格。
- 不把 API 文件理解包裝成完整 provider integration owner。
- 不把 Notion export 當成 direct commit / ticket evidence。

### Case 2：request log 排查怎麼講

30 秒版：

```text
遇到 merchant integration 或 callback 異常時，我不會只看畫面錯誤。我會先用 request log 定位 target、step、request / response、error_message 和時間窗口，再判斷問題是在 game-api 呼叫商戶、商戶回傳格式、下游 provider timeout，還是後台 / report 顯示延遲。
```

追問時怎麼接：

- 若看到 deserialization / response format 類錯誤，先比對 response body 是否符合 callback contract，不急著判定交易狀態錯。
- 若是 timeout，先切出 timeout window，再追商戶 API 到第三方 API 的往返時間，避免只怪單一 service。
- request log / async audit 是排查線索，不等於交易帳本；真正狀態仍要回到 runtime state、wallet state、bet record 或 transaction record。

不要這樣講：

- 不說完整 observability platform owner。
- 不說 request log 就是 source of truth。
- 不把單次營運排查流程誇大成已建立完整監控平台。

### Case 3：RTP / risk monitor 怎麼講

30 秒版：

```text
AntPlay 的風控素材可以用來說明我理解 slot platform 不只看單筆下注成功，也會看一段時間內 merchant 或 player 的 RTP 是否異常。這類 monitor 通常有時間窗口、最低投注量或筆數門檻，重點是幫營運與技術判斷是否需要進一步檢查遊戲結果、商戶狀態或 runtime 設定。
```

追問時怎麼接：

- Merchant RTP exceed 偏向看商戶整體是否在某個時間窗口內 RTP 異常，可能牽涉商戶虧損、放水狀態或遊戲設定。
- Player RTP exceed 偏向看單一玩家投注量與 RTP 是否異常，適合當作疑似遊戲異常或高風險玩家的觀測入口。
- 風控 monitor 是觀測與告警入口，不代表它直接決定派彩或交易狀態；runtime source of truth 與 math result contract 要分開看。

不要這樣講：

- 不說完整 RTP 策略 owner。
- 不說完整風控平台 owner。
- 不把風控條件理解包裝成主導遊戲數學模型。

### Case 4：wallet / connector 切換怎麼講

30 秒版：

```text
像 AntPlay 切到 UGSoft 單一錢包這類 connector / wallet mode 切換，我會把它當成營運風險流程看：切換前先進維護、記錄原 callback 配置，切換時確認 info、balance、bet-settle 等 callback 指向，切換後再解除維護並觀察 request log / runtime 行為，確保失敗時能回復。
```

追問時怎麼接：

- 重點不是「改 URL」而已，而是要知道哪些 callback 是玩家資訊、餘額、投派整合，哪些設定失誤會造成登入、餘額或下注派彩異常。
- 切換前要保留原配置，因為 provider / connector 切換失敗時需要快速 rollback。
- 切換後要用 request log、callback response、merchant / player 行為與後台查詢一起確認，不只看設定頁是否儲存成功。

不要這樣講：

- 不說主導完整 AntPlay / UGSoft wallet migration。
- 不說完整 wallet / ledger / reconciliation owner。
- 不把操作流程包裝成 DevOps / SRE rollout owner。

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
