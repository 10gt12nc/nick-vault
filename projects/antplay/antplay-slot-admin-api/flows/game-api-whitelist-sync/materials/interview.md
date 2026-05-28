# game-api-whitelist-sync Interview Notes

日期: 2026-05-28

## Step 3 面試方向

這條 flow 的面試主軸不是「我做了一個 CRUD」，而是:

```text
後台 control plane 如何透過 DB + Redis 影響 game-api runtime access-control。
```

## 常見追問

| 追問 | 回答方向 |
| --- | --- |
| DB 和 Redis 哪個是 source of truth? | DB 是設定保存與後台查詢來源；Redis 是 runtime filter 實際判斷來源。 |
| DB 成功、Redis 失敗怎麼辦? | 現有 flow 有 failure window；正式 owner 會補 retry、rebuild cache、reconciliation 與告警。 |
| query-then-insert 能防重嗎? | 只能擋一般重複操作；併發下仍建議 DB unique key `(agent_id, ip)`。 |
| sync 為什麼危險? | 逐 agent batch，Redis 不跟 DB transaction rollback，可能部分 agent 成功、部分失敗。 |
| 這算 security platform 嗎? | 不能誇大。這是 Game API IP whitelist control plane / runtime filter support，不是完整 security platform。 |

## Step 4 應補

- 30 秒 / 90 秒 / 3 分鐘正式說法。
- STAR。
- failure scenario Q&A。
- Lead / Architect 追問。
- 和 UGSoft 白名單 control plane 的差異比較。
