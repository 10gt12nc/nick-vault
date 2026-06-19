# JD Fit Analysis

## 職缺定位

這是偏 hands-on 的 Backend Team Lead，不是只管人的純主管。JD 明確寫「能寫程式，也能帶人」、「穩定性 > 花俏技術」、「速度 vs 品質取捨」與「熟悉 Claude Code 或 Codex AI 工具」，代表對方可能在找一個能把 AI-assisted development、Code Review、線上穩定性與團隊流程一起拉起來的人。

對 Nick 來說，這是 stretch role，但不是亂投。最準確定位是：

```text
具備 production flow ownership、AI-assisted engineering workflow 與技術帶人潛力的 hands-on Backend Lead candidate。
```

## 匹配點

| JD 要求 | Nick 可對應素材 | 說法 |
| --- | --- | --- |
| Java 5 年以上 | 2020/10 起 Java 後端，智湧 + 瀚鼎經驗 | 可對齊 5 年門檻，但正式年月依履歷如實呈現 |
| Spring Boot / Spring Cloud | Spring Boot、SSM / Spring MVC、MyBatis、服務間 API / provider integration | Spring Boot 可主打；Spring Cloud 若未補 evidence，寫微服務 / service boundary 可轉換 |
| MySQL / PostgreSQL | MySQL、分表、schema routing、transaction / index / query troubleshooting | MySQL 強；PostgreSQL 只寫 RDBMS 概念可轉換 |
| Redis / Kafka / RabbitMQ | Redis、Kafka、RabbitMQ、ActiveMQ、Quartz、request log / bet record / report projection | 強匹配，可準備 MQ duplicate、DLQ、projection source of truth |
| Docker / Kubernetes | Docker / K3s / rollout / observability analysis | 可作 interview 加分，不寫完整 DevOps / K8s owner |
| 遊戲 / 博弈系統 | iwin / AntPlay / UGSoft provider、wallet、bet-settle、slot job、slot math | 非常貼，應前移到履歷與自我推薦 |
| 線上問題與穩定性 | provider timeout、callback 重送、wallet partial success、MQ retry、batch replay、legacy takeover | 強匹配，主打 production flow risk |
| Code Review / 技術指導 | diff review、AI-assisted workflow、claim boundary、KB 回填、legacy flow 文件化 | 可說協助 review / 風險檢查 / 文件化，不說正式 reviewer owner |
| Claude Code / Codex AI | iwin-workspace / math-workspace / KB 驗證閉環 | 這是本 JD 特殊加分項，應前移 |
| 團隊管理 3-8 人、2 年帶人 | 目前沒有足夠 evidence 顯示正式管理職 | 最大風險；改投 hands-on lead candidate，面試誠實說明 |

## 最大風險

### 1. 正式管理經驗不足

JD 寫「2年以上團隊管理或技術帶領經驗」與「管理 5-8 人」。若 Nick 沒有正式排任務、績效、onboarding、衝突處理與跨部門排程責任，不能包裝成正式管理經驗。

建議口徑：

```text
我目前不是純管理職主管，但工作方式已經偏 hands-on tech lead：會拆 production flow、整理 code path / DB / Redis / MQ / log、協助交接與文件化，也會用 AI 工具輔助 code reading、diff review、測試檢查與 KB 回填。若進入正式組長角色，我會先從任務拆解、Code Review 標準、AI 使用邊界、事故追蹤與知識庫交接建立團隊節奏。
```

### 2. 架構 / DevOps 不可誇大

JD 寫 Scalability / Performance / Security、CI/CD、自動化測試、Docker / Kubernetes。Nick 可以講 K3s rollout / observability analysis、Docker / K3s 基本概念與 AI-assisted verification，但不得寫成完整 DevOps / SRE / K8s migration owner。

### 3. Lead title 的薪資與責任

這份薪資月薪 `80,000~140,000`，下限和一般 Senior 重疊，上限才合理反映 Team Lead。若對方真的要管理 5-8 人、扛線上穩定性、導 AI 工具與流程，Nick 不應低估自己。

## 推薦履歷策略

使用 `08` A 版為底，再針對這份 JD 做 30% 客製。

前移：

- AI-assisted engineering workflow / Codex / Claude Code 類工具。
- Code Review / diff review / production risk review。
- 遊戲 / 博弈 provider、wallet、bet-settle、MQ / batch。
- Legacy takeover / system reconstruction。
- 線上穩定性與 failure mode。
- 技術帶人潛力：任務拆解、文件化、交接、review checklist。

後移：

- Slot math 細節，只保留 domain 差異化。
- K3s / rollout / observability，只作加分。
- 內部 repo / provider 名稱，正式投遞改成泛稱。

## 面試主力 Case

1. `payment-order-provider-request` / `payment-provider-callback`：provider timeout、callback 重送、order lifecycle、idempotency。
2. `third-party-transfer-in-out` / `slot-bet-settle-rollback`：wallet / bet-settle、partial success、rollback / compensation。
3. `request-log-rabbitmq-async` / `proxy-user-data-report-projection`：MQ async audit、duplicate consume、projection source of truth。
4. `bet-record-sharding-schema-route` / `partition-table-creation`：分表 / schema routing / high-traffic data governance。
5. `iwin-workspace` / `math-workspace` supporting evidence：AI-assisted code reading、diff review、驗證閉環、KB 回填。
6. Legacy takeover：缺交接文件下如何重建 service / DB / Redis / MQ / log flow。

## 薪資策略

JD 標示月薪 `80,000~140,000`。這份是 Team Lead title，且需要遊戲 / 博弈 domain、AI 工具、線上穩定性與團隊帶領。

建議口徑：

```text
我會以月薪 120,000-140,000 作為主要期待，實際依管理範圍、on-call / incident 責任、獎金結構與整體 package 討論。
```

若 HR 壓低：

```text
如果角色需要實際管理 5-8 人、負責 Code Review、線上穩定性、AI 工具導入與跨部門推進，我會希望待遇能反映 Team Lead 的責任範圍。若職責比較接近 Senior IC 或準 Lead，薪資可以依實際授權與成長路徑討論。
```

保守底線：

```text
110,000 以上再看職責、工時、獎金、技術成長性與是否真的有 Lead 授權。
```

## 投遞判斷

建議投。這份 JD 的特殊價值在於它同時吃：

- 遊戲 / 博弈 domain。
- 高交易 / wallet / provider / MQ。
- AI coding tools。
- 穩定性與品質導向。
- hands-on lead。

這些都和 Nick 目前履歷包高度重疊。唯一要守住的是管理經驗邊界：投遞可用 Lead candidate 角度，面試不能說成已正式管理 5-8 人。
