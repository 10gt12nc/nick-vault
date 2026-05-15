# iwin app_bi Career / Interview

更新時間：2026-05-15
文件角色：project-level 保守面試素材
證據層級：分析素材 / learning-only；專案存在 / code-backed；Nick 貢獻待確認

## 結論

`app_bi` 可以作為面試中的「後台入口如何追 production flow」素材，但目前不更新正式履歷 / 自傳。

可以說：

```text
我整理過 iwin app_bi 的後台 / BI / control plane flow。它本身多半不是交易 source of truth，但能作為追 payment、game log、Redis config、報表 projection 與營運操作風險的入口。我會把後台查詢或操作降級成入口 evidence，再往下游後端 repo 找真正的狀態機、一致性、retry、補償與對帳邊界。
```

不能說：

```text
我主導 app_bi。
我負責完整後台系統。
我設計 app_bi 架構。
我透過 app_bi 證明自己負責 payment / wallet / game settlement。
```

## 可講的面試主軸

- 後台操作不是等於 runtime truth。
- 報表與查詢多半是 projection / troubleshooting view。
- 人工修正入口要回到 payment source of truth 才能判斷 money correctness。
- Redis sync 要追 runtime consumer、reload ack、version / checksum、reconcile。
- 遊戲局查詢要追 log writer、wallet / provider transaction、settlement 狀態。

## 已完成素材

| Flow | 可講重點 | 正式履歷 |
| --- | --- | --- |
| `point-control-admin-operation` | 後台 control plane、DB / Redis / GM command / audit 邊界 | 不更新 |
| `admin-config-redis-sync` | DB -> Redis projection、runtime config stale risk | 不更新 |
| `daily-game-record-summary` | BI projection、batch correctness、報表不等於交易 truth | 不更新 |
| `game-round-record-query` | 玩家申訴排查、log_reel、writer 線索、source of truth 邊界 | 不更新 |

## 履歷邊界

目前只保留為面試分析素材，不放入：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

若未來要升級成正式履歷，至少需要：

- Nick 本人 MR / ticket / commit / production issue / 本人確認。
- 明確說明 Nick 是做 app_bi 本身，還是透過 app_bi 追下游後端 flow。
- 不把後台入口包裝成完整 payment / wallet / game settlement owner。

## 下一步

只推薦一件事：

```text
payment Step 1
```

原因：

- app_bi 已收斂為入口 / 分析素材。
- 下一步應補 money correctness 的後端 source of truth。
