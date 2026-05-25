# iwin_gameserver Career / Interview Boundary

更新時間：2026-05-22
證據層級：部分真實開發過 + code-backed；center-http 上分 / 下分仍為分析素材 / learning-only

## Project-level 結論

`iwin_gameserver` 可以保守放進正式履歷 master 與投遞用自傳，但範圍必須限定在本輪已掃到直接 evidence 的第三方遊戲 provider 投派整合。

原因：

- Nick / `10gt12nc` 在 Antplay / GSC / PG gameserver 投派整合、money job、`PlayerData` 餘額異動 hook、`GamePlayer` log dispatch 與 log reel path 有 direct commits。
- 這能支撐「參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接」。
- 仍不能把 `iwin_gameserver` 寫成 Nick 主導完整遊戲伺服器、完整 wallet owner、完整 provider owner 或已建立完整 idempotency / reconciliation。

## 已完成 flow

| Flow | Step 狀態 | 可用方式 | 正式履歷判斷 |
| --- | --- | --- | --- |
| `third-party-transfer-in-out` | Step 5 已完成；project consolidation 已升級 | 有直接開發 evidence 的面試案例：wallet correctness、idempotency、failure window、reconciliation、observability | 可併入 project-level 第三方 provider 投派整合保守 claim |
| `center-http-deposit-withdraw` | Step 5 已完成 | 正式面試 case：center_http 上分 / 下分、payment / game_api order boundary、wallet mutation、timeout retry、`billNos` idempotency | 不放正式履歷 / 自傳；維持 interview-only |
| `game-spin-settlement-log-reel` | Step 5 已完成 | 正式面試 case：一般 slot spin、center wallet sync、`log_reel`、version guard、async log failure window | 一般 slot spin 不放履歷；provider log reel / 投派整合回填既有 project claim |
| `bet-target-set-query` | Step 5 已完成 | 正式面試 case：打碼目標設定 / 查詢、投注扣減、`log_spin_bet` audit、coupon direct evidence、idempotency / offline / reconciliation 追問 | 不新增獨立 gameserver 打碼履歷 bullet；coupon 打碼入口可作 coupon flow supporting evidence |

## 可安全使用的 project 語氣

可說：

- 「我參與過 iwin gameserver 第三方遊戲 provider 投派整合與 log projection 相關開發，範圍包含 Antplay / GSC / PG 類 bet / settle / refund / transfer-in-out flow。」
- 「我會把 gameserver flow 拆成入口、wallet mutation、log / report projection、reconciliation 幾層來看。」
- 「這類 flow 的 owner 風險是防重、response 語意、retry / compensation 與 observability。」

不可說：

- 「我主導 iwin_gameserver。」
- 「我負責完整遊戲錢包或全部第三方遊戲整合。」
- 「我解決過重複扣款 / 派彩 production incident。」
- 「我建立完整 idempotency / outbox / reconciliation 架構。」
- 「我改善錯帳率或效能 X%。」

## 已升級與仍需 evidence

已升級：

- 第三方 provider 投派整合：已由 Nick / `10gt12nc` commits 支撐，採「參與 / 開發 / 維護」口徑。

仍需補 evidence 才能升級：

- `center-http-deposit-withdraw` 的 direct development claim。
- 完整 gameserver wallet owner、dbproxy owner、idempotency / reconciliation owner。
- production incident / 改善比例。

## 下一步建議

只推薦一件事：

```text
iwin third_games_api gsc-transfer-bet-settle-rollback Step 5
```

原因：

- `center-http-deposit-withdraw` Step 5 已完成，claim gate 結論為 code-backed interview-only。
- `game-spin-settlement-log-reel` Step 5 已完成；一般 Game40 spin 維持 interview-only，provider log reel 支撐既有 project claim。
- `bet-target-set-query` Step 5 已完成；coupon 打碼入口是真實開發過 + code-backed，完整打碼 flow 維持 code-backed 面試素材。
- Career Track 的 rolling / scoped consolidation 已完成。
- 後續 third_games_api 本批代表 flows 已完成到 Step 5；目前沒有預設下一步。

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：第三方 provider 投派整合與 gameserver 錢包 / 投注流水串接，限 Antplay / GSC / PG 類 flow 與 log projection。
- 可面試講：third-party transfer in/out 可用「有直接開發 evidence」語氣；center_http 上分 / 下分已完成 Step 5，仍用 code-backed / 分析過語氣；game-spin-settlement-log-reel 已是 Step 5 正式面試素材，但一般 Game40 spin 不作 direct development claim；bet-target-set-query 已是 Step 5 money rule 素材，coupon 打碼入口有 direct evidence。
- 不可誇大：不得寫成 Nick 主導 gameserver、完整 wallet owner、完整第三方遊戲整合 owner、完整上分 / 下分 owner或解決 duplicate callback production incident。
