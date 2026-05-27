# ugsoft-admin-api Contribution Claim Consolidation

日期: 2026-05-20

## 結論

`ugsoft-admin-api` 可以列為 Nick 真實開發過的後台 / control plane repo。Nick / `10gt12nc` 在 `origin/main` 可見大量 direct commits，涵蓋後台登入 / JWT / RBAC、商戶 / provider 白名單、超級代理、報表查詢、風控監控、RabbitMQ request log / bet record、Quartz / report job 等功能。

履歷可以保守寫:

> 參與 UGSoft 後台 API / control plane 開發與維護，處理登入權限、商戶 / provider 白名單、報表查詢、風控監控、RabbitMQ request / bet record 非同步資料處理與 Quartz / report job 維護。

不要寫:

- 不得寫成主導完整 UGSoft 平台。
- 不得寫成主導完整 provider gateway / connector。
- 完整 wallet / money flow owner。
- 完整 RabbitMQ architecture owner。
- 不得寫改善 X% 或完整事故 owner。

## Evidence Summary

| 類別 | 判斷 | Evidence |
| --- | --- | --- |
| 直接開發 | 真實開發過 | `origin/main` 自 2024 起約 190 筆 Nick / `10gt12nc` commits |
| 後台權限 / login | 真實開發過 | `feat(#171) slot-admin api`、`fix(#383) JWT / 401`、`feat(#423) 商戶登入 api`、`feat(#680) auth_token / kick user` |
| 商戶 / provider 白名單 | 真實開發過 | `feat(#421) 商戶白名單`、`feat(#682) slot-game-api API 白名單`、`feat: 平台商白名單IP表` |
| 超級代理 / 報表 | 真實開發過 | `feat(#444) 超級代理`、`feat(#509) 超級商戶`、每日報表 / 活動費 / hourly report 相關 commits |
| 風控 / 監控 | 真實開發過 | `feat(#659) 商戶遊戲風控概況`、RTP exceed / monitor alert / voucher monitor 相關 commits |
| RabbitMQ / async | 真實開發過 | `feat(#774) RequestLog 改丟 rabbitmq 非同步執行`、`feat(#775) rabbitmq 收風控通知`、`feat: BetRecord MQ 入庫` |
| connector / provider gateway | 待確認 | admin API 可見白名單 / provider config，完整 gateway 要另掃 `ugsoft-connector-api` |

## Source Scan Record

來源 repo:

```text
/Users/nick/Git/ugsoft/ugsoft-admin-api
```

掃描方式:

- 已執行 `git fetch --all --prune` 更新 remote refs。
- local branch: `main`
- local HEAD: `0cc62e0e1a040e69b1650079d9ecfe92dd64380d`
- `origin/main`: `b1b83f64ffc971cc838ef935867a5a2234e3d201`
- ahead / behind: `0 / 42`，本機工作樹落後 `origin/main` 42 commits；本次 code 判斷以 fetched `origin/main` object、`origin/main` log 與 remote branch log 為準。
- source repo 工作樹乾淨。
- source README 含內部位址，已排除，不寫入 vault。

2026-05-26 code / KB recheck:

- 重新 fetch 成功，`origin/main` 已前進；本檔保留原本 contribution claim，不因新 remote refs 自動擴張履歷主張。
- 目前結論仍成立：可寫後台 / control plane、RabbitMQ / report job / 白名單 / 權限等真實開發經驗；不可寫完整 UGSoft 平台、完整 provider gateway 或完整 wallet owner。

本次掃描範圍:

- `git log origin/main --author='10gt12nc|Nick|nick'`
- `git log --all --author='10gt12nc|Nick|nick'`
- `git show --stat --name-only` 抽查 key commits
- `git diff --stat 1df26c0..origin/main`
- `src/main/java/com/ps/domain/admin/controller`
- `src/main/java/com/ps/domain/admin/rabbitMq`
- `src/main/java/com/ps/domain/admin/service`
- `src/main/java/com/ps/domain/manage/service`
- `src/main/resources/mapper`
- `sql/`
- vault 既有 `projects/`、`senior-owner-playbook/` 內 ugsoft / resume 關鍵字；`archive/` 已清空，不再作必要來源

未完成:

- 未做 `ugsoft-admin-api` 全量 Step 1 / Step 2。
- 未逐條 flow 建立 `flow.md` / `career-interview.md`。
- 未掃 `ugsoft-connector-api` 的 provider gateway 實作。

## Important Commit Evidence

### 後台 API / Login / Auth / RBAC

- `aac4858` / `b97fda4` / `1df26c0`：`feat(#171): slot-admin api 開發`，早期 admin API、登入登出、agent list、detail、swagger / response 調整。
- `cac1f77`：`fix(#383): jwt payload 塞參數 / API JWT過期返回 401`。
- `8c22f79` / `c761cb6`：`feat(#423): 商戶登入api`。
- `454cdcc`：`feat(#589): RoleTypeFilter 技術客服`。
- `629f1bd` / `6ed6dc4` / `ab78cf4` / `2f0616a`：商戶 / 全站使用者踢線、auth token controller / service / mapper。

### 商戶 / Provider 白名單與控制面

- `ed79070`：`feat(#421): 商戶白名單`，涉及 `SecurityConfig`、`MerchantController`、`WhiteListFilter`、repository / service / entity / VO。
- `f90a1eb`：`feat(#682): slot-game-api API白名单功能`，新增 `GameApiWhiteIpController`、service、mapper、Redis key 與操作 log。
- `2fb2ce5`：`feat: 平台商白名單IP表`，新增 `ProviderWhiteIpController`、entity、mapper、service 與 XML mapper。

### 超級代理 / 報表 / 後台查詢

- `2c9f48d`：`feat(#444): 超級代理`，跨 `AgentDailyController`、`AuthController`、`BetRecordController`、`RequestLogController`、`RoleFilter`、`AgentService`、player / report path。
- `2438a4e` / `565cbbd`：`feat(#509): 超級商戶`。
- `ed4f153` / `f338fc2` / `6795199`：每日報表新增活動費，包含 controller、service、mapper、job。
- `6e69f17` / `7fe3328` / `fb675d0`：`UpdateAgentDailyTable` / `UpdateAgentHourlyTable` job 修正。

### 風控 / 監控

- `57b101f`：`feat(#659): 商戶遊戲風控概況`，涉及 Redis scan、monitor controller、RTP exceed mapper / service、Quartz monitor jobs。
- `1c2af07` / `c1e6762` / `686bc71`：RTP exceed 檢查 / notify 修正。
- `250811a` / `44d1f84` / `05156ef` / `8be68ca`：RabbitMQ 收風控通知與 queue config。

### RabbitMQ / Request Log / Bet Record

- `814b024`：`feat(#774): RequestLog 改丟 rabbitmq 非同步執行`，涉及 `RequestLogListener`、`RequestLogMapper`、`RequestLog` entity 與 mapper XML。
- `f3a9d72` / `5f838f8` / `fa86544`：request log RabbitMQ 後續修正。
- `98ad763`：`feat: BetRecord MQ 入庫 01`，新增 RabbitMQ config、`ConnectBetRecordConsumerService`、payload/message/listener 與 bet record repository path。
- `821bc2e` / `c99a325`：BetRecord / requestLog MQ 後續修正，包含 currency default 與 duplicate / mapper 調整。

## Resume Claim

可放履歷:

- 參與 UGSoft 後台 API / control plane 開發與維護，處理 login / JWT / RBAC、商戶 / provider 白名單、超級代理與報表查詢功能。
- 參與 RabbitMQ request log / bet record 非同步資料處理與風控通知 consumer 維護，處理資料入庫、重複檢查、時間分區與 job / report 查詢的資料一致性。

可面試講:

- 後台權限與 agent hierarchy 如何影響查詢範圍。
- 白名單資料如何同步 DB / Redis / runtime access control。
- request log 從同步寫入改為 RabbitMQ consumer 後，會出現哪些 idempotency、lost message、duplicate、partition key 與 observability 風險。
- bet record MQ 入庫為什麼要檢查 provider bet id / pt_day / agent_id，amount normalization 與 quota async publish 失敗如何界定。
- 報表 / job 重跑時，delete + insert、時間窗口、分表與 partial failure 怎麼處理。

不可誇大:

- 不說主導 UGSoft 平台或完整後台重構。
- 不說完整設計 RabbitMQ event architecture。
- 不說完整 provider connector / wallet owner。
- 不說改善多少性能或事故數字。

## Flow Track 狀態

2026-05-27 已完成 `ugsoft-admin-api Step 1`，候選 flow 見 [step1-candidate-flows.md](step1-candidate-flows.md)。

2026-05-27 已完成 `ugsoft-admin-api Step 2`，本批代表 flows 見 [step2-flow-comparison.md](step2-flow-comparison.md)。目前選定:

1. `connect-bet-record-mq-ingestion`
2. `request-log-rabbitmq-admin-consumer`
3. `game-api-provider-white-ip-control-plane`

本檔是 Career Track 的 project-level rolling consolidation；它可以支撐保守履歷 claim，但不代表 `ugsoft-admin-api` Flow Track 已完整。Step 2 完成後，第一條代表 flow `connect-bet-record-mq-ingestion Step 5` 已建立；可回填本檔 RabbitMQ / BetRecord async evidence，但本檔仍維持 rolling，不升級 final。第二條代表 flow `request-log-rabbitmq-admin-consumer Step 5` 已建立 RequestLog RabbitMQ 非同步入庫 learning package、正式面試稿與 claim gate；可回填本檔 RabbitMQ / request log async supporting evidence，但仍不直接更新 `05 / 08`。若繼續本 project，下一步回到第三條代表 flow `game-api-provider-white-ip-control-plane Step 3`。
