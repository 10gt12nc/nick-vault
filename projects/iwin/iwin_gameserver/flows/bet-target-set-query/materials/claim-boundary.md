# Claim Boundary：bet-target-set-query

## 證據層級

目前結論：`部分真實開發過 + code-backed`

已確認 Nick / `10gt12nc` direct evidence：

- `6c99dd3`：新增 coupon 打碼目標 command / reason。
- `30a9fcb`：修正 coupon 設定的 `betCnt`。

Step 3 不直接更新正式履歷 / 自傳。

## 可說

- 我追過 iwin_gameserver 的打碼目標設定 / 查詢 flow。
- 我能說明 `SET_BET_TARGET`、`SET_BET_TARGET_COUPON`、`QUERY_BET_TARGET`。
- 我能說明 `betTarget` / `betTotal`、投注累加、提款限制與 audit log 的關係。
- coupon 打碼目標入口有 direct commit evidence。

## 可作面試素材，但要保守

- 可以談 money rule correctness。
- 可以談重複設定、漏扣減、log 缺失、offline player 的 failure window。
- 可以談 `log_spin_bet` 是 audit source，不是 rule source。

## 不可說

- Nick 主導完整提款打碼系統。
- Nick 設計完整 wagering rule engine。
- Nick 負責 app_bi / payment / game_api 全部上游 contract。
- Nick 解決過打碼錯誤 production incident。
- 本 Step 可直接更新 `05` / `08`。

## Step 5 前需要補的 evidence

- app_bi / game_api / payment 的實際 caller。
- 是否有 ticket / MR / production issue。
- `commExt.spinNeeds` 的 persistence / flush path。
- offline player 行為。
- 重複 request 防重設計是否存在。

## 下一步

```text
iwin iwin_gameserver bet-target-set-query Step 4
```
