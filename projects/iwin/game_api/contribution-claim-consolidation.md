# iwin game_api Contribution Claim Consolidation

更新時間：2026-05-20
掃描等級：Level 2+ project-level contribution consolidation
狀態：已完成；2026-05-20 已重新 fetch / 重讀 / 覆核
證據層級：部分真實開發過 + code-backed；正式 claim 只採保守口徑

## 結論

`game_api` 可以升級為「部分真實開發過」，但正式履歷只放 `coupon-redeem-credit-grant` 這條已同時具備 Nick / `10gt12nc` direct commits 與 flow package 的成果。

`partner-deposit-withdraw-bill` 與 `agent-bonus-receive-transfer` 已完成 Step 5，但目前未見 Nick / `10gt12nc` direct path evidence，只保留為 code-backed 面試素材，不寫進正式履歷主成果。

補充：repo-wide 掃描也看到 Nick / `10gt12nc` 曾開發邀請好友轉盤活動相關 path，但該 flow 尚未整理成完整 flow package，因此目前只作補充 evidence，不升級成正式履歷主 bullet。

2026-05-20 重新覆核後，結論不變：

- `game_api` 與 `iwin_gameserver` 都已重新 `git fetch --all --prune`。
- 兩個 repo 的本機 `main` 都與 `origin/main` 同步，工作樹乾淨。
- 重新跑 repo-wide Nick / `10gt12nc` author log，`game_api` 仍只看到 coupon 與邀請好友轉盤兩組 direct evidence。
- `iwin_gameserver` 有更多 Nick / `10gt12nc` gameserver commits，但在本文件只採與 `game_api coupon` 下游直接相關的 `6c99dd3`、`30a9fcb`；其他 gameserver evidence 應放到 `iwin_gameserver contribution claim consolidation`，不反向擴張 `game_api` claim。
- 重讀 05 / 08 / todo / inventory 與 archive 搜尋結果後，沒有新增足以把 partner / agent bonus 升級成 Nick 真實開發過的 evidence。

## 本次重讀與掃描範圍

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `projects/iwin/game_api/README.md`
- `projects/iwin/game_api/step2-flow-comparison.md`
- `projects/iwin/game_api/flows/coupon-redeem-credit-grant/`
- `projects/iwin/game_api/flows/partner-deposit-withdraw-bill/`
- `projects/iwin/game_api/flows/agent-bonus-receive-transfer/`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/06-todo.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`
- `senior-owner-playbook/13-code-capability-map.md`
- `archive/` 內與 `game_api`、coupon、partner、agent bonus、履歷相關線索

已掃 source repo：

| Repo | local branch | local HEAD | remote HEAD | ahead / behind | 判斷 |
| --- | --- | --- | --- | --- | --- |
| `/Users/nick/Git/iwin/game_api` | `main` | `39bb6e38210bb79c6e68a6a6d818cb87986d39f0` | `origin/main` 同 HEAD | 0 / 0 | 已 fetch，主分支同步 |
| `/Users/nick/Git/iwin/iwin_gameserver` | `main` | `30a9fcb95bfda33b582deeb4e149eb06bed4afe3` | `origin/main` 同 HEAD | 0 / 0 | 已 fetch，主分支同步 |

2026-05-20 重新覆核：

- `/Users/nick/Git/iwin/game_api`：重新 fetch，`main` / `origin/main` 仍為 `39bb6e38210bb79c6e68a6a6d818cb87986d39f0`，ahead / behind `0 / 0`，工作樹乾淨。
- `/Users/nick/Git/iwin/iwin_gameserver`：重新 fetch，`main` / `origin/main` 仍為 `30a9fcb95bfda33b582deeb4e149eb06bed4afe3`，ahead / behind `0 / 0`，工作樹乾淨。
- `game_api` remote branches 仍包含 `origin/beta`、`origin/bugs/write_black_list`、`origin/coupon`、`origin/feature/RD-128`、`origin/fix-ci-deploy`、`origin/fix-performance`、`origin/k3s`、`origin/main`、`origin/test`。
- `iwin_gameserver` remote branches 仍包含 `origin/AntPlay-transferInOut`、`origin/Nick-GSC_PG`、`origin/Nick-review`、`origin/beta`、`origin/feature/RD-162`、`origin/feature/RD-188`、`origin/k3s`、`origin/main`、`origin/nick-LocalDevTest`、`origin/rate-script`。

未做：

- 未修改公司 source repo。
- 未 checkout / merge / rebase 公司 source repo。
- 未把 config、secret、internal endpoint 或敏感內容寫入 vault。
- 未掃完整 `game_api` 每個 feature 的逐行全量 history；本次聚焦 contribution claim consolidation 所需的 repo-wide Nick / `10gt12nc` commits、branches、重要 diff 與既有 flow evidence。
- 2026-05-20 重新覆核沒有做 Level 3 全量逐檔逐行；本輪目標是確認已完成 consolidation 是否仍正確。若 Nick 要把邀請好友轉盤、登入註冊、戰績查詢或其他 game_api 功能放履歷，應另開 Flow Track 深掃。

## Repo-wide Nick / `10gt12nc` evidence

### 可放履歷：真實開發過 + code-backed

#### 1. `coupon-redeem-credit-grant`

`game_api` direct commits：

- `8683e32`（2026-01-06，`10gt12nc`）：建立優惠券功能主體，涵蓋 `CouponRedeemController`、coupon service / DAO / mapper / entity、GM command name、`log_user` 查詢等 path。
- `1e96a1f`、`c2dabf7`、`d7d9ed3`、`e9d63b1`、`3d283d2`、`89b698f`（2026-01-07 ~ 2026-01-08，`10gt12nc`）：持續調整 coupon setting / redeem request / service / GM command / mapper。
- `9843b15`、`752f435`（2026-01-12，`10gt12nc`）：補 coupon record / coupon record service / mapper。
- `3921fc0`、`e52492e`、`b9254b1`、`39bb6e3`（2026-01-16 ~ 2026-01-19，`10gt12nc`）：補 method / URL whitelist、`CouponReason`、coupon redeem service 細節。

`iwin_gameserver` downstream direct commits：

- `6c99dd3`（2026-01-07，`10gt12nc`）：補 coupon 下游打碼相關 handler path，包含 `SpinBetTargetConst` 與 `HttpService`。
- `30a9fcb`（2026-01-19，`10gt12nc`）：補 coupon bet count 相關處理，path 在 `HttpService`。

保守履歷口徑：

- 參與玩家優惠券兌換上分 / 打碼要求 flow 開發，串接 `game_api` API、coupon setting / record、GM command 與 `iwin_gameserver` 下游上分 / 打碼處理。
- 可面試延伸 transaction boundary、GM command partial success、idempotency、Redis lock / DB constraint、coupon record / wallet log / bet target reconciliation。

不可誇大：

- 不寫主導完整 coupon / reward system。
- 不寫修復 production 雙領事故。
- 不寫設計 Redis distributed lock；`origin/k3s` 的 coupon 防重複提交修正為 Arnold commit。
- 不寫完整玩家端 API owner 或完整 wallet / reconciliation owner。

### 可面試講：code-backed / 分析過

#### 2. `partner-deposit-withdraw-bill`

已完成 Step 5，已確認 `PartnerController`、`PartnerServiceImpl`、`ValidatedServiceImpl`、`PartnerLogServiceImpl` 與 Mongo 分日 order log / GM command 互動。

本輪 repo-wide Nick / `10gt12nc` path-specific history 未看到 partner deposit / withdraw / bill path direct commits。

可面試講：

- partner request validation / sign / replay window。
- Mongo order status 先寫 pending、GM command 後更新 success / failure 的一致性風險。
- provider retry、internal bill number、idempotency key 與查單 / reconciliation。

不可寫正式履歷：

- 不寫 Nick 開發 Partner API 上分 / 下分。
- 不寫 partner money API owner。
- 不寫完整 wallet / reconciliation owner。

#### 3. `agent-bonus-receive-transfer`

已完成 Step 5，已確認 `AgentShareServiceImpl#transferReceive`、`bonusReceive`、Mongo `agent_money`、Redis `GameAgent:{rid}` projection、GM 上分與 `game_job` 收益計算關聯。

本輪 repo-wide Nick / `10gt12nc` path-specific history 未看到 agent bonus receive / transfer path direct commits。

可面試講：

- Mongo truth source vs Redis projection。
- API 領取 / 轉帳和 batch commission calculation 的狀態邊界。
- GM 上分、Mongo 更新、Redis projection、audit log 的 partial failure。

不可寫正式履歷：

- 不寫 Nick 開發代理佣金領取 / 轉帳。
- 不寫 commission / agent money owner。
- 不寫完整 reward / settlement owner。

### 補充 evidence：真實開發過，但尚未做 flow package

#### 4. 邀請好友轉盤活動

repo-wide Nick / `10gt12nc` commits：

- `4e75d3b`（2024-07-01，`10gt12nc`）：邀請好友轉盤活動，包含 `InvitationFreeSpinsController`、相關 DAO / service / mapper / response / interceptor / entity。
- `71d9c73`（2024-07-03，`10gt12nc`）：邀請好友轉盤活動白名單，包含 `SafetyInterceptor` 相關調整。

判斷：

- 這是 `game_api` repo 內的 direct development evidence。
- 但目前尚未做 Step 1 / Step 2 ranking 內的完整 flow package，也未完成 Step 5 claim gate。
- 現階段只作補充 evidence，不放入正式履歷主 bullet；若 Nick 之後要加活動系統經驗，可另開 flow 深挖。

## 三層 Claim 結論

### 可放履歷：真實開發過

- `coupon-redeem-credit-grant`：可用「參與 / 開發」口徑。

建議正式履歷句：

```text
參與玩家優惠券兌換上分 / 打碼要求 flow 開發，串接 game_api API、coupon setting / record、GM command 與 iwin_gameserver 下游上分 / 打碼處理；可面試延伸 transaction boundary、idempotency、partial success 與 reconciliation，但不寫主導完整活動獎勵系統或修復 production 雙領事故。
```

### 可面試講：code-backed / 分析過

- `partner-deposit-withdraw-bill`
- `agent-bonus-receive-transfer`
- 邀請好友轉盤活動：有 direct commits，但未整理成完整 flow package，現階段只作補充。

### 不可誇大

- 不說 Nick 主導 `game_api`。
- 不說 Nick 負責完整玩家端 API、完整 Partner API、完整代理佣金或 reward system。
- 不說 Nick 設計 coupon Redis lock 或修復 production 雙領事故。
- 不說 Nick 是完整 wallet / reconciliation owner。
- 不寫改善百分比、正式架構師、完整 system owner。

## 對履歷 / 自傳的影響

本次會同步更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

更新方式：

- 保留既有 coupon 句子。
- 把 evidence 狀態從單條 `coupon Step 5` 升級為 `game_api contribution consolidation`。
- 明確補上 partner / agent bonus 只作面試素材，邀請好友轉盤只作補充 evidence。

不新增：

- 完整 `game_api` owner claim。
- Partner API / agent bonus formal resume bullet。
- 量化改善或事故修復 claim。

## 下一步建議

只推薦一件事：

```text
iwin iwin_gameserver contribution claim consolidation
```

原因：

- `game_api` 本批代表 flow 與 project-level contribution claim 已收斂。
- `payment`、`game_job`、`app_bi`、`bi_share` 的 contribution consolidation 也已收斂。
- 若近期目標是先把履歷 / 面試 claim 風險收斂，`iwin_gameserver` 目前比重做 `game_api` 更值得做 rolling / scoped consolidation；後續 Flow Track 仍可回 `center-http-deposit-withdraw Step 4`。
