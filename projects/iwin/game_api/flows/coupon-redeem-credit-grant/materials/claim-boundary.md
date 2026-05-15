# coupon-redeem-credit-grant claim boundary

更新時間：2026-05-15
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Step 4 面試邊界補充

Step 4 已轉成保守面試案例，但不改變 claim 層級：

| Claim | 可用層級 | 說明 |
| --- | --- | --- |
| 可用於 Senior / Owner 面試討論 | 分析素材 / learning-only | Step 4 已整理 30 秒 / 2 分鐘 / 5 分鐘版本 |
| `game_api` coupon redeem flow 存在 | 專案存在 / code-backed | 已由 controller / service / DAO / GM command 確認 |
| `iwin_gameserver` 有下游 deposit / bet target handler | 專案存在 / code-backed | 已掃 `HttpService`、`HttpNewBill`、`NewBillJob`、`PlayerData` |
| `main` 有 check-then-insert race 風險 | 專案存在 / code-backed + analysis | DB dump 顯示 coupon user index 不是 unique key |
| Redis lock 完全解決雙領 | 不可主張 | `origin/k3s` 只是 mitigation，production deploy 與 lock semantics 待確認 |
| Nick 實作 / 主導 / 修復此 flow | 待確認 | 需要 MR / ticket / commit / 本人確認 |

## 可以主張

- 這是一條 code-backed 的 iwin `game_api` flow。
- Flow 涉及 coupon 兌換、玩家登入 token、Redis、MySQL、GM command、玩家錢包上分與打碼要求。
- `game_api` 會呼叫 `GM_CMD_DEPOSIT` 與 `SET_BET_TARGET_COUPON`。
- `iwin_gameserver` 有對應 handler，並會修改玩家金額與打碼資料。
- `origin/main` 目前可看到 check-then-insert race 風險。
- `origin/k3s` 有 coupon Redis lock 修正，可作為防重複提交 evidence，但 production 是否已部署待確認。
- DB dump 顯示 `coupon_record(coupon_code, log_user_id)` 是普通 index，不是 unique key。

## 可以在面試中保守說

- 「我分析過優惠券兌換上分 flow，重點是跨 API、DB、Redis、GM command 的一致性與 idempotency。」
- 「我會從 source of truth、transaction boundary、failure window、reconciliation 四個角度拆。」
- 「這類 flow 不適合只靠本地 transaction，要補 DB unique、下游 idempotency key、狀態機與對帳。」
- 「Redis lock 可以降低重複提交，但 money correctness 的底線仍應在 DB unique 和下游 idempotency。」

## 目前不能主張

- Nick 真實開發過這條 flow。
- Nick 主導 coupon 兌換功能。
- Nick 修復 coupon 雙領。
- Nick 設計 Redis lock。
- Nick 負責 `game_api` 或 `iwin_gameserver` owner。
- 有任何量化改善，例如錯帳率、重複兌換率、查帳時間下降。
- production 已部署 `origin/k3s` lock。

## Step 4 可用 / 禁止說法

可用：

- 「我分析過 coupon redeem 的跨系統一致性風險。」
- 「這條 flow 的 OK 語意需要分清楚：wallet applied、bet target set、本地 record written 不一定同時完成。」
- 「如果我是 owner，我會優先把 idempotency 底線放到 DB unique 和下游 deterministic bill no。」

禁止：

- 「我負責 coupon redeem。」
- 「我設計 coupon 分散式鎖。」
- 「我解決 duplicate redeem production incident。」
- 「這條 flow 已完整防雙領。」

## 需要補的 evidence

要升級成 `真實開發過`，至少需要其中一種：

- Nick 本人 MR / PR / commit。
- Nick ticket / issue / 任務描述。
- Nick 本人確認「我做過這條 flow 的某一段」。
- production incident / hotfix evidence 且能對到 Nick 參與。
- code review / 設計討論紀錄。

要升級成正式履歷素材，還需要：

- 明確標出 Nick 做的是設計、實作、修 bug、排查、補償、對帳，還是只做 code review / 分析。
- 確認哪些 branch / commit 有部署到 production。
- 確認是否有可公開、不洩漏內部資訊的成果描述。

## 正式履歷暫不更新

目前只保留在 flow / interview learning package。Step 5 前不更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`
