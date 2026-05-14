# Career Interview - admin-config-redis-sync

更新時間：2026-05-14
用途：單條 flow 的保守面試 / 履歷素材
證據層級：分析素材 / learning-only；code 功能為專案存在 / code-backed；Nick 貢獻待確認

## 結論

這條 flow 可以當面試分析案例，不更新正式履歷 / 自傳。

可講成：

```text
我分析過一條後台設定同步 Redis 的 control plane flow。表面上是後台按鈕同步，實際上是 MySQL source of truth 投影到 Redis，再影響 runtime / API 讀取設定。我會從 DB / Redis / reload / audit / reconcile 的角度拆風險。
```

不能講成：

```text
我主導 app_bi 設定同步平台。
我設計完整設定中心。
我修復 production 設定同步事故。
```

原因：

- 目前只確認到 `app_bi` 後台同步端與部分 `app_bi` API 讀取端。
- 未掃下游 runtime consumer。
- 未確認 Nick 本人 MR / ticket / commit / production issue。

## 面試 30 秒版

我看過一條後台設定同步 Redis 的 flow。它不是單純 CRUD，因為後台設定存在 MySQL，但線上服務或 API 讀的是 Redis projection。同步時會把支付設定、商戶設定、玩家層級、活動設定、遊戲清單等資料轉成 Redis key。真正的風險是 DB 和 Redis 不一致、部分 channel 同步成功、部分失敗，或 Redis 已更新但 runtime 沒 reload。

## 面試 3 分鐘版

我會先把這條 flow 定位成 control plane 到 runtime projection。

後台設定本身存在 MySQL，例如支付方式、商戶設定、玩家層級、活動設定、遊戲清單和渠道資訊。管理員在後台按同步後，`RedisSynchronize` 會從 MySQL 查出啟用資料，組成 runtime 需要的 JSON 或 hash，再寫到 Redis。部分設定還會送 `sendGmCommand()`，通知 runtime reload。

我看的重點不是 controller 有幾個 method，而是狀態怎麼轉：

```text
MySQL 設定已修改
-> waitSyn 標示待同步
-> Redis projection 已更新
-> reload command 已送出
-> runtime 已套用新設定
```

主要 production 風險：

- DB 已改但 Redis 未同步，後台看起來是新設定，但線上仍讀舊 Redis。
- 多 channel / 多 key 同步可能 partial success。
- 欄位投影遺漏，DB / UI 有欄位但 Redis projection 沒補齊。
- Redis 寫入成功不等於 runtime 已套用。

如果我是 owner，我會優先補 per channel / per key 同步結果、version / checksum、reload ack 邊界、避免高風險 key 先刪後寫，以及 DB vs Redis 的 reconcile。

## 可展示能力

- 把後台設定同步視為 control plane -> runtime projection。
- 區分 MySQL source of truth 與 Redis projection。
- 看出 partial success、stale config、欄位漏投影與 reload failure。
- 能說明 owner 要怎麼定義「同步成功」。

## 履歷候選句

目前不建議寫進正式履歷。

如果未來 Nick 補到本人 evidence，才可考慮非常保守的版本：

```text
參與後台設定同步 Redis 相關功能維護，協助釐清 MySQL 設定來源、Redis projection、同步狀態與 runtime 生效邊界，提升設定變更排查與維護可理解性。
```

目前證據層級不足，不放入：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

## Claim Boundary

可以說：

- 我分析過後台設定同步 Redis 的 production flow。
- 我能從 MySQL source of truth、Redis projection、runtime reload、audit、reconcile 拆風險。
- 我能指出 partial success、stale config、欄位漏投影、reload failure 的風險。
- 我能用這條 flow 說明 owner 要怎麼看「同步成功」。

不能說：

- 我主導 Redis 設定同步平台。
- 我設計完整設定中心。
- 我負責全鏈路 runtime config owner。
- 我修復 `whatsapp` / `notEmbeddedMerchantList` / merchant status 投影問題。
- 我改善同步成功率、延遲或事故率。

## 詳細附錄

- 主研究報告：`flow.md`
- 證據附錄：`materials/evidence.md`
- 技術決策附錄：`materials/decision-notes.md`
- 面試稿附錄：`materials/interview.md`
- 履歷邊界附錄：`materials/claim-boundary.md`
