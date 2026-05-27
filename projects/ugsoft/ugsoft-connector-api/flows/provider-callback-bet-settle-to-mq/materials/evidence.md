# provider-callback-bet-settle-to-mq Evidence

## 任務

- 任務：`ugsoft ugsoft-connector-api provider-callback-bet-settle-to-mq Step 3`
- 日期：2026-05-27
- 掃描等級：Level 2 Flow 深掃。
- 目標：建立 provider callback / bet-settle / MQ producer + downstream consumer 的 flow evidence。

## KB / Vault 已重讀

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`
- `projects/ugsoft/ugsoft-connector-api/README.md`
- `projects/ugsoft/ugsoft-connector-api/step1-candidate-flows.md`
- `projects/ugsoft/ugsoft-connector-api/step2-flow-comparison.md`
- `projects/ugsoft/ugsoft-connector-api/contribution-claim-consolidation.md`
- `projects/ugsoft/ugsoft-connector-api/flows/transfer-wallet-in-out-query/*`

既有文件判斷：

- Step 1 / Step 2 可沿用；Step 2 已把本 flow 排第二順位。
- 第一條 `transfer-wallet-in-out-query` 已完成 Step 5；本輪依規則回到同 project 第二條 flow。
- 本 flow 之前沒有 `flows/provider-callback-bet-settle-to-mq/`，本輪新增 Step 3 learning package。

## Source Repo 狀態

### ugsoft-connector-api

來源 repo：

```text
/Users/nick/Git/ugsoft/ugsoft-connector-api
```

遠端 / 本機狀態：

- 已執行 `git fetch --all --prune`。
- local branch：`Nick_Test`
- local HEAD：`c2cab730c0cd6ead6d92a038ef56f97987577059`
- `origin/master`：`4bd2195e1e574978f11a1d4b5e744792f16ecad0`
- `origin/develop`：`079aa6603b50db3c185e383295ca5966bbe272fb`
- local vs `origin/master`：ahead / behind = `0 / 61`
- local vs `origin/develop`：ahead / behind = `190 / 0`
- source repo 工作樹不乾淨：有 `.DS_Store`、test、docs 等既有 local changes / untracked files。本輪只讀 remote objects / `origin/master` / git history，不採 source repo 髒檔作正式 evidence。

### ugsoft-admin-api

來源 repo：

```text
/Users/nick/Git/ugsoft/ugsoft-admin-api
```

遠端 / 本機狀態：

- 已執行 `git fetch --all --prune`。
- local branch：`main`
- local HEAD：`0cc62e0e1a040e69b1650079d9ecfe92dd64380d`
- `origin/develop`：`ad4ee3704eec0cb2c3542ff52f6a50180b6825ab`
- local vs `origin/develop`：ahead / behind = `793 / 2`
- `origin/master` 在本機 refs 中未解析成功；本輪只用本地工作樹與 fetched refs 做 downstream context。

## Connector 已掃 code path

- `src/main/java/com/ps/domain/connector/controller/ConnectCallbackController.java`
- `src/main/java/com/ps/domain/connector/service/ConnectCallbackService.java`
- `src/main/java/com/ps/domain/connector/service/CallbackDerplayService.java`
- `src/main/java/com/ps/domain/connector/adapter/AdapterCallback.java`
- `src/main/java/com/ps/domain/connector/service/ConnectAntplayAdapter.java`
- `src/main/java/com/ps/domain/connector/service/ConnectBetRecordMqService.java`
- `src/main/java/com/ps/common/config/RabbitMQConfig.java`
- `src/main/java/com/ps/common/constant/RabbitMq.java`
- `src/main/java/com/ps/domain/connector/vo/ConnectBetRecordMqDto.java`
- `src/main/java/com/ps/domain/connector/vo/ConnectCallbackBetSettleData.java`
- `src/main/java/com/ps/domain/connector/vo/DerplayBetNSettleMessage.java`
- `src/main/java/com/ps/quartz/main/service/ConnectBetRecordSyncService.java` 作關聯 context。

## Downstream 已掃 code path

- `/Users/nick/Git/ugsoft/ugsoft-admin-api/src/main/java/com/ps/config/RabbitMQConfig.java`
- `/Users/nick/Git/ugsoft/ugsoft-admin-api/src/main/java/com/ps/domain/admin/constant/RabbitMq.java`
- `/Users/nick/Git/ugsoft/ugsoft-admin-api/src/main/java/com/ps/domain/admin/rabbitMq/betRecord/ConnectBetRecordListener.java`
- `/Users/nick/Git/ugsoft/ugsoft-admin-api/src/main/java/com/ps/domain/admin/rabbitMq/betRecord/ConnectBetRecordConsumerService.java`
- `/Users/nick/Git/ugsoft/ugsoft-admin-api/src/main/java/com/ps/domain/admin/rabbitMq/betRecord/BetRecordMqPayload.java`
- `/Users/nick/Git/ugsoft/ugsoft-admin-api/src/main/java/com/ps/domain/game/slot/data/entity/BetRecord.java`

## Git Evidence

### Nick / 10gt12nc direct evidence - connector producer

| Commit | Author | 意義 |
| --- | --- | --- |
| `59121a3` | `10gt12nc` | AntPlay bet_settle callback 回傳格式初期 evidence。 |
| `e95b353` | `10gt12nc` | `单一钱包：回调时候写mq`，新增 / 調整 RabbitMQ config、`ConnectBetRecordMqService`、`ConnectCallbackService`、`CallbackDerplayService`、MQ DTO。 |
| `120c0fb` | `10gt12nc` | callback 寫 MQ log。 |
| `a173cf3` | `10gt12nc` | `feat: mq`，包含 `ConnectBetRecordMqService`、AntPlay / DerPlay bet record sync job、repository。 |
| `df1fdcf` | `10gt12nc` | `fix: mq Derplay 單一錢包 日期`，DerPlay MQ 日期 / time 相關修正。 |
| `3e9d0d0` | `10gt12nc` | `feat: mq 跨日 pt_day`，跨日 / pt_day 查重 context。 |
| `b6329a8` | `10gt12nc` | `fix: job多 單 BetRecordMq 01`，callback / MQ / job 多處修正。 |
| `5747ff3` / `ac6d25c` / `edf8f26` | `10gt12nc` | job MQ currency default / currency 修正。 |

### Nick / 10gt12nc direct evidence - admin consumer

| Commit | Author | 意義 |
| --- | --- | --- |
| `98ad763` | `10gt12nc` | `feat: BetRecord ＭＱ入庫 01`，新增 admin-api `ConnectBetRecordListener` / `ConnectBetRecordConsumerService` / duplicate support / MQ payload。 |
| `f641b04` | `10gt12nc` | `fix: job多 單 BetRecordMq 01`，admin consumer / duplicate support 修正。 |
| `821bc2e` | `10gt12nc` | `feat: BetRecord、requestLog Mq`，BetRecord / requestLog MQ 入庫相關。 |
| `c99a325` | `10gt12nc` | `fix: job MQ currency 預設幣別為 CNY`，admin consumer currency default。 |

### 主管 / 團隊 context，不當 Nick direct evidence

| Commit | Author | 意義 |
| --- | --- | --- |
| `6e1b755` | `arnold` | 將 callback controller 邏輯移到 service。 |
| `984910a` | `arnold` | AntPlay callback 驗簽改用 antplayAgentId 作 sign content。 |
| `9ecf0a0` | `arnold` | AntPlay BetSettle callback sign rule / fields 更新。 |
| `054dc7e` | `arnold` | DerPlay betSettle 修正。 |
| `185f4c5` | `arnold` | betSettle error code propagation；失敗時不送 MQ 的正確性修正。 |
| `5070ec5` / `1f063a0` / `a0ffed0` | `arnold` | DerPlay / AntPlay amount scaling 修正。 |
| `73d40f6` | `arnold` | callback provider IP whitelist。 |
| `ea19bbe` | `arnold` | AntPlay callback request log 改用 connectorAgentId。 |
| `fde25c0` / `6f8c707` | `arnold` | 二級代理 subAgent / response rewrite。 |

Nick 已確認 `arnold` 是主管帳號；上述只能作 current behavior / team context。

## Code Evidence 摘要

### ConnectCallbackController

已確認：

- AntPlay info / balance / bet_settle endpoint。
- DerPlay form endpoint。
- Provider IP whitelist 檢查。
- finally 裡寫 callback request log。
- AntPlay request log 使用 connectorAgentId，而不是 provider 自己 agentId。

### ConnectCallbackService / CallbackDerplayService

已確認：

- AntPlay callback 驗簽。
- balance / betSettle wallet type boundary。
- adapter callback 成功才送 MQ。
- DerPlay cert -> token -> currency。
- DerPlay message parse / validation。
- DerPlay bet amount / win amount normalization。

### ConnectBetRecordMqService

已確認：

- `sendAntplayBetSettle` mapping AntPlay DTO。
- `sendDerplayBetSettle` mapping DerPlay DTO。
- `send` publish to direct exchange / routing key。
- publish exception catch log，不往外拋。

### RabbitMQ

Connector producer：

- `RabbitMq.CONNECT_BET_RECORD_EXCHANGE = ugSoftConnectBetRecord.direct`
- `RabbitMq.CONNECT_BET_RECORD_ROUTING_KEY = ugSoftConnectBetRecord`
- `RabbitMq.CONNECT_BET_RECORD_QUEUE_KEY = ugSoftConnectBetRecord`
- `RabbitMQConfig` 建 direct exchange、durable queue、binding。

Admin consumer：

- `ConnectBetRecordListener` 用 `@RabbitListener(queues = "#{connectBetRecordQueue.name}")`。
- `ConnectBetRecordConsumerService` parse payload、validate key fields、duplicate check、save `BetRecord`、publish quota update。

## 已確認

- 本 flow 確實是跨 repo：connector-api producer + admin-api consumer。
- Nick / `10gt12nc` 有 direct commits 覆蓋 producer MQ、consumer MQ、currency / pt_day / duplicate 相關修正。
- Current code 仍有 team context changes，不能只用 current blame 判斷 Nick 沒做過。
- Current behavior 沒有看到完整 outbox / exactly-once。

## 待確認

- production 實際部署 branch。
- Rabbit listener ack / retry / DLQ 設定。
- MQ publish failure 是否有外部 alert。
- callback request log 是否足以支撐 manual replay。
- quota update consumer 的完整 failure handling。
- 是否有 ticket / incident 能證明 Nick 處理 production issue。
