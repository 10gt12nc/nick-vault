# Decision Notes - game-api-provider-white-ip-control-plane

日期: 2026-05-27

## 這條 flow 的技術硬底子

### Control Plane vs Data Plane

- Control plane: 後台新增 / 刪除白名單、寫 DB、同步 cache、寫 operation log。
- Data plane: connector runtime request / callback 進來時，用 agentId / provider + source IP 判斷是否放行。

面試時要強調: 這不是單純 CRUD，因為 control plane 的設定會直接改變 runtime access boundary。

### DB source of truth vs runtime cache

Game API:

- DB: `game_api_login_ip_white`
- Runtime cache: Redis set `ugSoft:GameApiWhiteIp:{agentId}`

Provider:

- DB: `provider_login_ip_white`
- Runtime cache: connector in-memory provider -> IP set

Owner 判斷: DB 可以重建 cache；cache 快但可能 stale。故障時要知道以哪邊為準。

### Scope decision

Game API white IP 是 agent-scoped。Provider white IP latest 是 provider global-scoped。

這是產品 / 安全 / runtime 行為的重大差異:

- agent-scoped: 更細，但管理量與同步複雜度高。
- global-scoped: 管理簡化，但放行範圍變大，必須確認是否符合 provider callback 信任模型。

### Consistency decision

DB + Redis / RabbitMQ fanout 沒有強交易。可選策略:

- 接受 eventual consistency，補 operation log / reload / manual repair。
- 寫 DB 後由定時 job / startup reload cache，fanout 只作加速。
- 把 cache update 失敗變成操作失敗，但可能影響後台可用性。
- outbox pattern: DB 寫設定與待發事件同 transaction，再由 worker 發 fanout。

目前 code 比較接近 best-effort cache update / fanout notification，不應誇大成完整 outbox。

## 面試可主動講的 owner trade-off

- 對白名單這種安全設定，刪除 IP 後 cache stale 比新增 IP 後 cache stale 更危險，因為前者可能繼續放行。
- provider cache reload 若失敗，應至少有 last refresh time、reload error log、manual reload 或 health check。
- 操作權限需要比一般查詢更嚴格，因為這是 runtime access-control setting。
- operation log 必須包含 agent / provider / ip / operator，否則出了問題很難追。

## Step 4 補充: 面試決策框架

面試時可以用「三層」講法避免發散:

1. Setting layer: 後台 DB 是 source of truth，必須有 duplicate boundary、權限與 operation log。
2. Propagation layer: Game API 走 Redis set，Provider latest 走 RabbitMQ fanout reload；兩者都是 propagation，不是 source of truth。
3. Enforcement layer: connector runtime filter / callback check 只看 runtime view，所以 cache freshness 決定實際放行結果。

如果面試官追問「怎麼做更好」，不要直接喊微服務或重架構，先按風險分級:

- 小補強: IP 格式校驗、DB unique、operation log 欄位完整、Redis / cache rebuild endpoint。
- 中補強: reload metric、last refresh time、manual reload、刪除操作告警。
- 大補強: outbox pattern、retry worker、節點健康檢查、定期 reconcile DB 與 cache。

本 flow 不適合包裝成 money correctness；它的價值是 runtime access boundary、cache consistency 與安全設定治理。
