# game-api-whitelist-sync Evidence

日期: 2026-05-28

## Step 5 掃描補充

Step 5 沒新增 code evidence，沿用 Step 3 / Step 4 的 Level 2 Flow 深掃結果，目標是完成單條 flow claim gate。已重新確認 source repo 本機 refs 狀態；嘗試 fetch remote refs 失敗，依 KB 規則不反覆重試，以下仍只能宣稱「依本地 refs / 本地工作樹判斷」，不能宣稱已看最新 remote。

## Source Repo 狀態

| Repo | Branch | Local HEAD | Local remote ref | Ahead / behind | Working tree | Fetch |
| --- | --- | --- | --- | --- | --- | --- |
| `antplay-slot-admin-api` | `main` | `2e15503d88de3af133cce17fbf3ff80262678419` | `origin/main` = `2e15503d88de3af133cce17fbf3ff80262678419` | `0 / 0` | clean | 嘗試 fetch 失敗，依本地 refs |
| `antplay-slot-game-api` | `develop` | `079aa6603b50db3c185e383295ca5966bbe272fb` | `origin/develop` = `079aa6603b50db3c185e383295ca5966bbe272fb` | `0 / 0` | clean | 嘗試 fetch 失敗，依本地 refs |

注意: 不記錄內網 remote URL / IP / sensitive detail。

## Vault 掃描範圍

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/antplay/antplay-slot-admin-api/README.md`
- `projects/antplay/antplay-slot-admin-api/step1-candidate-flows.md`
- `projects/antplay/antplay-slot-admin-api/step2-flow-comparison.md`
- 既有 `request-log-rabbitmq-admin-consumer` flow 文件作格式參考

## Source Code 掃描範圍

Admin-api control plane:

- `src/main/java/com/ps/domain/admin/controller/GameApiWhiteIpController.java`
- `src/main/java/com/ps/domain/admin/service/GameApiWhiteIpService.java`
- `src/main/java/com/ps/domain/admin/service/impl/GameApiWhiteIpServiceImpl.java`
- `src/main/java/com/ps/domain/admin/mapper/GameApiWhiteIpMapper.java`
- `src/main/resources/mapper/GameApiWhiteIpMapper.xml`
- `src/main/java/com/ps/domain/admin/entity/GameApiWhiteIp.java`
- `src/main/java/com/ps/domain/admin/entity/vo/GameApiWhiteIpVo.java`
- `src/main/java/com/ps/common/constant/RedisKey.java`

Game-api runtime context:

- `src/main/java/com/ps/common/filter/WhiteIpFilter.java`
- `src/main/java/com/ps/common/constant/RedisKey.java`

未掃 / 待確認:

- DB DDL / migration 是否有 `(agent_id, ip)` unique key。
- production / UAT 哪些環境啟用 `WhiteIpFilter` 的完整配置。
- Redis rebuild / reconciliation job 是否存在。
- runtime reject metrics / alert 是否存在。
- 後續 `arnold` / team commits 對 current behavior 的完整影響。

## Code Evidence

| Code path | Evidence | 判斷 |
| --- | --- | --- |
| `GameApiWhiteIpController#addWhiteIp` | `/game_api_white_ip/add`，role filter + `isLimitedAdmin`，查重後 DB insert + Redis `SADD` | 新增白名單主路徑已確認 |
| `GameApiWhiteIpController#getWhiteIp` | 透過 `SecurityContextHolder` 與 `AgentRoleUtil#processAgentIds` 決定查詢範圍 | role / agent scope 已確認 |
| `GameApiWhiteIpController#deleteWhiteIp` | `getIpById` 後 DB delete + Redis `SREM` | 刪除白名單主路徑已確認 |
| `GameApiWhiteIpController#ss00172r001WhitelistSync` | admin-only + limited admin，呼叫 `WhitelistSync(null, "SS00172R001")` | 特定 merchant sync 入口已確認 |
| `GameApiWhiteIpServiceImpl#WhitelistSync` | 來源 merchant IP 同步到 prefix agents，逐 agent Redis delete / add，再 DB delete / batch insert | batch sync 路徑已確認 |
| `GameApiWhiteIpMapper.xml` | `INSERT` / `DELETE` / `queryExistIp` / `getIpWhiteList` / `getIpById` | DB CRUD SQL 已確認 |
| `RedisKey.GAME_API_WHITE_IP` | `antplay:GameApiWhiteIp:` | admin-api 與 game-api 使用同一 key prefix |
| `WhiteIpFilter#doFilter` | 從 query/body 取 `agentId`，取 client IP，`SISMEMBER` Redis set，不在白名單則 reject | runtime enforcement path 已確認 |
| `AdminOperationLogService#insertAdminOperationLogsNew` | add / delete 成功後寫 operation log | audit path 已確認 |

## Commit Evidence

| Commit | Repo | Author | Evidence | Claim 判斷 |
| --- | --- | --- | --- | --- |
| `f90a1eb` | admin-api | `10gt12nc` | `feat(#682): slot-game-api API白名单功能`，新增 controller / service / mapper / entity / Redis key | Nick direct evidence |
| `6cf3855` | admin-api | `10gt12nc` | `delete deleteRedis`，補 delete / Redis remove | Nick direct evidence |
| `4c86d96` | admin-api | `10gt12nc` | `agentId 前端必傳 不會null` | Nick direct evidence |
| `1dd366c` | admin-api | `10gt12nc` | 狀態小寫修正 | Nick direct evidence |
| `0f0e559` | admin-api | `10gt12nc` | 同步 game-api `GAME_API_WHITE_IP` | Nick direct evidence |
| `e9bb03b` | game-api | `10gt12nc` | 同步 admin-api 舊版 GetIP / 白名單相關 context | downstream direct evidence |
| `b307244` | game-api | `10gt12nc` | `feat(#684): 同步 舊code`，白名單 filter context | downstream direct evidence |
| `0d69762` / `02559b8` / `2c6a351` | game-api | `10gt12nc` | POST body / cached body wrapper 修正 | downstream direct evidence |
| `d878824` | game-api | `10gt12nc` | WhiteIpFilter log 修正 | downstream direct evidence |
| `425e394` | admin-api | `eliot` | sync ip whitelist from parent | team current behavior context；不是 Nick direct evidence |
| `arnold` / `Derek` 後續 filter commits | game-api | team | filter order、環境限制、白名單修正 | team current behavior context；不是 Nick direct evidence |

## 已確認

- 後台新增 / 刪除白名單會同時操作 DB 與 Redis。
- 查詢白名單會依登入者 role / agent scope 限制可查範圍。
- 新增 / 刪除成功會寫 admin operation log。
- sync API 會把特定 merchant source IPs 同步給 prefix agents。
- game-api runtime filter 讀相同 Redis key prefix 判斷來源 IP。
- runtime filter 不在白名單時會回 `NOT_IN_API_WHITE_LIST`。

## 待確認

- `game_api_login_ip_white` 是否有 DB unique key。
- Redis update 失敗是否有 retry / compensation。
- 是否有從 DB rebuild Redis 的 job 或手動工具。
- production / uat / dev 對白名單 filter 的開關與差異。
- runtime reject 是否有 metrics / alert。
- sync API 是否仍是臨時 hard-coded 工具，或後續已有通用化設計。

## Evidence Level

- Flow: 真實開發過 + code-backed。
- Nick direct evidence: admin-api `#682` commits；game-api `#684` / `WhiteIpFilter` 相關 commits。
- Supporting evidence: team 後續修正可作 current behavior context。
- 不可升級: 完整 security platform、完整 API gateway、完整 WAF / IAM、完整 DB / Redis 強一致 owner。
