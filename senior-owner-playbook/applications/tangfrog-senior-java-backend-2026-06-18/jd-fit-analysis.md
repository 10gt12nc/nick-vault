# JD Fit Analysis

## 職缺定位

這份是資深 Java 後端 IC / 遊戲後端核心工程師，不需要管理責任。JD 關鍵字集中在遊戲後端架構設計、核心功能開發、系統維運、效能監控、技術問題排除、技術文件與 AI 工具。

對 Nick 來說，這份比 Team Lead 缺更容易切入，但薪資上限也比較低。最準確定位是：

```text
具備遊戲 / 博弈 production flow、provider integration、MQ / Redis / MySQL 與 AI-assisted workflow 的 Senior Java Backend candidate。
```

## 匹配點

| JD 要求 | Nick 可對應素材 | 說法 |
| --- | --- | --- |
| 遊戲後端架構設計 | third-party provider、wallet / bet-settle、slot runtime、provider connector | 可講參與 / 分析架構與 flow，不寫完整 architect |
| 核心功能開發 | payment provider、遊戲 provider、wallet、bet / settle / rollback、slot job | 強匹配 |
| 系統維運與效能監控 | batch / report projection、Mongo backup、K3s / observability analysis、log tracing | 可主打 troubleshooting；監控平台不誇大 |
| 技術問題排除與決策 | callback 重送、provider timeout、MQ duplicate、Redis / DB consistency、schema routing | 強匹配 |
| 技術文件與開發標準 | KB、flow 文件、claim boundary、AI-assisted workflow | 非常貼 |
| Claude Code / Codex AI | iwin-workspace / math-workspace / KB 驗證閉環 | 這是特殊加分項 |
| git / maven / spring / mybatis | Java 後端日常技術棧 | 可直接列專長 |
| RabbitMQ / Redis / MySQL / Linux | request log / bet record MQ、Redis cache / runtime projection、MySQL schema / index、Linux 基本維運 | 強匹配 |
| Netty / Elasticsearch | 目前沒有明確主力 evidence | 只寫可補強，不主打 |

## 風險與降調

- 薪資區間 `60,000-100,000` 偏低。若對方上限硬卡 100,000，需看年終、獎金、工時、技術深度與升遷空間。
- JD 寫「架構設計」，但管理責任是「不需負擔管理責任」。投遞時要用 Senior IC 口徑，不要套 Team Lead 包。
- 系統維運 / 效能監控可以用 log / DB / Redis / MQ / batch / K3s analysis 講，但不得寫完整 SRE / DevOps owner。
- Netty / Elasticsearch 不要為了迎合加分條件而誇大。

## 推薦履歷策略

使用 `08` A 版為底，做 20% 客製。

前移：

- 遊戲後端 / provider gateway / wallet / bet-settle。
- RabbitMQ / Redis / MySQL / MyBatis。
- 系統維運、效能排查、log tracing、batch / projection。
- 技術文件、開發標準、legacy flow reconstruction。
- Codex / Claude Code 類 AI-assisted engineering workflow。

後移：

- Team Lead / 管理說法。
- 薪資過高的 lead claim。
- Slot math 深細節，只作遊戲 domain 差異化。
- K3s / rollout 只作維運理解加分。

## 面試主力 Case

1. `third-party-transfer-in-out` / `slot-bet-settle-rollback`：遊戲 provider、wallet、bet-settle、rollback。
2. `payment-provider-callback` / `payment-order-provider-request`：provider request / callback、timeout unknown、idempotency。
3. `request-log-rabbitmq-async` / `request-bet-record-mq-sync`：RabbitMQ async audit、duplicate consume、DLQ / retry。
4. `proxy-user-data-report-projection` / `daily-game-data-summary`：batch / report projection、source of truth、replay。
5. `bet-record-sharding-schema-route`：MySQL / schema routing / high-traffic data。
6. `iwin-workspace` / `math-workspace` supporting evidence：Codex / Claude Code 類 AI-assisted code reading、文件同步與驗證閉環。

## 薪資策略

JD 標示月薪 `60,000-100,000`。對 Nick 目前定位來說，這份不適合低於上限太多。

建議口徑：

```text
我會以月薪 100,000-110,000 作為期待，實際依年終、獎金、工時、職責深度與成長空間討論。
```

如果 HR 說上限 100,000：

```text
我理解此職缺區間上限是 100,000。我的期待會希望接近上限，主要是因為我過去經驗不只是一般 Java API，而是長期接觸遊戲平台、第三方 provider、payment provider、wallet / bet-settle、RabbitMQ / Redis / MySQL、既有系統維護與 AI-assisted engineering workflow。這些和職缺要求的遊戲後端架構、穩定性、維運排查與技術文件都直接相關。
```

## 投遞判斷

建議投，但定位為 market check / 保底機會，不優先於 Team Lead 缺。

這份的優點是風險小、JD 關鍵字很貼 Nick；缺點是薪資上限偏保守。若對方聯絡積極，可以用來測試市場對 Nick 的遊戲後端 / AI-assisted workflow / 系統維運定位是否買單。
