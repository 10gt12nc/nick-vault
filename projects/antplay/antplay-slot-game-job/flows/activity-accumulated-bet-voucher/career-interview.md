# activity-accumulated-bet-voucher Career / Interview

日期: 2026-05-25

## Evidence Level

- 證據層級: 專案存在 / code-backed + Nick merge evidence。
- Nick evidence: `62fa93f` merge `feature/accumate_bet` into `master`，merge message 指向累計下注送零元旋轉活動修改。
- Direct implementation evidence: current `ActivityAccumateBetConsumerService` 主要 blame 為 Gill，後續有 Arnold / Eliot 修改；Nick 不宜主張主導開發。
- 完成狀態: Step 3 深掃完成，Step 4 面試 case 待整理。
- 本文件是 flow-level 素材，不是 project-level final consolidation。正式履歷仍以 `contribution-claim-consolidation.md` 與 rolling resume package 為準。

## 履歷保守 Bullet

不建議單獨放成正式主 bullet。

若要放進 project-level 素材池，只能保守寫:

- 接觸 / 分析 AntPlay slot job 的活動累計投注 reward flow，理解 Kafka settlement event、Redis 累計投注、BetVoucher 發放與防重邊界。

更短版本:

- 以 code-backed 方式梳理活動累計投注 voucher flow，聚焦 Redis / DB consistency 與 reward idempotency。

## 面試定位

這條 flow 面試時不要講成「我做活動發獎」。比較安全的定位是:

```text
settlement event -> activity eligibility -> Redis accumulate -> voucher issuing -> duplicate reward guard
```

主軸是 reward correctness，不是完整活動平台。

## 30 秒說法草稿

我看過 AntPlay job 裡一條活動累計投注送 voucher 的 flow。它會消費 `settled_bets`，依活動設定過濾 agent、活動時間與幣別，把同玩家的投注額累加到 Redis；達到 daily 或 period 門檻後，透過 `BetVoucherService` 發 free spin / voucher。這條 case 我會用來分析 Kafka 重送、Redis awarded count、DB voucher count 與重複發放的邊界，但我會保守說這是 code-backed 分析素材，不說是我主導開發。

## 可說

- 這條 flow 是 Kafka settlement event 之後的 reward projection。
- Daily 使用 Redis accumulate + daily awarded key 控制每日一次。
- Period 使用 Redis awarded count，再補 DB voucher count 查詢。
- `addVoucher` 成功 / Redis awarded 更新失敗、DB count 查詢失敗回 0，是主要 failure window。
- Nick 有 merge evidence，但 implementation 主要是其他人 commits，因此要保守。

## 不可誇大

- 不說主導活動累計投注功能。
- 不說完整 reward platform owner。
- 不說已確認 `BetVoucherService` 有完整 idempotency。
- 不說 Kafka / Redis / DB 已有 exactly-once 或完整 reconciliation。
- 不把 Gill / Arnold / Eliot 的 current code 寫成 Nick direct contribution。

## Step 4 應補

- 30 秒 / 90 秒 / 3 分鐘正式說法。
- STAR，但要標 code-backed，不標真實主開發。
- Senior 追問: Kafka retry、Redis / DB mismatch、Daily / Period 重複發、timezone / replay。
- 反問: reward idempotency key、補發 / 回收工具、活動設定審核。
