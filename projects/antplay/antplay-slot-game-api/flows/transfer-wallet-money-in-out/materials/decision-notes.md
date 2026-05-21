# transfer-wallet-money-in-out Decision Notes

日期: 2026-05-21

## 1. Source of Truth

建議面試時保守說:

- DB `ag_transfer_player_wallet` 應是 balance source of truth。
- Redis `antplay:transfer:Agent:{agentId}:balance` 是 hot balance cache。
- 若 Redis 和 DB 不一致，owner 決策應以 DB / transaction record 對齊，再修 Redis。

## 2. Idempotency

目前看到兩層:

1. Redis short lock: `antplay:Transfer:ReferenceLock:{transferReferenceId}`，TTL 3 秒，擋短時間重送。
2. Transaction 查詢: 查 `pt_transfer_player_wallet_transaction` 是否已有同 reference / status。

風險:

- short lock TTL 過後，仍需要 DB unique key 或 transaction query 正確。
- 只查當日 `pt_day`，跨日重送語意要確認。
- Step 3 未看到 DB unique constraint，不能宣稱完整 idempotency。

## 3. Transaction Boundary

目前主流程順序:

```text
request log initial
-> transaction SUCCESS
-> order lookup
-> wallet DB update
-> Redis increment
-> request log response
```

Owner 觀察:

- transaction 先 SUCCESS，wallet update 後發生 failure 會有狀態不一致風險。
- Redis 不是 DB transaction 的一部分，dual-write 需要 repair。
- lookup 先寫成功可幫助查單，但也可能查到尚未完成 wallet update 的交易。

## 4. Transfer-out 防負數

目前 transfer-out:

- 先從 Redis 讀 beforeBalance。
- Java 端判斷 `beforeBalance - amount >= 0`。
- 再進 DB update `balance = balance - ?`。

Owner 改善方向:

- DB conditional update: `WHERE balance >= ?`。
- 同帳號 / 幣別 row lock。
- 交易表 status 從 PENDING -> SUCCESS / FAILED。
- 異常時補 request log 與 repair task。

## 5. 面試切法

不要講成「我會上分下分 API」。要講成:

- 外部 money request 的 idempotency。
- DB / Redis consistency。
- 分表後查單與 audit。
- failure window 與 owner repair。

## 6. Step 4 Owner 改善排序

若面試官問「你會先改哪裡」，建議排序:

1. DB unique key / constraint：先確保同一 `transferReferenceId` 不會重複入帳。
2. Transaction status：由 PENDING 開始，wallet DB update 成功後才 SUCCESS，失敗寫 FAILED。
3. Conditional wallet update：transfer-out 用 `WHERE balance >= amount` 或 row lock 防負數。
4. Redis repair：把 Redis 定位成 cache，用 DB / transaction 重建或對帳。
5. Request log 補寫：audit 不阻塞 money flow，但要能補寫、告警、追查。
