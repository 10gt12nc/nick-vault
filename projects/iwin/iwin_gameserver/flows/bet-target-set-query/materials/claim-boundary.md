# Claim Boundary：bet-target-set-query

## 證據層級

目前結論：分層 claim。

Step 5 結論：已完成單條 flow claim gate；不直接更新正式履歷 / 自傳，不新增獨立 gameserver 打碼履歷 bullet。

已確認 Nick / `10gt12nc` direct evidence：

- `6c99dd3`：新增 coupon 打碼目標 command / reason。
- `30a9fcb`：修正 coupon 設定的 `betCnt`。

Step 5 不直接更新正式履歷 / 自傳。coupon 打碼入口只作既有 coupon / gameserver supporting evidence，正式履歷仍以 project-level consolidation 為準。

## Step 5 Claim 分層

| 範圍 | 證據層級 | 可用方式 |
| --- | --- | --- |
| `SET_BET_TARGET_COUPON`、`SpinBetTargetConst.COUPON`、coupon `betCnt` 修正 | 真實開發過 + code-backed | 可說 Nick / `10gt12nc` 參與 coupon 打碼目標入口開發 / 修正 |
| `SpinNeedData` rule state、一般投注扣減、`log_spin_bet` audit | 專案存在 / code-backed | 可作正式面試素材，不寫成 Nick 主導 |
| `SET_BET_TARGET` BI 人工設定與 app_bi caller | 專案存在 / code-backed | 可說有讀過操作面與 center_http 關聯，不作 direct development claim |
| `QUERY_BET_TARGET` payment / app_bi 查詢 | 專案存在 / code-backed | 可說理解提款檢查 / 後台查詢關聯，不作 payment / BI owner |
| idempotency / offline fallback / persistence / incident | 待確認 | 只能作 owner improvement / 待補 evidence |

## 可說

- 我追過 iwin_gameserver 的打碼目標設定 / 查詢 flow。
- 我能說明 `SET_BET_TARGET`、`SET_BET_TARGET_COUPON`、`QUERY_BET_TARGET`。
- 我能說明 `betTarget` / `betTotal`、投注累加、提款限制與 audit log 的關係。
- coupon 打碼目標入口有 direct commit evidence。
- 可用正式面試 case 講 money rule correctness、state transition、source of truth / audit source 分離、idempotency 風險與 reconciliation 思路。

## 可作面試素材，但要保守

- 可以談 money rule correctness。
- 可以談重複設定、漏扣減、log 缺失、offline player 的 failure window。
- 可以談 `log_spin_bet` 是 audit source，不是 rule source。

## 不可說

- 不得寫成 Nick 主導完整提款打碼系統。
- Nick 設計完整 wagering rule engine。
- Nick 負責 app_bi / payment / game_api 全部上游 contract。
- Nick 解決過打碼錯誤 production incident。
- 本 Step 可直接更新 `05` / `08`。
- Nick 負責 payment / app_bi 的打碼查詢或人工設定。

## 後續若要升級需補 evidence

- 是否有 ticket / MR / production issue。
- `commExt.spinNeeds` 的 persistence / flush path。
- offline player 行為。
- 重複 request 防重設計是否存在。
- project-level final consolidation 是否決定把 coupon 打碼入口寫入 05 / 08。

## 下一步

```text
iwin third_games_api gsc-transfer-bet-settle-rollback Step 5
```
