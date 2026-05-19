# partner-deposit-withdraw-bill evidence

更新時間：2026-05-19
Step：5
掃描等級：Level 2 Flow 深掃 + Step 5 單條 flow claim gate
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Step 5 更新摘要

2026-05-19 Step 5 再次重新 fetch `game_api` remote refs，確認 source repo 最新性，並重跑 partner 相關 path-specific history / Nick author filter。

結論：

- `game_api` local `main` 與 `origin/main` 同步。
- partner flow path history 仍未找到 Nick / `10gt12nc` direct path commit。
- `origin/k3s` 的 partner sign replay / secret log 修正方向仍由 Arnold 提交，不能寫成 Nick 成果。
- 本 flow 完成 Step 5，但只保留為 code-backed 面試素材；不更新正式履歷 / 自傳。
- 不做完整 `game_api contribution claim consolidation`，因 Step 2 本批代表 flows 尚未都完成 Step 5。

## Step 4 更新摘要

2026-05-19 Step 4 重新 fetch `game_api` remote refs，確認 local `main` 與 `origin/main` 仍同步，並把 Step 3 的 code-backed flow 收斂成面試素材：

- `career-interview.md`：補一句話、30 秒、2 分鐘、5 分鐘、STAR、不同職缺講法與 Step 5 下一步。
- `materials/interview.md`：補 3 分鐘講法、常見追問、Lead / Architect 追問、反問面試官與 Step 5 待補。
- `materials/claim-boundary.md`：維持 code-backed 面試素材；未看到 Nick / `10gt12nc` direct path evidence 前，不更新正式履歷 / 自傳。
- `flow.md`、README、Step 文件與共用索引：同步狀態為 Step 4，下一步為 Step 5。
- `senior-owner-playbook/04-interview-casebook.md`：新增 Partner API 上分 / 下分 / 查單案例索引。

Step 4 不新增正式履歷 claim，也不執行 `game_api contribution claim consolidation`。

## 自動重讀紀錄

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `projects/iwin/game_api/README.md`
- `projects/iwin/game_api/step1-candidate-flows.md`
- `projects/iwin/game_api/step2-flow-comparison.md`
- `projects/iwin/game_api/flows/coupon-redeem-credit-grant/flow.md`
- `projects/iwin/game_api/flows/coupon-redeem-credit-grant/materials/evidence.md`
- `projects/iwin/iwin_gameserver/flows/center-http-deposit-withdraw/flow.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`

既有文件狀態：

| 文件 | 狀態 | 判斷 |
| --- | --- | --- |
| `README.md` | 本輪需同步 | 第二條 flow 已完成 Step 5，下一步改回 Step 2 ranking 的第三順位 |
| `step1-candidate-flows.md` | 可沿用 | 已列出此 flow 為第二順位候選 |
| `step2-flow-comparison.md` | 本輪需同步 | 此 flow 從 Step 4 進到 Step 5；下一步回到第三順位 |
| `flows/coupon-redeem-credit-grant/` | 可沿用 | 第一條 flow 已 Step 5，但不代表整個 `game_api` project 完成 |
| `flows/partner-deposit-withdraw-bill/flow.md` | 可沿用 / 本輪同步 | Step 3 / Step 4 內容可用；Step 5 補 claim gate 結論 |

## Code Repo 最新狀態

Source repo：`/Users/nick/Git/iwin/game_api`

- Step 3 已執行 `git fetch --all --prune`；Step 4 開始前已再次執行 `git fetch --all --prune`。
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

## 本輪掃描範圍

Controller / DTO / enum：

- `src/main/java/com/slots/web/controller/PartnerController.java`
- `src/main/java/com/slots/web/pojo/dto/partnerApi/PartnerLoginDto.java`
- `src/main/java/com/slots/web/common/enums/PartnerApiEnums.java`
- `src/main/java/com/slots/web/pojo/vo/center/BillInfo.java`

Service / validation / sign：

- `src/main/java/com/slots/web/service/partner/PartnerService.java`
- `src/main/java/com/slots/web/service/partner/PartnerLoginService.java`
- `src/main/java/com/slots/web/service/partner/ValidatedService.java`
- `src/main/java/com/slots/web/service/partner/impl/PartnerServiceImpl.java`
- `src/main/java/com/slots/web/service/partner/impl/PartnerLogServiceImpl.java`
- `src/main/java/com/slots/web/service/partner/impl/ValidatedServiceImpl.java`

下游關聯：

- `src/main/java/com/slots/web/common/gmintfc/gmcommand/GMCommandNames.java`
- 參考既有 `iwin_gameserver/flows/center-http-deposit-withdraw/flow.md`

history / diff：

- path-specific `git log --all` for PartnerController、PartnerServiceImpl、PartnerLogServiceImpl、ValidatedServiceImpl、PartnerLoginDto、BillInfo。
- Nick / `10gt12nc` author filter for same paths。
- `origin/main..origin/k3s` diff for partner paths。

archive / vault reference：

- 搜尋 `archive`、`projects`、`senior-owner-playbook` 中的 Partner、NewPay、Withdraw、BillInfo、BillList、上分、下分等既有材料。
- 未直接複製 archive 舊文；本輪依 code 重寫。

## 主要 code evidence

### HTTP 入口

`PartnerController`：

- `POST /api/NewPay` -> `partnerService.newPay(dto)`
- `POST /api/Withdraw` -> `partnerService.withdraw(dto)`
- `POST /api/BillInfo` -> `partnerService.billInfo(dto)`
- `POST /api/BillList` -> `partnerService.billList(dto)`

### 參數驗證

`ValidatedServiceImpl`：

- 共用必填：`agentId`、`accountId`、`time`。
- `NewPay` 必填 `value`。
- `Withdraw` 必填 `withdrawType`；`withdrawType=1` 時必填 `value`。
- `BillInfo` 必填 `billNos`。
- `BillList` 必填 `start`、`end`。

### Sign

`PartnerLogServiceImpl#checkSign`：

- main branch 有 30 秒 time window 判斷，但 throw 被註解。
- 從 Redis PHP `SETTINGS / SECRET_KEY` 取 API secret。
- main branch 會 info log API secret 物件內容，屬敏感資訊風險。
- 用 JSON DTO + secret 計算 MD5 sign，不符丟 `20038`。

`origin/k3s` diff：

- 啟用 30 秒時窗 throw `20039`。
- 增加 `partner:nonce:{sign}`，同一 sign 在 TTL 內只能用一次。
- 移除 secret info log，改成 debug 層級的 sign 驗證訊息。

### NewPay

`PartnerServiceImpl#newPay`：

- `billNos = yyyyMMdd + "-" + UUID`
- `getMap(dto, 210, channel)` 驗簽、抓玩家、換算 value。
- `saveCoin(dto, 201, billNos, nTime, 1, channel)` 寫上分訂單。
- `GM_CMD_DEPOSIT` 呼叫 gameserver。
- 成功後 `updCoin(billNos, 0)`。
- `FmsServiceException` catch 會 `updCoin(billNos, 4)`。
- generic `Exception` catch 只回 error `3`，未看到 `updCoin`。

### Withdraw

`PartnerServiceImpl#withdraw`：

- `billNos = yyyyMMdd + "-" + UUID`
- `getMap(dto, 206, channel)`。
- 帶 `withdrawType`、`type`、`billNos`。
- `saveCoin(dto, 206, billNos, nTime, 2, channel)` 寫下分訂單。
- `GM_CMD_WITHDRAW` 呼叫 gameserver。
- 回傳 value 會除以 `channel.getMoneyRatio()`。
- 成功後 `updCoin(billNos, 0)`。
- exception pattern 與 NewPay 相同。

### Mongo order

`saveCoin`：

- DB：`bi_log`
- Collection：`coin_change_log_{today yyyyMMdd}`
- 欄位：`accountId`、`agentId`、`value`、`cps`、`billNos`、`reason`、`time`、`status`、`type`
- 初始 `status=1`

`updCoin`：

- DB：`bi_log`
- Query：`billNos`
- Update：`status`
- Collection：`coin_change_log_{today yyyyMMdd}`
- 使用 `mongo.upsert`

風險判斷：

- 如果跨日補更新，`updCoin` 依當天日期找 collection，可能不是原單所在 collection。
- `upsert` 若找不到原單，可能建立缺少完整欄位的 status-only document。

### BillInfo / BillList

`BillInfo`：

- `billNos` 用 `_` 分隔多筆。
- 從每筆 `billNos` 的 `yyyyMMdd-` 前綴取得 collection 日期。
- Query 條件有 `agentId`、`billNos in (...)`。
- sort time desc，limit default 100 max 500。

`BillList`：

- Query 條件有 `agentId`、`accountId`、可選 `billNos`、`time gte/lte`。
- `start` / `end` 轉日期，`DateUtils.findDates` 產生日 collection list。
- sort 只在 `sort` 不空時套用。
- `queryBillInfos` 逐日 count 與 find。
- catch exception 後只 log，不往上丟。

## Path-specific history

針對 PartnerController、PartnerServiceImpl、PartnerLogServiceImpl、ValidatedServiceImpl、PartnerLoginDto、BillInfo：

- `ad58834 2023-12-07 arnold <arnold@tychetech.com.tw> feat(#first): first commit`
- `ddbaa85 2026-05-06 arnold <arnold@tychetech.com.tw> feat(k3s): 升 Java 21 + Spring Boot 3.2,改走容器化部署`
- `c7b5049 2026-05-13 arnold <arnold@tychetech.com.tw> fix(login): redisPhpService.hget null 防呆,避免登入前接口 NPE`
- `a8d6a0a 2026-05-13 arnold <arnold@tychetech.com.tw> security: 安全性更新`

Nick / `10gt12nc` author filter：

- 本輪未找到 Nick / `10gt12nc` 直接修改上述 partner flow path 的 commit。
- Step 5 重跑同一組 partner flow path author filter，結果仍為空。

## 證據層級判斷

| Claim | 層級 | 判斷 |
| --- | --- | --- |
| `game_api` 有 partner 上分 / 下分 / 查單 flow | 專案存在 / code-backed | 已由 controller / service / Mongo / GM command 確認 |
| 這條 flow 涉及 money correctness | code-backed + analysis | 上下分、訂單、查單與 wallet side effect |
| main branch sign replay 防護不足 | code-backed + analysis | time window throw 註解、無 nonce；`origin/k3s` 已修 |
| Nick 實作此 flow | 待確認 | 本輪 path-specific history 未看到 `10gt12nc` |
| 可寫入正式履歷 | 否 | Step 5 判定仍缺本人確認、commit、ticket、MR 或 production issue evidence |

## Step 5 結論

這條 flow 已完成 Step 5 單條 flow claim gate。它可以作為 code-backed 面試案例，但目前不能寫成 Nick 真實開發過。

下一步回到 `game_api` Step 2 ranking，做第三順位 `agent-bonus-receive-transfer` 的 Step 3。仍不做完整 `game_api contribution claim consolidation`。

下一步：

```text
iwin game_api agent-bonus-receive-transfer Step 3
```
