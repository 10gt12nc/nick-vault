# iwin_gameserver Architecture Map

更新時間：2026-05-15
掃描等級：Level 1 Flow 掃描
狀態：已補 root module / game module / service instance 邊界
證據層級：專案存在 / code-backed；Nick 貢獻待確認

## 閱讀定位

本文件只用來定位 repo、module、入口、上下游與候選 flow。它不是單條 flow 報告，也不取代 `flow.md`。

## 最小架構圖

```mermaid
flowchart LR
  Client["玩家 client / Unity"] --> Gate["slots-gate\nWebSocket / TCP"]
  Gate --> Center["slots-center\n大廳 / 錢包 / center_http"]
  Gate --> Game["slots-games\n各遊戲 runtime"]
  Game --> Center
  Center --> Dbproxy["slots-dbproxy\nMySQL / Redis proxy"]
  Game --> Dbproxy
  Center --> LogServer["slots-game-log\n遊戲與投注 log"]
  Game --> LogServer
  Center --> Social["slots-social\nmail / social"]
  Admin["app_bi / payment / game_api / third party gateway\n待依 flow 確認"] --> Center
  Center --> Redis["Redis\ncache / config / MQ-like queue"]
  Dbproxy --> MySQL["MySQL\nplayer / log / social tables"]
```

## Module 定位

### Root Maven Modules

根 `pom.xml` 已確認掛載的 module：

| Root module | 角色 | 是否執行期服務 |
| --- | --- | --- |
| `slots-gate` | 玩家連線入口，WebSocket / TCP codec，轉發玩家封包到 center / game | 是 |
| `slots-center` | 大廳、玩家 cache、錢包、center_http、活動、打碼與第三方整合入口 | 是 |
| `slots-dbproxy` | 通用 DB / Redis 查寫代理 | 是 |
| `slots-social` | 郵件 / social 類服務 | 是 |
| `slots-common` | common library：network、Protobuf、Redis、Zookeeper、dao annotation、controller | 否 |
| `slots-game-log` | 遊戲與投注 log writer / cron 類工作 | 是 |
| `slots-games` | game aggregator，底下再掛多個遊戲 module | 部分是 |

### Game Modules

`slots-games/pom.xml` 已確認掛載：

| 類型 | modules |
| --- | --- |
| 共用遊戲層 | `slots-game-common` |
| active game modules | `slots-game2-dfdc`、`slots-game8-slzw`、`slots-game10-ajtx`、`slots-game12-ald`、`slots-game15-mxt`、`slots-game16-jjbx`、`slots-game19-ly`、`slots-game22-xjbs`、`slots-game23-bqtp`、`slots-game27-tgwsj`、`slots-game34-bcbm`、`slots-game35-by`、`slots-game39-tg`、`slots-game40-sgj`、`slots-game48-cb`、`slots-game51-eyd`、`slots-game52-zszw`、`slots-game54-ldjz`、`slots-game57-zfbb`、`slots-game59-cjtx`、`slots-game60-dzpk`、`slots-game61-lp`、`slots-game62-bingo`、`slots-game64-dj`、`slots-game65-ss`、`slots-game66-db`、`slots-game82-dice2`、`slots-game83-crash2` |
| present but commented in `slots-games/pom.xml` | `slots-game7-jzbx`、`slots-game9-wlcs`、`slots-game11-jktb`、`slots-game13-rxbq`、`slots-game25-ykr`、`slots-game26-ytdh`、`slots-game28-ymghd`、`slots-game30-bjl`、`slots-game41-csd`、`slots-game49-wxhh`、`slots-game58-mhzy`、`slots-game63-truco`、`slots-game67-domino`、`slots-game68-dice`、`slots-game69-crash`、`slots-game71-trucom`、`slots-game81-double2` |

### Repo 內存在但邊界不同

| 目錄 | 狀態 | 本輪處理方式 |
| --- | --- | --- |
| `slots-robot` | repo 內有 `pom.xml`，但未掛在 root `pom.xml` modules | 暫列待確認，不當作主線候選 flow |
| `slots-tools` | 離線工具 / SQL / proto 相關目錄，未掛 root pom | 只作 schema / tooling 線索，不當 runtime flow |
| `service/**` | 各服務 instance config，包括 center、gate、dbproxy、log、social、各 game service | 只記錄 instance 邊界，不寫環境細節 |
| `conf/json/**` | 遊戲與 runtime config | 單條遊戲 flow 深掃時再讀 |

| Module | 角色 | Senior / Owner 關注 |
| --- | --- | --- |
| `slots-gate` | 玩家連線入口、WebSocket / TCP codec、轉發到 center / game | connection state、routing、封包序號、防攻擊、backpressure |
| `slots-center` | 大廳、玩家 cache、center_http、錢包與活動 / 打碼 / 第三方整合入口 | money correctness、state transition、transaction boundary、idempotency |
| `slots-games` | 各遊戲 runtime 與共同遊戲抽象 | spin / bet / settle、RTP、機率、玩家狀態、投注流水 |
| `slots-dbproxy` | MySQL / Redis 查寫代理 | cache-aside、write path、dynamic SQL、partial failure |
| `slots-game-log` | log writer / cron 類工作 | betting log、reconciliation、reporting source |
| `slots-social` | 郵件 / social 類服務 | 輔助功能，暫非高優先 |
| `slots-common` | network、Protobuf、Redis、Zookeeper、controller、dao annotation | common infrastructure、服務發現、RPC callback |

## 已確認入口

HTTP / center command：

- `slots-center/src/main/java/com/slots/center/service/HttpService.java`
- 指令包含 `DEPOSIT`、`WITHDRAW`、`SET_BET_TARGET`、`QUERY_BET_TARGET`、`NOTICE`、`ANTPLAY_BET`、`ANTPLAY_SETTLE`、`ANTPLAY_REFUND`、`ANTPLAYTRANSFERINOUT`、`PGTRANSFERINOUT`、`GSC_BET`、`GSC_SETTLE`、`GSC_REFUND`、`GSC_OTHER`。

DB proxy：

- `slots-common/src/main/java/com/slots/common/controller/DbproxyController.java`
- `slots-dbproxy/src/main/java/com/slots/dbproxy/controller/DbController.java`
- `slots-dbproxy/src/main/java/com/slots/dbproxy/database/mapper/DbproxyMapper.java`

第三方遊戲 / 投派整合：

- `slots-center/src/main/java/com/slots/sql/job/HttpAntplayTransferInOut.java`
- `slots-center/src/main/java/com/slots/sql/job/HttpPGTransferInOut.java`
- `slots-center/src/main/java/com/slots/sql/job/HttpGSCTransferInOut.java`
- `slots-center/src/main/java/com/slots/center/job/http/AntplayTransferInOutJob.java`
- `slots-center/src/main/java/com/slots/center/job/http/PGTransferInOutJob.java`
- `slots-center/src/main/java/com/slots/center/job/http/GSCTransferInOutJob.java`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/AddCenterCoinAP.java`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/AddCenterCoinPG.java`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/AddCenterCoinGSC.java`

遊戲 runtime / spin 共同層：

- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/GamePlayer.java`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/data/AddCenterCoin.java`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/job/c2s/**`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/ruler/**`
- `slots-games/slots-game-common/src/main/java/com/slots/game/common/def/**`

## 跨專案關聯

已確認：

- `payment` 會透過 center_http 呼叫 `DEPOSIT`、`WITHDRAW`、`SET_BET_TARGET`、`QUERY_BET_TARGET`、`NOTICE`。
- `game_api` 會透過 center_http 查玩家、上分、下分，且不一定經過 `payment`。
- `app_bi` 可透過 center_http / Redis / GM command 影響 runtime 設定或查詢。

待確認：

- 第三方遊戲整合上游實際 repo 與 callback / request adapter。
- 每條 flow 的唯一請求 ID / transaction ID / bet ID 是否有全鏈路 idempotency。
- gameserver log 與 payment / game_api / third_games_api 對帳欄位如何對齊。

## 不可誇大的地方

- 本地 code 確認功能存在，不等於 Nick 真實開發過。
- workspace 舊分析只作參考，不當成履歷 evidence。
- 本文件不含 production URL、內網 IP、token、客戶資料。
- `iwin_gameserver` 不是單一服務，未來單條 flow 必須寫清楚跨到哪些 root module、哪些 game module、哪些 service instance；不能用一張大圖含糊帶過。
