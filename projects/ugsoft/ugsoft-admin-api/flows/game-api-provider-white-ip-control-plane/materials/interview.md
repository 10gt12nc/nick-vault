# Interview Notes - game-api-provider-white-ip-control-plane

日期: 2026-05-27

## Step 3 面試素材草稿

### 一句話

這條是後台 control plane 影響 runtime access-control 的 case，重點是 DB 設定、Redis / in-memory cache、權限與 audit 之間的一致性邊界。

### Senior 追問

| 追問 | 回答要點 |
| --- | --- |
| 為什麼不是 CRUD? | 因為後台新增 / 刪除會影響 connector runtime 是否放行 API request / provider callback。 |
| DB 和 Redis 不一致怎麼辦? | DB 是 source of truth，Redis 是 runtime view；要有重建 cache、manual repair、operation log 和監控。 |
| DB 成功但 fanout 失敗怎麼辦? | provider cache 可能 stale；目前 code 是 best-effort publish，不是 outbox，需補 retry / reload / metric。 |
| duplicate 怎麼處理? | Game API 是 `agent_id + ip`，provider latest 是 `provider + ip`；但未驗證 DB unique，不能說完全防 race。 |
| provider 為什麼改全站共用? | 這是 latest current behavior，可說 scope decision，但 `arnold` 改動不能當 Nick direct evidence。 |
| 這條能放履歷嗎? | Step 3 先不直接放；Step 5 或 contribution refresh 後可作後台 control plane / DB Redis 同步 supporting evidence。 |

### 誇大陷阱

- 面試官問「你設計 connector reload 嗎？」
  保守答: 我有 code-backed 理解 latest reload path，但 fanout reload 是主管後續改動，我可以分析它的 failure window，不把它說成我主導。

- 面試官問「這是否強一致？」
  保守答: 不是。DB + Redis / MQ fanout 不是單一交易，這是 eventual consistency，需要補 reload / repair / monitoring。

- 面試官問「這是不是完整資安系統？」
  保守答: 不是完整資安平台，是 runtime access-control 的白名單控制面 case。
