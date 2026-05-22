# Decision Notes - Antplay bet / settle / rollback

## Decision 1：舊三段式 flow 先獨立分析

判斷：

- 本 Step 3 聚焦 `/antplay/bet`、`/antplay/settle`、`/antplay/rollback`。
- `/antplay/bet_settle-new` 與 `ANTPLAYTRANSFERINOUT` 只作後續演進對照。

理由：

- 舊三段式最能看清楚 bet -> settle -> rollback state transition。
- 若把新版整合式 endpoint 混進來，會模糊目前 Nick 指定 flow 的 failure window。
- production 是否切到新版仍待確認，不能直接把新版當舊版修正結果。

## Decision 2：Money boundary 歸在 gameserver

判斷：

- `third_games_api` 是 adapter，不是錢包 source of truth。
- owner 分析要追到 `iwin_gameserver` 的 `AntplaySettledJob` 與 `modifyAndGetCoinAntplay`。

理由：

- adapter 回 `ok` 不代表只有 Mongo 成功，而是下游已經可能改錢。
- retry / double pay / double refund 的安全性，最終取決於 money mutation boundary 是否 idempotent。

## Decision 3：Mongo audit 不應是唯一 idempotency guard

判斷：

- adapter 在 gameserver success 後才寫 `third_transaction_antplay`。
- `settle` / `rollback` 只查 step 1，未看到 adapter 先查 step 2 / step 3 防重。

Owner 改善方向：

- 先落 durable request / transaction state，再推進 wallet mutation。
- 或讓 gameserver wallet mutation 使用 provider + betId + action / step 做唯一防重。
- 對每一步狀態補 pending / success / failed，讓 retry 可以查到 deterministic result。

## Decision 4：原始 bet / betTime 應放穩定 transaction evidence

判斷：

- `settle` / `rollback` 目前會從 `third_log_antplay` type 3 與 `third_transaction_antplay` step 1 讀原始 bet / betTime。

風險：

- log collection 缺失、格式變動或 duplicate，都會影響後續 settle / rollback。

Owner 改善方向：

- step 1 transaction evidence 要保存原始 bet、betTime、provider request id 與處理結果。
- log collection 保持 audit / debug，用 transaction table / collection 作狀態來源。

## Decision 5：履歷先保守

判斷：

- 本 flow 可作 code-backed 面試素材。
- 本 Step 3 不更新正式履歷 / 自傳。

理由：

- `third_games_api` Antplay path 目前未掃到足夠 Nick production direct evidence。
- 下游 `iwin_gameserver` Antplay direct evidence 已在 `iwin_gameserver` claim 歸位，不反向包裝到 `third_games_api`。
