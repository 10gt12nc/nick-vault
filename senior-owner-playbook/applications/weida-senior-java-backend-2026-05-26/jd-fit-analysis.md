# JD Fit Analysis

## 職缺定位

這不是一般 CRUD 後端。JD 想找的是能承擔線上服務穩定性、參與架構設計、做 code review、協助團隊成長與改善工程流程的 Senior Java Backend。

對 Nick 來說，這是 stretch role，但不是亂投。比較準確的定位是：

```text
具備 production flow ownership 潛力的 Senior Java Backend / Platform Backend 候選人。
```

## 匹配點

| JD 要求 | Nick 可對應素材 | 說法 |
| --- | --- | --- |
| Java / Spring 生態 | Java、Spring Boot、SSM / Spring MVC、MyBatis | 可主打 Java backend 與 Spring Boot API 維護 |
| 線上服務穩定性 | payment provider、wallet / bet-settle、MQ / batch、legacy takeover | 主打 production troubleshooting 與 failure window |
| Code Review / 程式碼品質 | diff review、AI-assisted workflow、claim boundary、KB 回填 | 說成工程流程與 review discipline，不誇大成正式 reviewer owner |
| 架構設計 / 技術選型 | system design templates、flow owner decision、K3s interview-only | 說參與設計討論、能提出取捨，不說主導完整架構 |
| MQ | Kafka、RabbitMQ、ActiveMQ、Quartz | 強匹配，準備 request log / bet record / report projection case |
| NoSQL | Redis、MongoDB | 強匹配，準備 Redis cache / runtime projection、Mongo backup / audit case |
| RDBMS | MySQL | 強匹配；PostgreSQL 需標成可快速轉換 |
| 微服務 / 分散式 | provider gateway、跨 service payment / wallet / MQ flow | 用跨系統狀態一致性與 provider integration 轉譯 |
| 技術導師 / 工程文化 | legacy system reconstruction、KB、AI-assisted workflow | 說協助整理可維護脈絡與分享，不說正式管理職 |

## 風險與降調

- JD 寫 `5 年以上 Java` 與條件 `7 年以上`；Nick 可用高交易 production flow 深度補年資落差，但不要假裝年資滿 7 年。
- Spring Cloud 沒有明確主力 evidence；可說熟悉微服務與服務間整合，Spring Cloud 可補。
- PostgreSQL 沒有明確主力 evidence；可說主要 MySQL，RDBMS index / transaction / schema 設計概念可轉換。
- DDD / Clean Architecture 是加分；可講分層、邊界、domain flow、source of truth，不要說完整落地 DDD。
- 技術導師角色只能保守說「協助 code review、整理 flow、文件化、分享與帶新人理解系統」，不說正式 Tech Lead。

## 推薦履歷策略

使用 A 版為底，做 20% 客製。

前移：

- 線上服務穩定性。
- production troubleshooting。
- MQ / Kafka / RabbitMQ。
- Redis / MongoDB / MySQL。
- 微服務 / provider integration。
- code review / 工程品質。
- legacy system takeover。

後移：

- Slot math。
- 遊戲 / 博弈 domain 細節。
- K3s / DevOps。
- AI-assisted workflow 只放工程方法，不當主成果。

## 面試主力 Case

1. Payment provider request / callback：第三方整合、callback 重送、timeout unknown、order lifecycle。
2. Wallet / bet-settle / rollback：transaction boundary、state transition、partial success。
3. Kafka / RabbitMQ / report projection：consumer retry、duplicate、DLQ / DLT、source of truth。
4. Legacy takeover / system reconstruction：缺交接文件下如何重建 service / DB / Redis / MQ / log flow。
5. Code review / engineering culture：如何 review AI 或同事 diff、如何避免 production risk。

## 薪資策略

JD 標示年薪 `1,200,000+`，符合 Nick `10 萬以上`目標，但這個標示只是下限，不代表可直接拿到高段。

建議口徑：

```text
我會以年薪 150-180 萬作為主要期待，實際依職務範圍、年終獎金、整體 package 與團隊責任討論。
```

若 HR 要月薪：

```text
期望月薪約 110,000-125,000，若整體 package、年終與技術成長性明確，可以彈性討論。
```
