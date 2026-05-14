# Interview - app_bi admin-config-redis-sync

更新時間：2026-05-14
Step：4 轉面試 case
掃描等級：Level 2 Flow 深掃延伸
證據層級：分析素材 / learning-only；code 功能為專案存在 / code-backed；Nick 貢獻待確認
格式狀態：舊平鋪格式可沿用，尚未遷移到 `materials/`

## 本次結論

這條 flow 可以轉成面試 case，但只能保守講成：

```text
我分析過一條後台設定同步 Redis 的 control plane flow。
表面上是後台按鈕同步，實際上是 MySQL source of truth
投影到 Redis，再影響 runtime / API 讀取設定。
我會從 DB / Redis / reload / audit / reconcile 的角度拆風險。
```

不能講成：

```text
我主導 app_bi 設定同步平台。
我設計完整設定中心。
我修復 production 設定同步事故。
```

原因：目前只確認到 `app_bi` 後台同步端與部分 `app_bi` API 讀取端；未掃下游 runtime consumer，也未確認 Nick 本人 MR / ticket / commit / production issue。

## 30 秒版本

我看過一條後台設定同步 Redis 的 flow。它不是單純 CRUD，因為後台設定存在 MySQL，但線上服務或 API 讀的是 Redis projection。同步時會把支付設定、商戶設定、活動設定、遊戲清單等資料轉成 Redis key。真正的風險是 DB 和 Redis 不一致、部分 channel 同步成功、部分失敗，或 Redis 已更新但 runtime 沒 reload。所以我會把「同步成功」拆成三層：Redis 寫入成功、runtime 套用成功、使用者實際看到新設定。

## 3 分鐘版本

我會先把這條 flow 定位成 control plane 到 runtime projection。

後台設定本身存在 MySQL，例如支付方式、商戶設定、玩家層級、活動設定、遊戲清單和渠道資訊。管理員在後台按「同步到正式服」後，`RedisSynchronize` 會從 MySQL 查出啟用資料，組成 runtime 需要的 JSON 或 hash，再寫到 Redis。部分設定還會送 `sendGmCommand()`，通知 runtime reload。

我看的重點不是 controller 有幾個 method，而是狀態怎麼轉：

```text
MySQL 設定已修改
-> waitSyn 標示待同步
-> Redis projection 已更新
-> reload command 已送出
-> runtime 已套用新設定
```

這裡有幾個 production 風險。

第一是 DB 已改但 Redis 未同步。後台看起來是新設定，但線上仍讀舊 Redis。

第二是 partial success。很多同步是逐 channel、逐 key 寫入，如果中途失敗，可能 A channel 成功、B channel 失敗，或 `settings` 成功但 `clientSettings` / `channel:{code}` 沒成功。

第三是欄位投影遺漏。git history 裡有補 `whatsapp`、補 `notEmbeddedMerchantList`、merchant 只同步 `status = 1` 的修改，表示真實維護上確實會遇到「DB 有資料，但 Redis projection 沒補齊」的問題。

第四是 reload 邊界。某些 method 會送 GM reload，但像營運 / 提現設定那段，即使 GM command 失敗，也可能只記 log，最後仍回成功。這代表「寫 Redis 成功」不等於「runtime 已套用」。

如果我是 owner，我會優先補幾個保護：per channel / per key 同步結果、version / checksum、reload ack 邊界、避免高風險 key 先刪後寫、以及 DB vs Redis 的 reconcile。這樣事故時才知道是哪個設定、哪個 channel、哪個 runtime 沒套用。

## 面試主軸

### 面試官問：這不是很普通的後台同步 Redis 嗎？

可以回答：

普通 CRUD 是「資料寫進 DB 就結束」，但這條不是。它有兩份狀態：MySQL 是設定來源，Redis 是 runtime projection。後台同步成功只代表某個操作完成，不一定代表所有 runtime 都已讀到新設定。Senior 要看的不是 hSet，而是資料是否完整投影、是否部分成功、失敗後能不能知道哪個 channel / key 出問題，以及能不能從 DB 重建 Redis。

### 面試官問：你會先看哪幾個風險？

可以回答：

我會先看四個。

第一，DB 改了但 Redis 沒同步，系統是否有 `waitSyn` 或提醒。

第二，多 channel / 多 key 同步是否會 partial success，失敗時有沒有保留待同步狀態。

第三，Redis 寫入成功後 runtime 是否需要 reload，reload 失敗時是否仍回成功。

第四，新欄位或新設定是否容易漏投影，例如 DB / UI 有欄位，但 Redis JSON 沒補。

### 面試官問：如果同步到一半失敗怎麼辦？

可以回答：

我不會先假設能 transaction rollback，因為 Redis、GM command、runtime reload 都不在同一個 DB transaction 裡。比較務實的做法是先把結果粒度補出來：哪個 channel、哪個 key 成功，哪個失敗。失敗的保留 `waitSyn` 或建立可重試記錄，再提供人工重同步或 reconcile。高風險設定可以考慮 temp key + swap，避免先刪後寫造成中間空窗。

### 面試官問：Redis projection 要怎麼設計比較穩？

可以回答：

我會讓 MySQL 還是 source of truth，Redis 只當 projection。每次 projection 最好帶 `version`、`updated_at`、`operator`、`checksum`。同步後可以比對 DB enabled settings 和 Redis key，確認資料量、欄位和 checksum 一致。runtime 如果有 reload，也要把「Redis 已更新」和「runtime 已套用」拆成兩個狀態，不能只看後台回成功。

### 面試官問：這條 flow 裡你能證明什麼？

可以回答：

我能證明的是：code 裡存在大量後台設定同步 Redis 的入口；有 MySQL 到 Redis projection；有 `waitSyn`、`opLog`、部分 `sendGmCommand()`；也有 commit history 顯示欄位漏投影和啟用狀態投影邊界曾經被修正。不能證明的是：我本人主導這些功能，或下游 runtime 全鏈路都已掃完。

## Senior 追問清單

- MySQL 和 Redis 誰是 source of truth？
- 同步成功的定義是什麼？
- 如果 Redis 寫入成功但 reload 失敗，要回成功還是失敗？
- 多 channel 同步時，第 5 個 channel 失敗，前 4 個成功要怎麼處理？
- `waitSyn` 是提醒、狀態機，還是可恢復機制？
- `opLog` 是否能還原 before / after？
- 欄位新增時，怎麼避免 DB / UI 有欄位但 Redis projection 漏掉？
- 活動設定先刪後寫會不會有短暫空窗？
- 是否需要 version / checksum？
- 是否需要 reconcile job？

## Lead / Architect 追問清單

- 這種設定同步應該手動觸發、自動同步，還是 background job projection？
- 高風險設定是否需要 approval / two-man rule？
- 是否需要把設定發布做成版本化 rollout？
- runtime reload 要不要 ack？ack 粒度是節點、服務、channel，還是 key？
- 如果多個後台同時同步，是否有 ordering 問題？
- Redis key schema 是否有向後相容策略？
- 是否需要灰度發布設定？
- 設定錯誤如何 rollback？
- 如何設計 dashboard 看 DB / Redis / runtime version 差異？

## 可說 / 不可說

### 可以說

- 我分析過後台設定同步 Redis 的 production flow。
- 我能從 MySQL source of truth、Redis projection、runtime reload、audit、reconcile 拆風險。
- 我能指出 partial success、stale config、欄位漏投影、reload failure 的風險。
- 我能用這條 flow 說明 owner 要怎麼看「同步成功」。

### 必須保守說

- 這是 `app_bi` 後台入口與發送端分析。
- 目前只確認部分 `app_bi` API 讀 Redis。
- 下游 runtime consumer 尚未掃。
- Nick 個人實作貢獻待確認。

### 不能說

- 我主導 Redis 設定同步平台。
- 我設計完整設定中心。
- 我負責全鏈路 runtime config owner。
- 我修復 `whatsapp` / `notEmbeddedMerchantList` / merchant status 投影問題。
- 我改善同步成功率、延遲或事故率。

## 履歷保守 bullet

目前不建議寫進正式履歷。

如果未來 Nick 補到本人 evidence，才可考慮改成非常保守的版本，例如：

```text
參與後台設定同步 Redis 相關功能維護，協助釐清 MySQL 設定來源、Redis projection、同步狀態與 runtime 生效邊界，提升設定變更排查與維護可理解性。
```

目前證據層級不足，不放入：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

## Step 4 / Step 5 結論

這條 flow 已可作為保守面試 case。

Step 5 已完成履歷 / 自傳更新判定：

- 不更新 `senior-owner-playbook/05-resume-master-zh.md`。
- 不更新 `senior-owner-playbook/08-application-autobiography-zh.md`。
- 保留為面試分析素材，不包裝成 Nick 個人實作成果。

目前定位：

- `flow.md`：主研究報告。
- `evidence.md`：code / commit evidence。
- `decision-notes.md`：技術硬底子與設計選項。
- `claim-boundary.md`：不能誇大的邊界。
- `interview.md`：本 Step 4 面試素材。

下一步只推薦一件事：

```text
app_bi daily-game-record-summary Step 3
```

原因：

- `admin-config-redis-sync` 已完成 Step 5。
- 同 project 下一條未完成且值得做的是 `daily-game-record-summary`。
- 產出會是報表 projection、truth source、資料延遲與補跑邊界。
- 預期不更新履歷，除非後續補到 Nick 本人 evidence。
