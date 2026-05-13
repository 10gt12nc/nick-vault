# 核心定位：從功能工程師到高交易 Production System Owner

這套學習包的核心不是「學更多 Java 後端技巧」。

真正目標是：

> 成為能理解、維護、判斷、改善高交易 production 系統的人。

這和一般 CRUD backend engineer 的差別很大。CRUD 重點是功能能不能做出來；高交易 production 系統重點是功能上線後資料會不會錯、交易會不會重複、錢會不會不一致、線上問題能不能追、失敗後能不能恢復。

## 1. 你的定位

Nick 目前最有價值的路線不是「一般 Java 後端」，而是：

> High-Transaction Platform Backend Engineer

這條線包含：

- 高交易平台
- 金流 callback
- transfer wallet / ledger
- 下注、派彩、rollback、結算
- Kafka / MQ
- retry / compensation
- distributed consistency
- DB performance
- production troubleshooting
- reverse engineering
- RBAC / admin platform
- platform integration

市場高薪後端真正買單的，通常不是「功能寫很快」，而是能扛核心流程、交易正確性、線上風險與跨系統整合的人。

## 2. 過去容易誤判的地方

以前容易把這些事看成雜事：

- 修 callback
- 查 retry
- 查 lock / deadlock
- 查 DB 慢 query
- 查 Kafka / MQ 問題
- 逆向舊系統
- 補資料
- 對帳
- reconciliation
- production issue

但對 Senior Backend 來說，這些不是低價值維運。

這些正是高交易系統最值錢的能力：

- 系統會不會壞
- 資料會不會錯
- 交易會不會重複
- 錢會不會不一致
- 事故能不能定位
- 失敗能不能補償
- 營運能不能查得到狀態

## 3. Repo Analysis Guide 真正在做什麼

這套 prompt / guide 不是叫 AI 幫你摘要 class。

它真正做的是：

> 用 production owner 的角度分析系統。

所以它不重視：

- class summary
- Spring 教學
- CRUD 解說
- 方法逐行翻譯

它重視：

- flow
- state
- failure window
- consistency boundary
- retry / compensation
- source of truth
- audit / traceability
- operational risk
- owner decision

這些才是 Senior / Lead / Architect 會看的東西。

## 4. Flow 比 class 重要

production 真正出事通常不是因為某個 Controller 名字不好看，而是 flow 邊界出問題。

要看的不是：

```text
Controller -> Service -> Mapper
```

而是：

```text
API
-> Service
-> DB
-> Redis
-> MQ
-> callback
-> retry
-> state change
-> failure handling
-> reconciliation
```

每一條高價值 flow 都要回答：

- 入口在哪裡？
- source of truth 是哪張表或哪個狀態？
- Redis 是 cache、lock、queue，還是暫存狀態？
- MQ 發送前後的 durable state 是什麼？
- 外部 provider timeout 後怎麼判斷？
- callback duplicate 怎麼處理？
- retry 會不會造成重複副作用？
- 人工補償後還能不能對帳？

## 5. State 是高交易系統的核心

高交易平台本質上是 state machine。

不是只看 API 回傳成功或失敗，而是要看：

- 訂單狀態怎麼轉
- wallet 狀態怎麼轉
- round 狀態怎麼轉
- callback 狀態怎麼轉
- retry 狀態怎麼轉
- hidden state 在哪裡
- 終態是否可以被覆蓋
- 半完成狀態是否可查、可補、可恢復

Senior 要能講清楚：

- 哪些狀態可以重試
- 哪些狀態不可逆
- 哪些狀態只能人工介入
- 哪些狀態需要 audit log
- 哪些狀態代表資料已經不一致

## 6. Failure Scenario 才是深度

一般工程師會說功能流程。

Senior 要能說 failure scenario：

- callback duplicate
- MQ duplicate
- retry storm
- timeout
- deadlock
- lock contention
- cache stale
- DB 成功但 MQ 失敗
- MQ 成功但 consumer 失敗
- provider 回傳不明
- reporting job 重跑
- reconciliation 差異
- manual fix 造成二次不一致

這其實就是 distributed system thinking。

不是背名詞，而是知道系統在壓力、失敗、重送、延遲、不一致時會發生什麼。

## 7. Senior / Owner 的三個核心能力

### Production Risk Thinking

功能不是唯一重點，風險才是重點。

要能問：

- 會不會 double spend？
- 會不會重複入分？
- 會不會重複派彩？
- 會不會資料不一致？
- 會不會 replay？
- 會不會 retry storm？
- 會不會造成 lock contention？
- 會不會讓營運查不到真相？

### Trade-off Thinking

Senior 不是只知道技術，而是知道代價。

每個改善都要能說：

- Option A 是什麼
- Option B 是什麼
- 優點
- 缺點
- migration risk
- rollback plan
- monitoring
- 對既有營運流程的影響

這是 architecture decision making。

### Ownership Thinking

Feature engineer 想的是：

```text
需求來了 -> 功能做完
```

System owner 想的是：

```text
這功能上線後：
會不會壞？
會不會不一致？
能不能追？
能不能 rollback？
能不能補償？
營運能不能查？
下一個人能不能維護？
```

這是從功能工程師往 core service owner 轉變的關鍵。

## 8. 你現在真正缺的不是廣度

短期不要被這些焦慮帶走：

- 要不要學 Go
- 要不要全端
- 要不要追所有雲端服務
- 要不要學一堆 AI 工具
- 要不要每個框架都碰

目前最高 CP 值是把既有經驗打深：

- Kafka / MQ
- DB performance
- distributed consistency
- production troubleshooting
- system design articulation
- transaction / wallet / settlement flow
- observability
- rollback / compensation / reconciliation

這些直接對應 Senior Java Backend / Platform Backend / System Owner 市場價值。

## 9. 目前最大缺口

### System Design Articulation

你可能已經看得懂，但還不一定講得清楚。

要訓練的是：

- 為什麼這樣設計
- 為什麼不用另一種方案
- trade-off 是什麼
- consistency 怎麼保
- retry 為什麼這樣做
- 失敗後怎麼恢復
- 監控要看什麼

### Case Study Packaging

很多 production work 本來就有價值，但需要包裝成：

```text
production work
-> senior case study
-> interview story
-> conservative resume bullet
```

但包裝不是誇大。不能把「看過、分析過」寫成「主導完成」。要清楚區分：

- 實際做過
- 參與維護
- 分析整理
- 提出改善方向
- 尚待 Nick 本人確認

### 深度而不是廣度

一條 flow 如果能講清楚 code path、資料狀態、failure window、owner decision、面試說法與履歷邊界，比掃過十個 repo 但都只知道 class name 更有價值。

## 10. 這套學習包的使用方式

每次只選一條 flow。

不要問：「這份文件有沒有很多？」

要問：

- 我能不能用 3 分鐘講給面試官聽？
- 我能不能畫出資料流？
- 我能不能指出 source of truth？
- 我能不能說出 3 個 failure scenario？
- 我能不能說明 retry / compensation / reconciliation？
- 我能不能提出一個保守但有價值的改善方向？
- 我能不能把它寫成不誇大的履歷 bullet？

做到這樣，才算一條 flow 完成。

## 11. 最終目標

這套資料不是為了讓 Nick 變成「很會寫 Java 的人」。

真正目標是：

> 從會寫 code，升級成會判斷 system。

最後要走的路線是：

```text
Feature Engineer
-> Platform Backend Engineer
-> Core Service Owner
-> Senior Backend Engineer
-> Lead / Architect 候選人
```

這條路的核心不是炫技，而是能扛住 production：

- correctness
- consistency
- reliability
- traceability
- recoverability
- operability
- maintainability

這才是高交易後端真正值錢的地方。
