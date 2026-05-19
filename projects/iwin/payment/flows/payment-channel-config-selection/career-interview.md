# payment-channel-config-selection career-interview

完成狀態：Step 5 已完成
證據層級：專案存在 / code-backed；分析素材 / learning-only；Nick 貢獻未確認到可放履歷的直接 evidence

## 保守定位

這條 flow 已完成 Step 5 claim gate，最終只作面試分析素材，不放入正式履歷。

可說：

- 我分析過 iwin payment 的支付方式 / 商戶 / 提現設定選擇 flow，重點是 DB 設定如何透過 Redis projection 被 payment runtime 消費。
- 這條 flow 不是直接 money movement，但它決定玩家能否看到正確的充值 / 提現入口。
- 面試時可討論玩家層級、channel、device、商戶 status、Redis partial sync、cold-cache fallback 與 config versioning。

不可說：

- Nick 主導 payment config selection。
- Nick 設計了完整設定中心。
- Nick 修過 payment list/detail production issue。
- 已確認完整 config version / atomic rollout。

## 30 秒講法

payment 的支付列表不是硬編碼，也不是把所有商戶都丟給前端。玩家呼叫 `/payment/list` 或 `/payment/detail` 時，後端會先查玩家層級，再從 Redis `settings` 讀支付類型、商戶、快捷支付、VIP 充值與提現設定，最後依 channel、device、玩家層級與商戶狀態過濾。這條 flow 的風險是 DB、Redis projection 和 runtime filter 不一致，會讓玩家看不到支付方式或看到錯商戶。

## 3 分鐘講法

我會把這條 flow 定位成 payment 的 runtime config selection。app_bi 後台維護 MySQL 設定，像 payment type、merchant、merchant account、convenient pay、VIP pay、pay setting、user layer，然後透過 RedisSynchronize 寫到 Redis `settings` hash。payment runtime 在 `/payment/list`、`/payment/detail`、`/public/withdrawConfig` 讀這些 projection，再結合玩家的 `log_user.user_layer`、channel、device 來決定前端看到什麼。

這裡的 owner 風險不是「能不能查到設定」而已，而是多個 key 要一致。舉例來說，`payTypeList` 出現某支付類型，但 `merchantList` 還沒同步到對應商戶，前端會看到支付方式卻沒有可用 detail；或者 `merchantList` 和 `merchantAccountList` 不一致，provider request 時就可能缺 merchant id。再來，cold cache fallback 也要測，因為 runtime 如果 Redis key 空了，fallback DB 後第一個 request 不能拿到空結果。

我會補的 owner guard 是 config version、每個 channel / key 的同步結果、schema validation、readiness check，並讓 payment runtime fail closed：不確定的商戶不要曝光，而不是讓玩家進入錯誤 money flow。

## STAR 面試素材

| 段落 | 保守講法 |
| --- | --- |
| Situation | payment runtime 需要依玩家層級、channel、device 和後台設定，決定玩家可見的支付 / 提現入口。 |
| Task | 分析 DB 設定、Redis projection、payment API filter 三層是否一致，找出會造成空列表、錯商戶或錯提現限制的 failure window。 |
| Action | 從 `/payment/list`、`/payment/detail`、`withdrawConfig` 往下讀到 `PayTypeServiceImpl`、`BaseServiceImpl`，再回看 app_bi `RedisSynchronize` 如何同步 `payTypeList`、`merchantList`、`merchantAccountList`、`paySetting`。 |
| Result | 收斂出 config version、同步 validation、per-channel readiness、cold-cache safety、fail closed 與 explainability 這幾個 owner decision。 |

## 可用問答

| 問題 | 保守答法 |
| --- | --- |
| 這條 flow 跟金流 correctness 有關嗎？ | 有，但它是前置條件。它不直接上分 / 扣款，但會決定玩家能否進入正確支付 / 提現 flow。 |
| payment list 怎麼決定顯示哪些支付方式？ | 讀玩家層級，讀 Redis 裡的 `payTypeList`、`merchantList`、`convenientPay`、`vipPay`，再依 channel、device、layers 過濾。 |
| payment detail 跟 list 差在哪？ | list 決定有哪些支付類型；detail 決定該 pay code 下有哪些商戶、快捷支付或 VIP 充值 detail。 |
| 最大風險是什麼？ | DB / Redis / runtime filter 不一致，尤其是多 key partial sync，會造成支付入口空白、錯商戶、錯檔位或提現限制錯誤。 |
| 會怎麼改善？ | 加 config version、同步結果表、schema validation、cold-cache 測試、channel/key readiness check，runtime 對不完整 config fail closed。 |
| 這條能放履歷嗎？ | 目前不放。它是 code-backed 分析素材，但沒有 Nick 直接 path-specific evidence。 |

## Step 5 Claim Gate

最終判定：

- 不更新 `senior-owner-playbook/05-resume-master-zh.md`。
- 不更新 `senior-owner-playbook/08-application-autobiography-zh.md`。
- 可保留在 `senior-owner-playbook/04-interview-casebook.md` 作 runtime config consistency 面試案例。

原因：

- `10gt12nc` 在相關 payment path 的 evidence 是 order insert id copy 修正，支撐 provider request / order insert consistency，不支撐本 flow 的 payment list/detail/config sync claim。
- app_bi payment config sync / RedisSynchronize 主線沒有看到 `10gt12nc` 直接修改 evidence。
- config / Redis projection 相關修改主要是 Derek / gill / arnold。

可用定位：

> 我分析過 payment runtime config selection，能從 DB source of truth、Redis projection、payment API eligibility 三層說明支付入口一致性風險。

不可升級為：

> 我主導 payment config selection / 設計設定中心 / 修復支付列表 production incident。

## 下一步

```text
iwin game_api contribution claim consolidation
```

## 履歷 claim 分層（2026-05-18 KB 對齊）

- 可放履歷：目前不單獨升級成本 flow 的真實開發成果；project-level payment contribution consolidation 已完成，payment 履歷只保守寫 provider 對接 / 維護與 order consistency。
- 可面試講：code-backed / 分析過。可用本 flow 說明 money correctness、狀態轉移、冪等、retry、補償、人工修復或 runtime config consistency。
- 不可誇大：不得把本 flow 寫成 Nick 主導完整 payment / wallet owner、設計整套金流架構、解決全部對帳或 production incident，除非後續補到本人 MR / ticket / production issue / 本人確認與重要 diff。
