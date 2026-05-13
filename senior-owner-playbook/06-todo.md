# Todo

## 已完成

- 已將外層舊整理資料歸入 `archive/` 參考區，之後可由 Nick 人工審查是否刪除。
- 從待刪區重新建立 `senior-owner-playbook/`。
- 建立新入口、維護規則、flow inventory、學習路線、flow 模板、面試案例與唯一履歷自傳。
- 已建立全 vault 共用自動維護規則：每次 project / flow / Step 任務前自動重讀 KB、既有文件與相關 code 最新狀態，完成後自動判斷是否維護 README、Step 文件、evidence、claim boundary、todo 或共用 KB。
- 已補上舊文件自動排查規則：AI 必須自己檢查既有 Step / flow 是否過舊、缺 evidence 或不符合目前 KB；上一個 Step 不乾淨時，優先重整舊文件，不直接跳下一步。
- 已補上技術硬底子與決策比較模組：每條高價值 flow 可新增 `decision-notes.md`，整理技術選型、差異比較、trade-off、owner decision 與面試追問。
- 已補上大專案 / 子專案地圖規則：地圖只用來定位 repo 與 flow，不取代 production flow。
- 已補上初階到資深的軟硬實力矩陣：作為定期檢查表，不作為新的發散學習主線。
- 已重整 `app_bi` Step 1 / Step 2 / Step 3，並補上 `point-control-admin-operation` 的 `decision-notes.md`。
- 已修正 Step 主線規則：AI 不可自行把「下游定位 / 補 evidence / 補 decision-notes / 架構圖」升級成新下一步；Step 3 乾淨後預設進 Step 4。
- 已完成 `app_bi point-control-admin-operation Step 4`，轉成保守面試 case，未更新履歷。
- 已完成 `app_bi point-control-admin-operation Step 5`，判定不更新正式履歷 / 自傳，只保留為面試分析 case 與候選履歷素材。

## 下一步

### 1. app_bi admin-config-redis-sync Step 3

建議下一步：

```text
app_bi admin-config-redis-sync Step 3
```

原因：

- `app_bi point-control-admin-operation` 已完成 Step 1-5。
- 單條 flow 完成不代表整個 `app_bi` project 完成。
- 應回到 `app_bi` Step 2 ranking，選下一條未完成且仍有價值的 flow。
- `payment-order-status-repair` 價值高，但只靠 `app_bi` 不適合深挖；若留在 `app_bi`，下一條較適合做 `admin-config-redis-sync`。

### 2. 再做第一條完整後端 flow

建議後端主線：

1. `payment-provider-callback`
2. `transfer-wallet-transfer-in-out`
3. `bet-settlement`
4. `settled-bets-kafka`
5. `observability-pipeline`

### 3. 每條完成後自動判斷是否更新

每條 flow 完成後，AI 要自動判斷是否需要更新：

- project README
- Step 文件
- flow evidence / claim-boundary
- `01-senior-owner-flow-inventory.md`
- `04-interview-casebook.md`
- `05-resume-master-zh.md`
- `08-application-autobiography-zh.md`
- `06-todo.md`

但不要把履歷寫太滿。只有完成 flow 並補 evidence 後，才升級履歷說法。

## 下一個 prompt

```text
app_bi admin-config-redis-sync Step 3
```

AI 會依共用規則自動重讀 KB、既有 project 文件與 `/Users/nick/Git/iwin/app_bi` code 最新狀態，不需要 Nick 每次重貼完整規則。

## Senior 面試最低門檻

要開始比較有把握投 10 萬以上 Senior / Platform Backend 職缺，至少先完成：

1. `payment-provider-callback`
2. `transfer-wallet-transfer-in-out`
3. `settled-bets-kafka`

每條都要有：

- `flow.md`
- `evidence.md`
- `decision-notes.md`
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
