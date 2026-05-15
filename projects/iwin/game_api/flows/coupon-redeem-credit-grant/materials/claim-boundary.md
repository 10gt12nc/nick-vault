# coupon-redeem-credit-grant claim boundary

更新時間：2026-05-15
證據層級：專案存在 / code-backed；Nick 貢獻待確認

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

## 目前不能主張

- Nick 真實開發過這條 flow。
- Nick 主導 coupon 兌換功能。
- Nick 修復 coupon 雙領。
- Nick 設計 Redis lock。
- Nick 負責 `game_api` 或 `iwin_gameserver` owner。
- 有任何量化改善，例如錯帳率、重複兌換率、查帳時間下降。
- production 已部署 `origin/k3s` lock。

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
