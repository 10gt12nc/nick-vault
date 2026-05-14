# Decision Notes - point-control-admin-operation

更新時間: 2026-05-13
用途: 技術硬底子與決策比較
狀態: 初版，承接 Step 3 重整

## 本 flow 牽涉的硬底子

這條 flow 不是要把所有分散式系統理論都讀完，而是針對 `point-control-admin-operation` 補必要底層。

直接相關:

- Transaction boundary
- Redis projection / runtime state
- GM command timeout / duplicate / partial success
- Batch operation partial failure
- Audit log reliability
- Reconciliation
- Admin permission boundary

## 專案已確認 evidence

已確認:

- MySQL transaction 只包住 `point_control` 操作。
- Redis hSet / hDel 在 GM command 前發生。
- GM command 用 HTTP call，失敗時回 false。
- 單筆 GM 失敗會 rollback DB，但未看到 Redis rollback。
- 單筆 DB commit 後才寫 Mongo log。
- 批量 GM 失敗會重送一次，但未看到第二次失敗後中止或標記 partial failure。
- `index()` 查詢會依 Redis `controlLimitRemain` 更新 DB status=2。
- `Base::_initialize()` 有權限檢查，但 `PointControlController::_initialize()` 未呼叫 parent，權限邊界待確認。

## 專案未確認

未確認:

- GM command receiver。
- Runtime service 是否直接讀 Redis。
- GM command 是否 idempotent。
- 是否有 operation id / version。
- 是否有 DB / Redis reconcile job。
- Mongo log 失敗是否有 fallback。
- 高風險操作是否有 approval / 二次確認。

## 技術選型比較

### Option A：維持同步 GM command，但補可觀測性與補償

適合情境:

- 目前系統已經依賴同步 GM command。
- 不想大改架構。

優點:

- 改動小。
- 容易逐步落地。
- 可以先補 log、retry result、partial failure 標記。

缺點:

- 仍然受 HTTP timeout / downstream 不穩影響。
- Redis / DB / GM command 不會自然一致。

production 風險:

- 仍可能發生 Redis 已寫但 GM 失敗。
- 批量操作仍需小心 partial success。

Owner 判斷:

- 短期合理。
- 第一優先是補 GM command 結果紀錄、Redis rollback / pending、reconcile。

### Option B：改成 pending state + worker / job 套用

適合情境:

- 可以接受後台操作不是立即同步完成。
- 想要更可靠地處理 retry 與補償。

優點:

- 可把 side effect 變成可重跑任務。
- 方便記錄狀態: pending / applied / failed / retrying。
- 比同步 HTTP 更容易做 observability。

缺點:

- 架構改動較大。
- 需要補 worker、狀態表、重試策略、營運查詢頁。

production 風險:

- 若 worker lag，後台顯示與 runtime 生效會有延遲。
- 需要定義使用者看到的狀態。

Owner 判斷:

- 中長期可考慮。
- 目前 evidence 不足，不建議直接寫「應該重構」。

### Option C：以 Redis 作 runtime source，DB 只做管理紀錄

適合情境:

- 下游 runtime 明確只讀 Redis。
- Redis 有足夠持久化與重建策略。

優點:

- runtime 讀取快。
- 後台控制可以快速生效。

缺點:

- Redis 與 DB 一致性要額外維護。
- Redis 遺失或 stale data 會直接影響 runtime。

production 風險:

- DB active 但 Redis 缺失。
- DB inactive 但 Redis 還存在。
- Redis remain 已歸零但 DB status 未更新。

Owner 判斷:

- 必須有 reconcile。
- 必須知道 Redis rebuild 來源。

## Senior / Owner 決策

我會先做:

1. 定位下游 GM receiver / Redis consumer。
2. 補 GM command 結果紀錄與錯誤可查性。
3. 補批量 partial failure 狀態。
4. 補 DB / Redis reconcile。
5. 確認權限與高風險操作 audit。

我會暫時不做:

- 不直接重構成完整 event-driven。
- 不直接宣稱要上 distributed transaction。
- 不在沒確認下游前改 Redis / GM command 契約。

原因:

- 目前 evidence 只到 `app_bi` 發送端。
- 先補可觀測性與收斂機制，風險 / 成本比最高。

## 面試追問

### 為什麼不是 DB transaction 就好？

因為 DB transaction 只保護 MySQL。Redis、HTTP GM command、Mongo log 都是 transaction 外 side effect。

### Redis 先寫是不是錯？

不能直接說錯，要看下游怎麼讀。若 GM command 只是通知 reload，而下游直接讀 Redis，先寫 Redis 可能是設計需求。但失敗時要有 rollback / pending / reconcile。

### 批量操作最大問題是什麼？

partial success。多個 center 中一部分成功、一部分失敗時，要能知道哪些已套用、哪些未套用，不能只回「操作成功」。

### Mongo log 失敗怎麼辦？

如果 audit log 對營運追查重要，Mongo log 不應只是 best effort。至少要有 fallback log、outbox-like record，或在操作狀態中標記 audit write failure。

### 權限邊界怎麼看？

後台高風險操作不能只看前端 menu。要確認後端 action 是否經過 permission check。本 flow 目前看到 `Base` 有權限檢查，但 `PointControlController::_initialize()` 沒呼叫 parent，需確認是否另有保護。
