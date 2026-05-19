# agent-bonus-receive-transfer claim boundary

更新時間：2026-05-19
Step：4
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## Step 4 判定

結論：可作 code-backed 面試素材，暫不更新正式履歷 / 自傳。

原因：

- Flow 本身已由 Controller、Service、Mongo model、Redis key、GM command 與 `game_job` 上游 job code 確認。
- path-specific history 未看到 Nick / `10gt12nc` 直接修改 `AgentShareServiceImpl`、`ShareCommonService`、Partner share endpoints、AgentBonusWash / Settlement 或相關 model。
- Nick 尚未本人確認這條代理佣金 flow 是他做過或維護過。
- Step 4 已整理成一句話、30 秒、2 分鐘、5 分鐘、STAR、Senior / Platform / Owner 口吻與常見追問。

## 可以主張

- `game_api` 有代理佣金領取 / 轉帳 flow。
- Flow 涉及 Mongo `agent_money`、Redis `GameAgent` projection、GM 上分、轉帳 / 領取 audit log。
- `game_job` 有佣金計算 / 結算 job，會把每日佣金累加到 `agent_money`。
- 可以從 idempotency、transaction boundary、failure window、reconciliation、observability 角度分析。

## 可以在面試中保守說

- 「我分析過一條代理佣金領取 / 轉帳 flow，重點是可領餘額、Redis projection 與 GM wallet side effect 的一致性。」
- 「我會先把 Mongo `agent_money` 定成 source of truth，Redis 當 projection，再補 operation id 與 reconciliation。」
- 「短時間 lock 只能防連點，不是 money flow idempotency。」
- 「這條目前是 code-backed 分析案例，我不會把它包裝成我主導開發。」

## 目前不能主張

- Nick 開發此代理佣金 flow。
- Nick 主導代理分潤 / 佣金結算 / wallet reconciliation。
- Nick 修復佣金重領、錯帳或 production incident。
- Nick 設計 `origin/k3s` 的 token auth 修正。
- 此 flow 已可寫入正式履歷。

## 待補 evidence

- Nick 本人確認是否做過此 flow。
- `10gt12nc` 是否在其他 branch / ticket / MR 修改過代理佣金 API 或 job。
- production issue / support ticket。
- `agent_money` 實際 Mongo index。
- gameserver `GM_CMD_DEPOSIT` 對 billNos 的 duplicate handling。

## 下一步

```text
iwin game_api agent-bonus-receive-transfer Step 5
```
