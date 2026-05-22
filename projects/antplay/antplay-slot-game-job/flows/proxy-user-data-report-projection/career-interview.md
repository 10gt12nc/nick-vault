# proxy-user-data-report-projection Career / Interview

日期: 2026-05-22

## Evidence Level

- 證據層級: 真實開發過 + code-backed。
- Nick / `10gt12nc` direct evidence: `#386` 代理用戶數據、`#590` currency 拆分、`#702` key 重複、`fix ag_report_player`。
- 本文件是 flow-level 面試素材，不是 project-level final consolidation。正式履歷仍以 `contribution-claim-consolidation.md` 與後續 Step 5 claim gate 為準。

## 履歷保守 Bullet

可放入 project-level 素材池的保守版本:

- 參與 AntPlay slot job / event processing 維護，處理 Kafka `settled_bets` 事件投影、代理玩家日報表聚合、currency 維度修正與 Quartz summary / backup / cleanup 類報表資料治理。

更短版本:

- 參與 Kafka / Quartz job 報表 projection 維護，處理代理玩家報表聚合、幣別維度與歷史資料彙總清理。

## 30 秒說法

我有參與 AntPlay slot job 裡代理玩家報表的 event projection。這條 flow 會消費 `settled_bets` Kafka event，把 bet record 依代理、玩家、日期、幣別聚合到 `ag_report_player`，再由每天 02:00 的 Quartz job 把三天前以前的日資料彙總、備份、刪除。這裡我會特別注意 report key、currency、batch insert / update、以及 summary 重跑時可能重複加總的 failure window。

## 90 秒說法

這條 flow 不是下注主交易，而是結算後的 derived report。上游送出 `settled_bets` event 後，`ProxyUserDataConsumerService` 會解析 `SettleBetsVo`，逐筆取 `agentId / playerId / account / bet / totalWin / currency`，再用 `agentId + playerId + dataDay + currency` 當 key 累加下注筆數、下注額、總贏分與 profit。DB 寫入前會先查舊 row，沒有就 insert，有且目前累計筆數較大才 update。

後面還有 `ReportAgentPlayerJob` 每天 02:00 跑 summary，把三天前以前的日報表按玩家與幣別彙總到 `dataDay = 0`，然後 backup / delete 舊日資料。這個 case 可以講出很多 Senior 關注點，例如 currency 加入 key 避免混算、agentId key 修正避免跨代理 collision、report table 是 derived projection 不是 source of truth，以及 summary / backup / delete 如果不是同一 transaction，要怎麼設計重跑與 reconciliation。

## 3 分鐘說法

我參與過 AntPlay slot job 裡代理玩家報表 projection 的維護。這條 flow 的背景是，下注結算完成後，上游會把一批 `BetRecord` 包成 `SettleBetsVo` 丟到 Kafka `settled_bets` topic。job service 端的 `ProxyUserDataConsumerService` 會消費這個 event，依 `agentId + playerId + dataDay + currency` 把下注筆數、下注額、total win、profit 聚合到報表 table。

這裡比較容易出問題的是 key design 與 consistency。早期如果 key 只有 player / day，或沒有把 currency、agentId 一起納入，比較容易在多幣別或不同代理同玩家資料時混算。後續我這邊的維護 evidence 包含 `#590` currency 維度拆分，以及 `#702` key 重複修正，把 agentId 納入 oldMap key 判斷。這類改動的重點不是只是多一個欄位，而是報表 projection 的 identity 要和業務維度一致。

寫入 DB 時，consumer 不是每筆直接加總，而是 process 內用 map 累積後查舊 row，沒有就 insert，有且新的 betCount 比 DB 大才 update。這能降低部分重送造成的 double add，但也帶來長生命週期 map、consumer concurrency、restart 後狀態如何恢復等問題，所以我會把這張表定位成 derived report，而不是交易 source of truth。

另一段是 Quartz summary job。它每天 02:00 把三天前以前的日報表彙總到 `dataDay = 0` 的 summary row，然後 backup 再 delete 舊資料。這裡的 owner 問題是 partial failure: 如果 summary update 成功但 backup 或 delete 失敗，下次重跑可能重複加總；如果 delete 成功但備份有問題，也會造成查帳壓力。所以實務上我會建議補 job execution id、處理筆數、agent / player / currency 維度 log、backup 去重或重跑 runbook，並保留用 source bet record 或 Kafka replay 重建報表的能力。

這個 case 我會保守描述為「參與 Kafka / Quartz 報表 projection 與資料治理維護」，不會說自己主導完整 Kafka platform 或完整 BI 系統。

## STAR

| 面向 | 內容 |
| --- | --- |
| Situation | AntPlay slot 需要把結算後 bet records 轉成代理 / 玩家報表，支援營運與代理查詢。 |
| Task | 維護 Kafka event projection、currency 維度、key collision 修正與歷史報表 summary / cleanup。 |
| Action | 追 `settled_bets` consumer、report key、`ag_report_player` insert / update、Quartz summary / backup / delete，修正或理解 currency / agent key 邊界。 |
| Result | 報表 projection 維度更貼近業務 identity，可作 Kafka / Quartz / derived report consistency 的面試 case。 |

## 可說

- 我參與過這條 flow 的開發維護，有 direct commit evidence。
- 我能解釋 Kafka event 如何投影到 DB report table。
- 我能講 currency 維度與 key collision 的一致性問題。
- 我能分析 Quartz summary / backup / delete 的 partial failure window。

## 不可誇大

- 不說我設計完整 Kafka event platform。
- 不說這條 flow 有 exactly-once。
- 不說我主導完整 BI / report platform。
- 不說我主導完整下注結算 source of truth。

## 待 Step 4 補強

- 把 failure windows 轉成正式追問回答。
- 補一版更像面試現場的 90 秒壓縮說法。
- 補「如果重做會怎麼設計」的 owner answer。
