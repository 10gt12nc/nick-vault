# iwin_gameserver Career / Interview Boundary

更新時間：2026-05-19
證據層級：專案存在 / code-backed；分析素材 / learning-only；Nick 貢獻依三層 claim gate 判斷

## Project-level 結論

`iwin_gameserver` 可以作為 Senior Java Backend / Platform Backend 面試中的 money flow 分析素材，但目前不建議直接放進正式履歷 master 或投遞用自傳。

原因：

- 已有 code-backed evidence 能支持「系統存在、flow 存在、風險點存在」。
- 尚未有 Nick 本人 MR / ticket / commit / production issue / 本人確認。
- 因此不能把 `iwin_gameserver` 寫成 Nick 主導、實作、改善或擔任 owner 的成果。

## 已完成 flow

| Flow | Step 狀態 | 可用方式 | 正式履歷判斷 |
| --- | --- | --- | --- |
| `third-party-transfer-in-out` | Step 5 已完成 | 面試分析素材：wallet correctness、idempotency、failure window、reconciliation、observability | 暫不放正式履歷 / 自傳 |
| `center-http-deposit-withdraw` | Step 3 已完成 | 面試分析素材：center_http 上分 / 下分、payment / game_api order boundary、wallet mutation、timeout retry、`billNos` idempotency | 暫不放正式履歷 / 自傳；Step 4 後再整理正式 case |

## 可安全使用的 project 語氣

可說：

- 「我分析過 iwin gameserver 的 money flow，重點看玩家餘額、投注 / 派彩 / 退款、log projection 與對帳風險。」
- 「我會把 gameserver flow 拆成入口、wallet mutation、log / report projection、reconciliation 幾層來看。」
- 「這類 flow 的 owner 風險是防重、response 語意、retry / compensation 與 observability。」

不可說：

- 「我主導 iwin_gameserver。」
- 「我負責遊戲錢包或第三方遊戲整合。」
- 「我解決過重複扣款 / 派彩 production incident。」
- 「我建立完整 idempotency / outbox / reconciliation 架構。」
- 「我改善錯帳率或效能 X%。」

## 若要升級成履歷素材需要的 evidence

至少需要其中一類：

- Nick 本人確認實際參與範圍。
- MR / commit / ticket 能對上 Nick 與此 flow 的具體修改。
- production issue / incident 能證明 Nick 參與排查或修復。
- 可公開或可整理的設計紀錄，能證明 Nick 的 owner decision。

升級後仍需分層寫法：

- 只做排查：寫成「分析 / 排查 / 釐清」。
- 有修改 code：寫成「實作 / 修正」但限定 scope。
- 有 owner decision：才可寫「制定策略 / 推動改善」，並避免誇大成全系統 owner。

## 下一步建議

只推薦一件事：

```text
iwin iwin_gameserver center-http-deposit-withdraw Step 4
```

原因：

- `center-http-deposit-withdraw` Step 3 已完成，已有 code-backed 主學習包。
- 但 `iwin_gameserver` 尚未做 project-level contribution claim consolidation。
- 下一步要先掃 Nick / `10gt12nc` commits、branches、重要 diff 與既有 flow evidence，確認哪些是「真實開發過」、哪些只是「code-backed / 分析過」。
- 做完 consolidation 後，再決定是否回到 `center-http-deposit-withdraw Step 4` 或更新履歷 / 自傳。

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：目前不放正式履歷；尚未補到 Nick 本人對 `third-party-transfer-in-out` 或 `center-http-deposit-withdraw` 的 MR / ticket / commit / production issue / 本人確認。
- 可面試講：code-backed / 分析過。可用 gameserver wallet transfer flow 與 center_http 上分 / 下分說明玩家餘額、DB proxy、log writer、failure window、idempotency 與 reconciliation。
- 不可誇大：不得寫成 Nick 主導 gameserver、完整 wallet owner、獨立完成第三方遊戲整合、完整上分 / 下分 owner 或解決 duplicate callback production incident。
