# Todo

## 已完成

- 已將外層舊整理資料歸入 `archive/` 參考區，之後可由 Nick 人工審查是否刪除。
- 從待刪區重新建立 `senior-owner-playbook/`。
- 建立新入口、維護規則、flow inventory、學習路線、flow 模板、面試案例與唯一履歷自傳。

## 下一步

### 1. 做第一條完整 flow

建議第一條：

```text
payment-provider-callback
```

原因：

- 金流 callback 是 Senior 面試高價值題。
- 涉及簽章、狀態機、冪等、MQ、副作用、補償、對帳。
- 可轉成履歷與面試案例，但要保持保守。

### 2. 每次只做一條

順序：

1. `payment-provider-callback`
2. `transfer-wallet-transfer-in-out`
3. `bet-settlement`
4. `settled-bets-kafka`
5. `observability-pipeline`

### 3. 每條完成後更新

- `01-senior-owner-flow-inventory.md`
- `04-interview-casebook.md`
- `05-resume-master-zh.md`

但不要把履歷寫太滿。只有完成 flow 並補 evidence 後，才升級履歷說法。

## 下一個 prompt

```text
先只做一條 flow：payment-provider-callback。

請深掃 nick-vault/archive 與 /Users/nick/Git/iwin/payment。
只能動 nick-vault。
不要複製舊文，請重寫成新的完整學習包。
不要腦補，沒有 evidence 就寫待確認。
請依 senior-owner-playbook/03-flow-learning-package-template.md 產出。
```

## Senior 面試最低門檻

要開始比較有把握投 10 萬以上 Senior / Platform Backend 職缺，至少先完成：

1. `payment-provider-callback`
2. `transfer-wallet-transfer-in-out`
3. `settled-bets-kafka`

每條都要有：

- `flow.md`
- `evidence.md`
- 3 分鐘面試說法
- 5 個追問
- 履歷保守 bullet
- claim boundary

詳細檢查看：

```text
senior-owner-playbook/11-senior-interview-readiness.md
```

不同職缺定位怎麼準備，看：

```text
senior-owner-playbook/12-role-target-readiness-matrix.md
```

本地專案能力地圖與外部案例標注規則看：

```text
senior-owner-playbook/13-code-capability-map.md
projects/CONVENTIONS.md
```
