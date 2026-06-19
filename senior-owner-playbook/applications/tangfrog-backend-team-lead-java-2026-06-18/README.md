# 糖蛙 Backend Team Lead Java 客製包

職缺：JAVA後端組長 / Backend Team Lead (Java)

公司：糖蛙線上娛樂股份有限公司

狀態：2026-06-19 JD-specific 客製版。JD 標示 2026-06-18 更新。

來源：Nick 貼上的 104 職缺內容，並以 2026-06-19 當下 vault 內 `05 / 08 / 11 / 17` 與既有 project contribution consolidation 對齊。本包只針對 JD 文字做履歷與面試準備，不代表已完成公司背景、產品、財務或團隊文化調查。

## 讀法

1. [jd-fit-analysis.md](jd-fit-analysis.md)：匹配度、風險、投遞策略與薪資判斷。
2. [resume-autobiography.md](resume-autobiography.md)：104 可貼的客製工作經驗、專長、自傳、自我推薦。
3. [interview-qa.md](interview-qa.md)：HR / Team Lead / AI / 技術問答與回答邊界。
4. [basic-review.md](basic-review.md)：JD-specific 20% 基本功最小複習清單。

## 結論

這份 JD 值得投，而且比一般 Senior Java Backend 更貼 Nick 目前的差異化組合。

建議定位：

```text
Senior Java Backend / Hands-on Backend Tech Lead Candidate
```

主打：

- Java / Spring Boot 後端與平台型系統維護。
- 遊戲 / 博弈 production flow：第三方 provider、payment provider、wallet / bet-settle、MQ / batch、slot job。
- 線上穩定性：state transition、idempotency、retry / compensation、observability、source of truth。
- Legacy takeover / system reconstruction：缺交接文件下重建 DB / Redis / MQ / log flow。
- AI-assisted engineering workflow：Codex / Claude 類工具輔助 code reading、diff review、文件同步、測試檢查與 KB 回填。
- Code Review / 技術帶人潛力：任務拆解、風險檢查、文件化、交接與團隊標準建立。

降調：

- 不包裝成已正式管理 3-8 人 2 年。
- 不寫完整架構師、完整 DevOps / K8s owner、完整 payment / wallet / MQ platform owner。
- Docker / K3s / Kubernetes 只作理解與 interview 加分，不作主成果。
- Spring Cloud、PostgreSQL 若未補 evidence，只寫可快速轉換 / 可補強。

## 投遞策略

這份不是純管理職，也不是一般 CRUD 後端。最適合用「能寫 code、能拆 production flow、能建立 AI-assisted review / KB / quality discipline 的 hands-on lead candidate」去投。

履歷應以 `08` A 版為底，混入 B 版遊戲 / provider domain 與 C 版 legacy takeover / AI-assisted workflow；但不改通用 `05 / 08` 主版本。
