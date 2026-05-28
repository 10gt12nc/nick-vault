# antplay-slot-admin-api Contribution Claim Consolidation

日期: 2026-05-20

## 結論

`antplay-slot-admin-api` 可以列為 Nick / `10gt12nc` 真實開發過的後台 API / control plane repo。它比純前端或 workspace 更有履歷價值，因為本 repo 有大量 direct commits，且涉及商戶登入 / 白名單、JWT / RBAC、超級代理、玩家 / 投注 / request log / 報表查詢、RTP / 暗池 / 活動風控監控、Quartz job、RabbitMQ request log 與風控通知。

履歷可以保守寫:

> 參與 AntPlay 後台 API / 商戶控制面開發維護，處理 admin / merchant auth、商戶白名單、Game API 白名單同步、超級代理、玩家 / 投注 / request log / 報表查詢、RTP / 暗池 / 活動風控監控、RabbitMQ request log / 風控通知與 Quartz job。

不要寫:

- 不得寫成主導完整 AntPlay slot platform。
- 不得寫成主導完整遊戲下注 / 派彩 / rollback runtime。
- 不得寫成主導完整 wallet / ledger / reconciliation。
- 建立完整 RabbitMQ architecture、exactly-once 或 outbox。
- 完整風控平台 owner、完整報表平台 owner 或量化改善。

## Evidence Summary

| 類別 | 判斷 | Evidence |
| --- | --- | --- |
| 直接開發 | 真實開發過 | `git log --all --author='10gt12nc|Nick|nick'` 有大量 direct commits；`shortlog --all` 顯示 `10gt12nc` 約 182 筆、`nick` 約 11 筆 |
| admin API 初版 | 真實開發過 | `feat(#171): slot-admin api 開發 / login logout / agent list / swagger / logout / refresh` 系列 commits |
| auth / JWT / RBAC | 真實開發過 | `fix(#383): jwt payload 塞參數 / API JWT 過期回 401`、`RoleTypeFilter 技術客服`、admin / merchant auth commits |
| 商戶白名單 / Game API 白名單 | 真實開發過 | `feat(#421): 商戶白名單`、`feat(#682): slot-game-api API 白名单功能`、`同步 game-api GAME_API_WHITE_IP` |
| 超級代理 / merchant control plane | 真實開發過 | `feat(#444): 超级代理` 系列，涵蓋商戶玩家列表、盈虧、每日報表、request log、投注記錄 |
| risk / RTP / dark pool monitoring | 真實開發過 | `feat(#659): 商户游戏风控概况`、`feat(656): 新增监控job`、`暗池调整会写记录`、monitor jobs / alert mapper |
| 玩家單點控制 | 真實開發過 | `feat(#767): 单点控制，agentPlayer 建立`、player control mapper / service / record |
| RabbitMQ / async | 真實開發過 | `feat(#774): RequestLog 改丢 rabbitmq 非同步执行`、`feat(#775): rabbitmq 收风控通知`、listener / mapper commits |
| final 全量 flow | 待補 | Step 1 / Step 2 已於 2026-05-28 完成；尚未建立單條 flow packages；本檔是 rolling consolidation |

## Source Scan Record

來源 repo:

```text
/Users/nick/Git/antplay/antplay-slot-admin-api
```

遠端狀態:

- 已嘗試執行 `git fetch --all --prune`，但 remote 為內網 Git，當前連線失敗；remote refs 未能更新。
- local branch: `main`
- local HEAD: `2e15503d88de3af133cce17fbf3ff80262678419`
- remote HEAD: `origin/main`
- 本機既有 `origin/main`: `2e15503d88de3af133cce17fbf3ff80262678419`
- local vs 本機既有 `origin/main`: `0 / 0`
- source repo 工作樹乾淨。
- 重要限制：因 fetch 失敗，本次只能宣稱「本機既有 refs / code-backed」，不能宣稱已看最新 remote。

本次掃描範圍:

- vault KB: `AGENTS.md`、`00-operating-rules.md`、`09-ai-prompt-manual.md`、`03-flow-learning-package-template.md`
- vault index: source repo inventory、flow inventory、05 / 08 / todo、既有 iwin / ugsoft claim 邊界
- source git: `git log --all`、`git log origin/main`、`shortlog --all`、remote branch list、`ls-tree origin/main`
- key commits: `#171` admin API 初版、`#421` 商戶白名單、`#444` 超級代理、`#659` 風控概況、`#682` Game API 白名單、`#767` 單點控制、`#774` request log RabbitMQ、`#775` 風控通知 RabbitMQ
- source code: controllers、mappers、RabbitMQ listeners、Quartz jobs、report / request log / monitor / player control / white IP 相關路徑

未完成:

- 已完成 `antplay-slot-admin-api Step 1 / Step 2`；第一條代表 flow 選定 `request-log-rabbitmq-admin-consumer`，尚未建立單條 flow package。
- 未逐條 flow 建立 `flow.md` / `career-interview.md`。
- 未掃 `antplay-slot-game-api` / `antplay-slot-game-job`，不能把 admin API claim 擴張成遊戲 runtime claim。
- 未逐檔逐行 Level 3。

## Important Commit Evidence

### Admin API 初版 / auth / JWT

- `#171` 系列：`slot-admin api 開發`、`login logout`、agent list、swagger、logout、refresh、response shape 等。
- `aaef730`：`jwt payload 塞參數`、JWT 過期回 HTTP 401，涉及 `SecurityConfig`、`AuthController`、`AdminAuth`、access denied / authentication entry point、`JwtUtil`。
- `454cdcc`：`RoleTypeFilter 技术客服`。

可支撐:

- 參與 admin API 初版與 auth / JWT / RBAC 類後台基礎能力。

不可誇大:

- 不說完整 IAM / SSO / security architecture owner。

### 商戶登入 / 白名單 / Game API 白名單

- `ed79070` / `c0510a3` / `154088a` / `e80b613`：商戶白名單與 merchant login / agentId 驗證，涉及 `MerchantController`、`WhiteListFilter`、`LoginIpWhiteListService`、repository。
- `f90a1eb`：slot-game-api API 白名單功能，涉及 `GameApiWhiteIpController`、entity、mapper、service、Redis key 與 operation log。
- `0f0e559`：同步 game-api `GAME_API_WHITE_IP`。

可支撐:

- 後台 control plane 對 runtime API 白名單 / cache 同步的維護。
- 面試可講「後台設定不是純 CRUD，錯了會影響 game-api 對外入口」。

不可誇大:

- 不說主導完整 game-api gateway 或完整 security platform。

### 超級代理 / 商戶查詢 / 報表

- `2c9f48d`：超級代理 squash，包含商戶玩家列表、盈虧、每日報表、request log、投注記錄。
- 相關 path: `AgentDailyController`、`BetRecordController`、`PlayerController`、`RequestLogController`、`RoleFilter`、`ReportAgentPlayerServiceImpl`、`AgentService`。

可支撐:

- 參與多租戶 / 上下級代理視角的查詢與權限邊界。

不可誇大:

- 不說完整代理佣金或完整商戶結算 owner。

### RTP / 暗池 / 活動風控監控

- `57b101f`：商戶遊戲風控概況，涉及 `MonitorController`、`AgentRtpRiskSummaryFacade`、RTP exceed mapper、monitor jobs / services。
- `1c2af07` / `3b391f5`：RTP exceed / Redis RTP 監控 job。
- `3486523`：暗池調整寫記錄與備註，涉及 `AgentRTPController`、`DarkPoolChangeLogMapper`、`DarkpoolService`、`ResetRedisRtp`。
- 其他 monitor path 包含 `MonitorAgentVoucherRtpExceed`、`MonitorDarkPoolRecord`、`MonitorBetVoucherExceed`。

可支撐:

- 後台風控 / RTP / 暗池監控與告警資料流。
- 面試可講 threshold、query window、alert persistence、false positive / false negative 邊界。

不可誇大:

- 不說完整風控平台或完整數學風控 owner。

### 玩家單點控制

- `5dc67cf` / `e12a398` / `0aa7db8`：單點控制、agentPlayer 建立與 SQL 修正，涉及 `AgentPlayerMapper`、`PlayerControlService`、mapper XML。
- 現有 code path 包含 `PlayerControlController`、`MerchantPlayerControlController`、`PlayerControlRecordController`、backup / cleanup jobs。

可支撐:

- 參與後台玩家控制 / 風控操作資料結構與營運查詢。

不可誇大:

- 不說完整玩家風控策略 owner；目前只是 admin API / control plane 層 evidence。

### RabbitMQ request log / 風控通知

- `814b024` / `a66007d` / `f3a9d72` / `5f838f8` / `fa86544`：RequestLog 改丟 RabbitMQ 非同步執行，涉及 `RequestLogListener`、`RequestLogMapper`、request log entity / mapper XML。
- `250811a` / `44d1f84` / `05156ef` / `8be68ca`：rabbitmq 收風控通知，涉及 `createMonitorAlertListener`、`MonitorAlertMapper`。

可支撐:

- 參與後台資料寫入從同步查詢 / 寫入轉到 RabbitMQ consumer 的非同步化。
- 面試可講 eventual consistency、duplicate message、consumer failure、mapper idempotency、UAT 配置風險。

不可誇大:

- 不說完整 RabbitMQ architecture、exactly-once、outbox 或全鏈路可靠性 owner。

## Resume Claim

可放履歷:

- 參與 AntPlay 後台 API / 商戶控制面開發維護，處理 admin / merchant auth、JWT / refresh / RBAC、商戶白名單、Game API 白名單同步、超級代理與商戶玩家 / 投注 / request log / 報表查詢。
- 參與 RTP / 暗池 / 活動風控監控與 Quartz job 維護，處理風控概況、threshold alert、暗池調整記錄與 monitor alert 資料流。
- 參與 request log 與風控通知 RabbitMQ 非同步處理，處理 listener、mapper 與資料入庫邊界。

可面試講:

- 後台 control plane 怎麼影響 runtime: 商戶白名單、Game API 白名單、agent / game 設定與 Redis / RabbitMQ cache refresh。
- 超級代理查詢如何處理上下級資料邊界與 role filter。
- 風控監控如何從 report / Redis / bet record 算 threshold，再寫 monitor alerts。
- request log 改 MQ 後的一致性、重複消費、失敗補償、UAT config 風險。
- 玩家單點控制的資料建立、record 查詢、過期備份 / cleanup 風險。

不可誇大:

- 不說主導完整 AntPlay slot platform。
- 不說主導完整 game runtime、下注 / 派彩 / rollback、wallet / ledger / reconciliation。
- 不說完整風控平台、完整報表平台或完整 RabbitMQ 架構 owner。
- 不說量化改善或事故修復，除非後續補 production issue / metric。

## 05 / 08 更新口徑

建議補入履歷 / 自傳的保守句:

> 參與 AntPlay 後台 API / 商戶控制面開發維護，範圍包含 admin / merchant auth、商戶白名單、Game API 白名單同步、超級代理、玩家 / 投注 / request log / 報表查詢、RTP / 暗池 / 活動風控監控，以及 RabbitMQ request log / 風控通知與 Quartz job 維護。

若篇幅有限，可併入現職的「後台 control plane / risk ops / async data processing」一段，不需要單獨列成最大主成果。

## Suggested Next

`antplay-slot-admin-api` 的 Career Track 已能保守補履歷，Flow Track Step 1 / Step 2 也已於 2026-05-28 完成。下一步若要繼續本 repo，應做第一條代表 flow `request-log-rabbitmq-admin-consumer Step 3`，深掃 RequestLog RabbitMQ consumer 的 message contract、duplicate check、DB insert、transaction / schema boundary 與 failure window。

```text
antplay antplay-slot-admin-api request-log-rabbitmq-admin-consumer Step 3
```
