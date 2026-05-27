# Evidence - game-api-provider-white-ip-control-plane Step 3 / Step 4

日期: 2026-05-27

## 本輪任務

`ugsoft ugsoft-admin-api game-api-provider-white-ip-control-plane Step 3`

## Step 4 補充掃描

本輪任務: `ugsoft ugsoft-admin-api game-api-provider-white-ip-control-plane Step 4`。

掃描等級:

- Level 2 面試轉換。
- 已重讀 Step 3 `flow.md`、`career-interview.md`、`materials/evidence.md`、`materials/interview.md`、`materials/claim-boundary.md`、`materials/decision-notes.md`。
- 已再次 fetch `ugsoft-admin-api` 與 `ugsoft-connector-api` remote refs；只更新 refs，不 pull、不 checkout、不改 source repo。
- 已重看 latest `origin/main` / `origin/master` 的白名單 controller、service、mapper、connector filter、provider cache listener / cache service / callback controller。
- Step 4 沒有新增 Nick direct commit evidence；本輪目標是把 Step 3 evidence 轉成正式 30 秒 / 90 秒 / 3 分鐘、STAR、Senior / Lead 追問、反問與誇大陷阱。
- Step 4 不更新 `05 / 08 / 04 / 17`；下一步 Step 5 才做單條 flow claim gate。

Step 4 source repo 狀態:

- `ugsoft-admin-api`: fetch 成功；local branch `main`，local HEAD `0cc62e0e1a040e69b1650079d9ecfe92dd64380d`，`origin/main` `b1b83f64ffc971cc838ef935867a5a2234e3d201`，ahead / behind `0 / 42`，working tree 乾淨。
- `ugsoft-connector-api`: fetch 成功；local branch `Nick_Test`，local HEAD `c2cab730c0cd6ead6d92a038ef56f97987577059`，`origin/HEAD` 指向 `origin/develop`，本輪下游 runtime snapshot 仍以 `origin/master` `4bd2195e1e574978f11a1d4b5e744792f16ecad0` 為準；source working tree 有既有 `.DS_Store`、test、docs local changes / untracked，本輪只讀，不採用髒檔。

Step 4 事實變更:

- `career-interview.md` 已從 Step 3 草稿升級為 Step 4 正式面試稿，補 STAR、Senior / Lead 追問、反問面試官與 Step 4 結論。
- `materials/interview.md` 已從 Step 3 草稿升級為 Step 4 正式面試材料。
- `materials/decision-notes.md` 補上 setting / propagation / enforcement 三層決策框架。
- `materials/claim-boundary.md` 狀態改為 Step 4 完成；仍不直接更新 `05 / 08`。

## 掃描等級

- 本次深度: Level 2 Flow 深掃。
- 理由: Step 2 已選定單條 flow，本輪需要追 controller / service / mapper / Redis / RabbitMQ / 下游 runtime context 與 path-specific history。
- 未做 Level 3: 未逐檔逐行掃全 repo、未逐一展開所有 commit diff、未驗證 production broker / DB DDL / admin-web UI / 真實 incident。

## Vault 重讀範圍

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/ugsoft/ugsoft-admin-api/README.md`
- `projects/ugsoft/ugsoft-admin-api/step1-candidate-flows.md`
- `projects/ugsoft/ugsoft-admin-api/step2-flow-comparison.md`
- `projects/ugsoft/ugsoft-admin-api/contribution-claim-consolidation.md`
- 前兩條代表 flow 的 `flow.md`

既有 Step 狀態: 可沿用。Step 1 / Step 2 已符合目前 KB，且明確指定本 flow 為第三代表 flow。前兩條 flow 已完成 Step 5。

## Source Repo 狀態

### ugsoft-admin-api

來源 repo:

```text
/Users/nick/Git/ugsoft/ugsoft-admin-api
```

遠端狀態:

- 已執行 `git fetch --all --prune`；第一次因 sandbox `.git/FETCH_HEAD` 權限失敗，approval 後成功。
- local branch: `main`
- local HEAD: `0cc62e0e1a040e69b1650079d9ecfe92dd64380d`
- `origin/main`: `b1b83f64ffc971cc838ef935867a5a2234e3d201`
- ahead / behind: `0 / 42`
- source working tree: 乾淨
- 判斷方式: local working tree + fetched `origin/main` file snapshots + path-specific history。因 local 落後 `origin/main` 42 commits，2026-04 後 `arnold` commits 只作 current behavior / 主管 context，不當 Nick direct evidence。

### ugsoft-connector-api

來源 repo:

```text
/Users/nick/Git/ugsoft/ugsoft-connector-api
```

遠端狀態:

- 已執行 `git fetch --all --prune`；第一次因 sandbox `.git/FETCH_HEAD` 權限失敗，approval 後成功。
- local branch: `Nick_Test`
- local HEAD: `c2cab730c0cd6ead6d92a038ef56f97987577059`
- 本 repo 沒有 `origin/main`；`origin/HEAD` 指向 `origin/develop`，本輪下游 latest context 以 `origin/master` 為主要 runtime snapshot。
- `origin/master`: `4bd2195`
- local `Nick_Test` / local `master` 相對 `origin/master` 落後；source working tree 有既有髒檔與未追蹤 docs / tests，本輪只讀，不改。
- 下游只作 code-backed context，不當 admin-api direct evidence。

## ugsoft-admin-api 掃描路徑

- `src/main/java/com/ps/domain/admin/controller/GameApiWhiteIpController.java`
- `src/main/java/com/ps/domain/admin/service/GameApiWhiteIpService.java`
- `src/main/java/com/ps/domain/admin/service/impl/GameApiWhiteIpServiceImpl.java`
- `src/main/java/com/ps/domain/admin/mapper/GameApiWhiteIpMapper.java`
- `src/main/resources/mapper/GameApiWhiteIpMapper.xml`
- `src/main/java/com/ps/domain/admin/controller/ProviderWhiteIpController.java`
- `src/main/java/com/ps/domain/admin/service/ProviderWhiteIpService.java`
- `src/main/java/com/ps/domain/admin/service/impl/ProviderWhiteIpServiceImpl.java`
- `src/main/java/com/ps/domain/admin/mapper/ProviderWhiteIpMapper.java`
- `src/main/resources/mapper/ProviderWhiteIpMapper.xml`
- `src/main/java/com/ps/common/constant/RedisKey.java`
- `src/main/java/com/ps/domain/admin/constant/RabbitMq.java`

## ugsoft-connector-api 下游掃描路徑

- `origin/master:src/main/java/com/ps/common/filter/WhiteIpFilter.java`
- `origin/master:src/main/java/com/ps/common/config/RabbitMQConfig.java`
- `origin/master:src/main/java/com/ps/common/rabbitMq/ProviderWhiteIpCacheListener.java`
- `origin/master:src/main/java/com/ps/domain/manage/service/ProviderWhiteIpCacheService.java`
- `origin/master:src/main/java/com/ps/domain/connector/controller/ConnectCallbackController.java`
- `origin/master:src/main/java/com/ps/common/constant/RabbitMq.java`
- `origin/master:src/main/java/com/ps/common/constant/RedisKey.java`

## Path-specific commit evidence

| Commit | 日期 | Author | 判斷 |
| --- | --- | --- | --- |
| `f90a1eb` | 2025-08-06 | `10gt12nc` | Game API white IP 初版，新增 controller / service / mapper / Redis key / operation log，Nick direct evidence |
| `6cf3855` | 2025-08-07 | `10gt12nc` | Game API delete / Redis delete 調整，Nick direct evidence |
| `4c86d96` | 2025-08-07 | `10gt12nc` | agentId 前端必傳，不再視為 nullable，Nick direct evidence |
| `1dd366c` | 2025-08-07 | `10gt12nc` | 狀態大小寫修正，Nick direct evidence |
| `2fb2ce5` | 2026-03-31 | `10gt12nc` | Provider white IP table / controller / service / mapper 初版，Nick direct evidence |
| `caf9fe7` | 2026-04-29 | `arnold` | Provider CRUD 後 publish fanout 通知 connector reload，主管 / current behavior context |
| `30fff32` | 2026-04-29 | `arnold` | Provider white IP 移除 agentId、改成全站共用，主管 / current behavior context |

## 已確認

- Game API white IP 後台新增時會查 `agent_id + ip` 是否重複，寫 `game_api_login_ip_white`，再同步 Redis set。
- Game API white IP 後台刪除時會 delete DB，再從 Redis set 移除 IP。
- Game API 查詢有 role / agent scope 處理，使用 `AgentRoleUtil#processAgentIds`。
- Game API 成功新增 / 刪除會寫 admin operation log。
- `WhitelistSync` 會用 source merchant 的 IP 同步到 prefix matching agents，更新 Redis 與 DB。
- Provider white IP 初版有 Nick direct evidence；latest `origin/main` 移除 agent scope，改成 `provider + ip` 全站共用 duplicate。
- Provider latest add / delete 後會 publish `providerWhiteIpCache.fanout`，但這是 `arnold` context。
- connector-api 有 Game API Redis white IP filter，以及 Provider White IP fanout listener / cache service / callback check context。

## 合理推論

- Game API Redis set 是 runtime filter 的快速讀取 view，DB 是設定 source of truth。
- Provider latest fanout reload 是為了讓 connector callback hot path 不每次查 DB。
- DB + Redis / fanout 不是原子交易，必須接受或修補 eventual consistency。

## 待確認

- `game_api_login_ip_white` / `provider_login_ip_white` 是否有 DB unique constraint。
- production Redis / RabbitMQ 實際 retry、monitoring、reload failure alert。
- admin-web UI 是否限制 provider / IP 格式與操作權限。
- `WhitelistSync` 若 Redis side effect 成功但 DB transaction rollback，實際恢復方式。
- provider fanout exchange 在 admin-api 是否有 config bean；本輪只看到常數與 publish，未完整驗證 broker 宣告端。

## Relationship Check

本輪事實變更:

- `ugsoft-admin-api game-api-provider-white-ip-control-plane` Step 4 已完成。
- `ugsoft-admin-api` 本批第三條代表 flow 已進入 Flow Track，尚未完成 Step 5。
- 不直接更新 `05 / 08 / 04 / 17`。

需要同步:

- `projects/ugsoft/ugsoft-admin-api/README.md`
- `projects/ugsoft/ugsoft-admin-api/step1-candidate-flows.md`
- `projects/ugsoft/ugsoft-admin-api/step2-flow-comparison.md`
- `projects/ugsoft/README.md`
- `projects/source-repo-inventory.md`
- `projects/source-repo-flow-audit.md`
- `senior-owner-playbook/01-senior-owner-flow-inventory.md`
- `senior-owner-playbook/06-todo.md`
- `senior-owner-playbook/13-code-capability-map.md`

不更新:

- `05-resume-master-zh.md` / `08-application-autobiography-zh.md`: Step 4 不直接更新履歷。
- `04-interview-casebook.md`: 本輪先不更新全域 casebook；Step 4 正式面試稿已在 flow-level `career-interview.md` 與 `materials/interview.md`，等 Step 5 或 rolling resume package 再判斷是否回填全域 casebook。
- `17-salary-negotiation.md`: 沒有薪資策略變更。
