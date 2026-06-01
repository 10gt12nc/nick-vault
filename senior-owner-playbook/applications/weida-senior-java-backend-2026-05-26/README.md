# 微達軟體 Senior Java Backend 客製包

職缺：資深 Java 後端工程師【擴編】

狀態：2026-06-01 JD-specific 客製版。

來源：Nick 貼上的 104 職缺內容，未額外查公司公開資料。本包只針對 JD 文字做履歷與面試準備，不代表已完成公司背景、產品、財務或團隊文化調查。

## 讀法

1. [jd-fit-analysis.md](jd-fit-analysis.md)：匹配度、風險與投遞策略。
2. [resume-autobiography.md](resume-autobiography.md)：104 可貼的客製工作經驗、專長、自傳、自我推薦。
3. [interview-qa.md](interview-qa.md)：HR / 薪資 / 文化 / 技術問答與回答邊界。
4. [basic-review.md](basic-review.md)：JD-specific 20% 基本功最小複習清單，包含 AOP / transaction / SQL / Redis / MQ / microservice。

## 結論

這個 JD 適合用 A 版主軸：`通用高交易 Senior Java Backend / Platform Backend`。

主打：

- Java / Spring Boot 後端。
- 線上服務穩定性與 production troubleshooting。
- RabbitMQ / Kafka / Quartz / batch。
- MySQL / Redis / MongoDB。
- 第三方 provider integration、payment provider、wallet / bet-settle。
- legacy system takeover、code reading、git history、DB / Redis / MQ / log flow reconstruction。
- code review、工程品質、AI-assisted engineering workflow。

降調：

- Slot math 只作差異化，不放最前面。
- K3s / rollout / observability 只作 interview-only 加分。
- Spring Cloud、PostgreSQL、DDD / Clean Architecture 只能說理解 / 願意補強，不寫成主力實戰。
- 技術導師與架構設計只能說參與 code review、整理 decision、協助團隊理解 flow，不說正式 Tech Lead。

## 複習策略

這個 JD 可能會問 Spring AOP、transaction、SQL index、Redis consistency、MQ duplicate consume 等基本功，但不需要讀完整 Spring 原始碼或建立泛用八股題海。

比例固定：

```text
70%：production case，先把 payment provider、wallet / bet-settle、MQ / projection 講熟。
20%：JD-specific 基本功，針對 Spring / SQL / Redis / MQ / microservice 常見追問補最小答案。
10%：coding test / LeetCode 保險，有面試邀約或筆試再補手感。
```
