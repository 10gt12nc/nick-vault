# game-round-record-query - Career / Interview

更新時間：2026-05-15
完成狀態：Step 3 已完成；Step 4 / Step 5 尚未完成
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 定位

這條 flow 目前適合作為「production troubleshooting 分析素材」，不是正式履歷成果。

它可以訓練面試時怎麼講：

- 後台查詢不是交易 truth。
- 玩家申訴排查要追 log writer、分表、channel、serial id 與 wallet / provider 對帳。
- 看到後台功能要能往下追真正後端 flow，而不是停在 controller。

## 目前可說

保守說法：

> 我有整理過遊戲局紀錄查詢 flow。後台會依玩家 ID、牌局號、第三方交易單號與日期查每日 `log_reel` 分表，並把 `common_data` 解析成各遊戲可讀的牌局細節。這類功能在玩家申訴與 production troubleshooting 很重要，但它是戰績 log / projection，不等於完整交易 truth；真的要判斷金額正確性，還要對 wallet、currency log、provider record 與 settle flow。

可延伸成 Senior 觀點：

- 查不到局時，不能直接說玩家沒玩，要先排除 channel shard、日期分表、log 延遲、writer 失敗。
- 查到局但金額不對時，要區分下注、派彩、餘額、戰績 log、wallet ledger。
- 第三方 provider 的 bet / settle / refund 可能是 insert + update 模型，要注意 `serial_id` 的唯一性與 update miss。

## 目前不能說

不能寫進履歷：

- 主導遊戲局查詢系統。
- 負責遊戲結算一致性。
- 建立 log pipeline。
- 改善查詢效能。
- 修復玩家申訴 production issue。

原因：

- 目前只有 code-backed flow 分析。
- 沒有 Nick 本人 MR / ticket / commit / production issue / 本人確認。
- `app_bi` 主要是後台查詢端，不是遊戲結算主系統。

## Step 4 應補

Step 4 再整理：

- 3 分鐘面試講法。
- Senior 追問與答法。
- Lead / Architect 追問：log projection vs source of truth、partition search、idempotent update、observability。
- 面試時如何誠實講「我分析過 / 維護時會這樣排查」，而不是說成「我主導」。

下一步：

```text
app_bi game-round-record-query Step 4
```
