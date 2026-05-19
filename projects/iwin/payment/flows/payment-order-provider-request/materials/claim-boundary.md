# claim-boundary

## 目前結論

本 flow 已完成 Step 5 claim gate。

結論：可以把 Nick 的個人貢獻從 `待確認` 升級為 `部分 provider request 真實開發過`。

可更新：

- flow-level `career-interview.md`
- `senior-owner-playbook/05-resume-master-zh.md` 的金流 provider 經驗描述
- `senior-owner-playbook/08-application-autobiography-zh.md` 的投遞用保守描述

原因：

- `git log --all` 與 path-specific history 顯示 `10gt12nc <60815760+10gt12nc@users.noreply.github.com>` 對多個 provider request / callback / query 相關檔案有新增與修正。
- `origin/pay4z-Nick` 指向 Pay4z 對接分支，`7853917` / `702cc73` 新增 `Pay4zController` 與 `Pay4zServiceImpl`。
- `origin/NaNapay_Nick` 包含 NaNapay 對接，`7ae7f11` 新增 `NanaPayController` 與 `NanaPayServiceImpl`。
- BFPAY / NewCashPay query、NimTestPay local / SIT manual testing controller 也有 `10gt12nc` path-specific commits。

## 可寫入面試素材

可以保守說：

- 「我參與過 iwin payment provider request / callback / query 對接與維護。」
- 「比較明確的 code evidence 包含 Pay4z、NaNapay、BFPAY，以及 NimTestPay local / SIT testing controller。」
- 「這類 flow 涉及 `payment_order`、商戶設定、provider request、簽章、金額單位、callback 與查單補償。」
- 「我會用它討論 provider integration 的 transaction boundary、timeout、idempotency、reconciliation。」

## 可升級 claim

- `真實開發過`：Pay4z provider request / callback / query 對接，依 `origin/pay4z-Nick` 與 `7853917` / `702cc73`。
- `真實開發過`：NaNapay provider request / callback / query 對接，依 `origin/NaNapay_Nick` 與 `7ae7f11`。
- `真實維護過`：BFPAY / NewCashPay 查單與狀態處理邊界，依 `260e550`。
- `真實修復過`：Pay4z callback / sign 相關問題，依 `9aa0477`。
- `真實測試支援 / SIT tooling`：NimTestPay local / SIT manual testing controller，依 `7403277`；不可包裝成 production provider owner。

## 仍需保守

- 只能寫「參與」或「維護」，不要寫「主導」。
- 只能寫已掃到的代表 provider，例如 Pay4z、NaNapay、BFPAY；不要寫全部 provider。
- NimTestPay 要標成 local / SIT manual testing tooling，不要寫 production 上線成果。
- `createOrderNo` id collision fix 可以作為資料一致性 bugfix，但不要說完整建單架構都是 Nick 設計。
- 沒有 production incident / metric 前，不寫改善比例或事故解決成效。

## 禁止 claim

- 禁止寫「主導 iwin 三方金流串接」。
- 禁止寫「設計 provider request 架構」。
- 禁止寫「對接全部 payment provider」。
- 禁止寫「解決 provider request timeout 對帳」。
- 禁止寫「建立完整 reconciliation 機制」。
- 禁止寫「確認所有 provider idempotent」。
- 禁止把 NimTestPay 寫成正式 production provider 上線成果。

## 下一步

```text
iwin game_api partner-deposit-withdraw-bill Step 4
```

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：真實開發過。Nick / `10gt12nc` 在 Pay4z、NaNapay、BFPAY、NimTestPay 與 `createOrderNo` 相關 commits / branches 有 path-specific evidence，可保守寫「參與第三方金流 provider request / callback / query 對接與維護」。
- 可面試講：code-backed / 分析過。可用本 flow 說明 provider request、callback、query、timeout unknown、訂單狀態與查單補償風險。
- 不可誇大：不是主導完整金流 owner。不得寫成主導 iwin payment、對接全部 provider、設計完整 provider 架構、建立完整 reconciliation 或改善成功率 X%。
