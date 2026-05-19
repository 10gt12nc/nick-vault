# agent-bonus-receive-transfer evidence

更新時間：2026-05-19
Step：5
掃描等級：Level 2 Flow 深掃
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Step 5 更新摘要

2026-05-19 Step 5 重新 fetch `game_api` 與 `game_job` remote refs，確認兩個 repo 的本機 `main` 仍與 `origin/main` 同步，並補單條 flow claim gate：

- `career-interview.md`：維持 code-backed 面試素材，更新 Step 5 結論與下一步。
- `materials/interview.md`：維持面試講法，更新為 Step 5 完成狀態。
- `materials/claim-boundary.md`：補 Step 5 claim gate；未看到 Nick / `10gt12nc` direct path evidence 前，不更新正式履歷 / 自傳。
- `flow.md`、README、Step 文件與共用索引：同步狀態為 Step 5；後續 `game_api contribution claim consolidation` 已完成。

Step 5 不新增正式履歷 claim。後續 `game_api contribution claim consolidation` 已完成，仍維持本 flow 只作面試素材。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/CONVENTIONS.md`

已重讀 vault：

- `projects/iwin/game_api/README.md`
- `projects/iwin/game_api/step1-candidate-flows.md`
- `projects/iwin/game_api/step2-flow-comparison.md`
- `projects/iwin/game_api/flows/coupon-redeem-credit-grant/flow.md`
- `projects/iwin/game_api/flows/coupon-redeem-credit-grant/materials/evidence.md`
- `projects/iwin/game_api/flows/partner-deposit-withdraw-bill/flow.md`
- `projects/iwin/game_api/flows/partner-deposit-withdraw-bill/materials/evidence.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`

既有文件狀態：

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `README.md` | 已同步 | 本輪改為 Step 5 完成，下一步改 project-level consolidation |
| `step1-candidate-flows.md` | 已同步 | 本輪改為 Step 5 完成 |
| `step2-flow-comparison.md` | 已同步 | 本輪改為 Step 5 完成，三條代表 flow 都已 Step 5 |
| `coupon-redeem-credit-grant` | 可沿用 | Step 5 完成，有 Nick direct evidence |
| `partner-deposit-withdraw-bill` | 可沿用 | Step 5 完成，但只作 code-backed 面試素材 |

## Code Repo 最新狀態

Source repo：`/Users/nick/Git/iwin/game_api`

- Step 3 開始前已執行 `git fetch --all --prune`。
- Step 5 開始前已再次執行 `git fetch --all --prune`。
- 本機 branch：`main`
- local HEAD：`39bb6e38210bb79c6e68a6a6d818cb87986d39f0`
- `origin/main` HEAD：`39bb6e38210bb79c6e68a6a6d818cb87986d39f0`
- ahead / behind：`0 / 0`
- 公司 repo 只讀；本輪沒有 pull、merge、checkout、rebase 或改檔。

近期 remote branch：

- `origin/k3s`
- `origin/main`
- `origin/coupon`
- `origin/bugs/write_black_list`
- `origin/fix-performance`
- `origin/beta`
- `origin/fix-ci-deploy`
- `origin/feature/RD-128`
- `origin/test`

下游 / 上游參考 repo：`/Users/nick/Git/iwin/game_job`

- Step 3 已執行 `git fetch --all --prune`。
- Step 5 開始前已再次執行 `git fetch --all --prune`。
- 本機 branch：`main`
- local HEAD：`23908f474efb5cfe5a3ce2bc780fb67a0860c4c2`
- `origin/main` HEAD：`23908f474efb5cfe5a3ce2bc780fb67a0860c4c2`
- ahead / behind：`0 / 0`
- 只讀掃描；未改工作樹。

## 本輪掃描範圍

`game_api` 已看：

- `src/main/java/com/slots/web/controller/PartnerController.java`
- `src/main/java/com/slots/web/service/share/IAgentShareService.java`
- `src/main/java/com/slots/web/service/share/impl/AgentShareServiceImpl.java`
- `src/main/java/com/slots/web/service/share/ShareCommonService.java`
- `src/main/java/com/slots/web/pojo/dto/share/ReqDto.java`
- `src/main/java/com/slots/web/pojo/entity/share/GameAgentModel.java`
- `src/main/java/com/slots/web/pojo/entity/share/AgentMoneyModel.java`
- `src/main/java/com/slots/web/pojo/entity/share/AgentTransferMoney.java`
- `src/main/java/com/slots/web/pojo/entity/share/BonusReceiveLogModel.java`
- `src/main/resources/mapper/bilog/GameAgentDao.xml`
- `GMCommandNames.GM_CMD_DEPOSIT` 使用點
- `origin/main..origin/k3s` diff for PartnerController / AgentShareServiceImpl / ShareCommonService

`game_job` 已看：

- `src/main/java/com/job/agentTask/AgentBonusWashJob.java`
- `src/main/java/com/job/agentTask/AgentBonusSettlementJob.java`
- `src/main/java/com/pojo/entity/job/AgentMoneyModel.java`
- `src/main/java/com/pojo/entity/job/AgentDayMoneyModel.java`
- `src/main/java/com/pojo/entity/job/PlayerBonusModel.java`

未掃：

- 未掃 gameserver `GM_CMD_DEPOSIT` handler 對 `billNos` 的 idempotency。
- 未掃 DB / Mongo index 實際 schema。
- 未掃 production issue / ticket / MR。
- 未逐 commit diff 全掃，只做 path-specific history 與 `origin/k3s` 相關 diff。

敏感資訊處理：

- source repo grep 過程看到環境 config 可能含敏感資訊；本文件只記錄「source repo 有敏感 config 風險」，不摘錄任何 token、password、internal IP、production URL 或客戶資料。

## 主要 code evidence

### HTTP 入口

`PartnerController`：

- `POST getDayFenx_transfer` -> `agentShareService.transferReceive(dto.getRid(), dto.getCRid(), dto.getMoney())`
- `POST getDayFenx_enter` -> `agentShareService.bonusReceive(dto.getRid())`
- `POST getDayFenx_zhuanzhang` -> `agentShareService.getBonusTransferRecord(dto.getRid())`
- `POST getDayFenx_lingqu` -> `agentShareService.getBonusReceiveRecord(dto.getRid())`
- `POST getFenx_money` -> `agentShareService.getShareTotal(dto.getRid())`

`origin/k3s` diff：

- Partner share APIs 改為從 token 取 uid，不再信任 body 的 rid。
- 這是 auth / identity boundary 改善，但目前不是 Nick evidence。

### 轉帳

`AgentShareServiceImpl#transferReceive`：

- 檢查 `money < 100`。
- 檢查不能自己轉給自己。
- 呼叫 `shareService.checkBonusReceiveLook(rid)` 防短時間重複提交。
- 若 `proxyType == 2`，用 `shareService.receiveMoney(today)` 檢查收益計算任務是否執行中。
- 查 `cRid` 的 `GameAgentModel`，並用 `link.contains(rid)` 檢查是否為團隊成員。
- 查轉出者 `agent_money`。
- `receiveMoney = moneyModel.money - money`，不足則拒絕。
- 更新轉出者 `agent_money.money` 與 `transferMoney`。
- upsert 轉入者 `agent_money.money` 與 `transferMoney`。
- 更新 Redis `GameAgent:{rid}.money` 與 `GameAgent:{cRid}.money`。
- 插入轉出 / 轉入兩筆 `agent_transfer_Money`。

### 領取

`AgentShareServiceImpl#bonusReceive`：

- 查代理資料。
- 呼叫 `checkBonusReceiveLook(rid)`。
- 若 `proxyType == 2`，用 `receiveMoney(today)` 檢查收益計算任務。
- 查 Mongo `agent_money`。
- `receiveMoney = moneyModel.money`，`transferMoney = moneyModel.transferMoney`。
- 若 `receiveMoney - transferMoney > 0`，用 reason 61 送 `GM_CMD_DEPOSIT`。
- 若 `transferMoney > 0`，用 reason 62 再送 `GM_CMD_DEPOSIT`。
- catch GM exception 回 `20069`。
- GM 成功後，更新 `agent_money.money=0`、`totalMoney += receiveMoney`。
- 更新 Redis `GameAgent:{rid}.money=0`、`total_money`。
- 插入 `vc_countlog` 領取紀錄。

### Lock / job status

`ShareCommonService#checkBonusReceiveLook`：

- Redis key：`Bonus:ReceiveLook:{rid}`
- 若 key 存在，回 true。
- 若 key 不存在，`setIfAbsent(key, rid, 5)` 後回 false。
- 註解寫 20 秒，實作傳入 5；TTL 單位待確認。

`ShareCommonService#receiveMoney`：

- Redis key：`Daily:Task:{date}`
- field：`purchase_status`
- status=1 時視為收益計算 job 執行中，拒絕領取 / 轉帳。

### Mongo / Redis model

`AgentMoneyModel`：

- collection：`agent_money`
- 欄位：`rid`、`money`、`transferMoney`、`totalMoney`、`lastModifyTime`、`channel`

`AgentTransferMoney`：

- collection：`agent_transfer_Money`
- 欄位：`rid`、`cRid`、`money`、`createTime`、`type`、`channel`

`BonusReceiveLogModel`：

- collection：`vc_countlog`
- 欄位：`sys_id`、`tx_count`、`info`、`channel`、`create_time`

### 上游 game_job

`AgentBonusWashJob`：

- 從 Redis 每日佣金資料計算玩家 / 代理佣金。
- 將每日佣金資料寫到 Mongo `agent_day_money`。
- 將可查詢的分享資料寫到每日 `agent_share` 類 collection。

`AgentBonusSettlementJob`：

- 檢查 `Daily:Task:{date}`，確保 wash job 已完成且 settlement 未重複執行。
- 查 `agent_day_money` 中 `bonus > 0` 的資料。
- 依 rid 查 Mongo `agent_money`，把 `dayMoney.bonus` 累加到 `agent_money.money`。
- 寫 `agent_money_log`。

## Path-specific history

`game_api` partner / agent share paths：

- `ad58834 2023-12-07 arnold <arnold@tychetech.com.tw> feat(#first): first commit`
- `ddbaa85 2026-05-06 arnold <arnold@tychetech.com.tw> feat(k3s): 升 Java 21 + Spring Boot 3.2,改走容器化部署`
- `a8d6a0a 2026-05-13 arnold <arnold@tychetech.com.tw> security: 安全性更新`

Nick / `10gt12nc` author filter：

- 本輪未找到 Nick / `10gt12nc` 直接修改 `AgentShareServiceImpl`、`ShareCommonService`、Partner share endpoints、AgentMoney / transfer / receive log model 或 GameAgent mapper 的 commit。
- Step 5 重跑同一組 agent bonus path author filter，結果仍為空。

`game_job` agent bonus paths：

- `c8a3fde 2023-12-07 arnold <arnold@tychetech.com.tw> feat(#first): first commit`
- `42ae838 2026-05-07 arnold <arnold@tychetech.com.tw> feat(k3s): 升 Java 21 + Spring Boot 3.2,改走容器化部署`

Nick / `10gt12nc` author filter：

- 本輪未找到 Nick / `10gt12nc` 直接修改 `AgentBonusWashJob`、`AgentBonusSettlementJob` 或相關 agent bonus model 的 commit。
- Step 5 重跑同一組 game_job agent bonus path author filter，結果仍為空。

## Step 5 claim gate

已確認：

- `game_api` agent bonus 入口與實作存在，且可由 Controller / Service / Mongo model / Redis key / GM command 串成完整 code-backed flow。
- 上游 `game_job` agent bonus wash / settlement job 存在，會把每日佣金累加到 Mongo `agent_money.money`。
- `game_api` 本輪 repo-wide Nick / `10gt12nc` author log 顯示直接 evidence 主要集中在 coupon 兌換與邀請轉盤活動，不在 agent bonus path。
- `game_job` repo-wide Nick / `10gt12nc` author log 顯示直接 evidence 主要集中在每日遊戲資料彙總、GSC 分批查詢與 coupon ActivityJob / CouponRecord 相關，不在 agent bonus wash / settlement path。

判斷：

- 本 flow 是 `專案存在 / code-backed` 面試素材。
- 目前不能升級成 `真實開發過`。
- 不更新 `senior-owner-playbook/05-resume-master-zh.md` 或 `08-application-autobiography-zh.md` 的正式履歷 / 自傳主成果。
- 已在 `game_api contribution claim consolidation` 中作為「可面試講：分析過」素材；正式履歷主 evidence 仍以 coupon flow 與其他 Nick direct evidence 為主。

## 證據層級判斷

| Claim | 層級 | 判斷 |
| --- | --- | --- |
| `game_api` 有代理佣金領取 / 轉帳 flow | 專案存在 / code-backed | Controller / Service / Mongo / Redis / GM command 已確認 |
| 本 flow 涉及 money-like balance correctness | code-backed + analysis | 可領佣金、轉帳、GM 上分、Mongo / Redis projection |
| `game_job` 是佣金來源之一 | code-backed | AgentBonusWash / Settlement 已確認 |
| Nick 實作此 flow | 待確認 | path-specific history 未看到 `10gt12nc`，也無本人確認 |
| 可寫入正式履歷 | 否 | 需要本人確認、commit、ticket、MR 或 production issue evidence |

## Step 5 結論

這條 flow 已完成 Step 5 單條 flow claim gate。它是高價值 code-backed 面試素材，但目前不更新正式履歷 / 自傳。

下一步：

```text
iwin iwin_gameserver center-http-deposit-withdraw Step 4
```
