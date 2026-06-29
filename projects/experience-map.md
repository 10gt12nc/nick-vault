# Experience Map

日期：2026-06-29

狀態：`v0 interview entry`。這份不是履歷、不是能力整理、不是 flow evidence、不是 repo capability audit；它只用來把 Nick 做過 / 可能做過的公司系統先訪談出來。

## 目的

Nick 目前真正卡住的點不是「不知道學什麼」，而是：

```text
有很多真實經驗，但還沒有先整理成「我做過哪些系統」。
```

所以本檔先做 `Experience Map`，順序固定是：

```text
Experience
-> system / repo / module interview
-> 本人確認
-> 才能進一步整理 capability / resume / interview story / learning gap
```

目前禁止：

- 不幫 Nick 寫履歷。
- 不整理能力。
- 不預設 Payment 最重要。
- 不把 git trace 自動等同於 production ownership。
- 不把 local / migration / playground 寫成 production experience。
- 不寫入 remote URL、token、內網敏感資訊。

## 本輪掃描方式

範圍：

```text
/Users/nick/Git
排除 /Users/nick/Git/nick
```

只看公司相關 repo 的 git author / committer metadata，作者關鍵字：

```text
10gt12nc
Nick
nick
我
```

Nick 已確認這些身份都代表 Nick 本人。本輪沒有讀 code、沒有查 path-specific git history、沒有更新履歷 / 自傳 / Story / 正式 flow。

證據層級：`git author / committer trace`。它只是 Experience Map 訪談入口，後續仍要用 Nick 本人確認區分：

- production 實際做過
- local / migration / playground
- code-backed 讀過 / 維護過
- learning-only
- 不確定 / 待查

## v0 掃描摘要

本輪掃到有 Nick 身份痕跡的 repo：

| Domain | repo 數 | matched commits |
| --- | ---: | ---: |
| antplay | 112 | 2174 |
| iwin | 21 | 989 |
| ugsoft | 2 | 432 |
| usproject | 55 | 1646 |
| DevOps | 0 | 0 |

說明：

- `DevOps = 0` 只代表本輪 author / committer metadata 沒命中；不代表 Nick 沒接觸過 DevOps。若 Nick 本人確認做過，後續可用 `本人確認` 補進訪談。
- `math` repo 數量很多，需後續 group 成 math family，不逐一變成履歷素材。
- workspace / `.work` / `Projects` 裡有 duplicate checkout，訪談時要區分 canonical repo 與 workspace copy。

## Experience Map v0

### 1. AntPlay 系統群

主要 repo / group：

| Repo / group | 類型 | matched commits |
| --- | --- | ---: |
| `antplay-slot-admin-api` | admin / control plane | 198 |
| `antplay-slot-game-api` | slot runtime API | 159 |
| `antplay-slot-game-job` | job / batch / report | 52 |
| `math-core` | math core | 20 |
| `antplay-push` | push / realtime | 9 |
| `platform-mock` | mock / failure test | 6 |

主要 math repo / group：

| Repo | matched commits |
| --- | ---: |
| `sph-math` | 125 |
| `spn-math` | 116 |
| `sfm-math` | 80 |
| `setl-math` | 69 |
| `sdt-math` | 68 |
| `slc-math` | 66 |

訪談重點先問：

- AntPlay Slot Platform 到底包含哪些子系統？
- `slot-game-api`、`slot-game-job`、`slot-admin-api` 各自做什麼？
- math repo 是修 bug、調 RTP、支援 buy free、幣別、debug bet、fixed multi bet，還是批次套改？
- 哪些是 production 實際上線？哪些只是維護、讀 code、測試或支援？

### 2. iwin 系統群

主要 repo：

| Repo | 類型 | matched commits |
| --- | --- | ---: |
| `payment` | payment provider | 100 |
| `iwin_gameserver` | game server / wallet / game runtime | 98 |
| `game_job` | job / summary / batch | 45 |
| `game_api` | player / partner API | 15 |
| `payment-thirdparty-simulator` | simulator | 13 |
| `app_bi` | BI / admin | 4 |
| `third_games_api` | third-party game API | 3 |

判斷：

`payment` 和 `iwin_gameserver` 的 git trace 接近；後續不應再把 iwin 經驗自動理解成 payment-only。

訪談重點先問：

- `payment` 到底做過哪些 provider / callback / query / withdraw？
- `iwin_gameserver` 到底做過哪些 wallet / transfer / bet / settle / game runtime？
- `game_job` 做過哪些 summary / backup / projection / batch？
- `game_api`、`third_games_api`、`app_bi` 是實作、維護、測試還是讀 code？

### 3. UGSoft 系統群

主要 repo：

| Repo | 類型 | matched commits |
| --- | --- | ---: |
| `ugsoft-connector-api` | provider connector / gateway | 222 |
| `ugsoft-admin-api` | admin / control plane | 210 |

判斷：

UGSoft 很適合當 Experience Map 第一章，因為 repo 少、commit trace 高、系統邊界相對清楚。

訪談重點先問：

- `connector-api` 做什麼？
- `admin-api` 做什麼？
- 兩者如何配合？
- Nick 實際做過 transfer wallet、provider config、request log、bet record、RabbitMQ、white IP、job sync 哪些部分？

### 4. usproject 系統群

主要 repo / group：

| Repo / group | 類型 | matched commits |
| --- | --- | ---: |
| `admin-api` | admin / backend API | 198 |
| `game-api` | game runtime API | 160 |
| `nbt` | Java 21 / migration / game service | 82 |
| `game-job` | job / batch | 52 |
| `game-push` | push / realtime | 9 |
| `game-web` | game frontend / nginx context | 8 |
| `usproject-deploy` | deploy | 6 |

workspace / local-dev traces：

| Repo / group | matched commits |
| --- | ---: |
| `.work/admin-api` | 199 |
| `.work/game-api` | 175 |
| `.work/nbt` | 103 |
| `.work/nbt-playground` | 51 |

訪談重點先問：

- 哪些是正式 production？哪些是 local rewrite / migration / playground？
- `admin-api`、`game-api`、`nbt` 分別是什麼系統？
- `game-push`、`game-job`、`usproject-deploy` 是否只是 context，還是 Nick 實際做過？
- 這組是否要晚一點訪談，避免混入近期 migration / local validation 與正式 production 經驗。

### 5. DevOps

本輪未掃到 Nick author / committer direct trace。

訪談處理：

- 先不列入 Experience Map 第一批主線。
- 若 Nick 本人確認某些 DevOps repo 是自己做過，再用 `本人確認` 補進來。
- 目前先當 deploy / observability / platform context，不作 direct experience。

## 建議訪談順序

這不是能力排序，也不是履歷排序；只是先把做過的世界問出來。

1. UGSoft
   - `ugsoft-connector-api`
   - `ugsoft-admin-api`
2. AntPlay Slot Platform
   - `antplay-slot-game-api`
   - `antplay-slot-game-job`
   - `antplay-slot-admin-api`
3. AntPlay Math
   - `math-core`
   - `sph / spn / sfm / setl / sdt / slc`
   - 其他小改 math packages
4. iwin
   - `payment`
   - `iwin_gameserver`
   - `game_job`
   - `game_api`
   - `third_games_api`
   - `app_bi`
5. usproject
   - `admin-api`
   - `game-api`
   - `nbt`
   - `game-job`
   - `game-push`
   - `deploy`

## 每個系統訪談格式

每個系統只問這些，不抽能力：

```text
1. 這個系統是做什麼的？
2. 主要使用者是誰？
3. 你實際做過哪些 repo / module？
4. 你做過哪些功能 / bug / 調整？
5. 哪些是 production？
6. 哪些是 local / migration / playground？
7. 哪些是你本人開發？
8. 哪些只是讀過 / 維護 / 排查？
9. 哪些你印象最深？
10. 哪些你現在不確定，需要之後查 code / git？
```

## 暫停線

Experience Map 目前只做訪談入口，不進入下列工作：

- 不整理履歷。
- 不整理能力。
- 不寫 Story。
- 不新增 flow。
- 不開 weekly learning。
- 不深掃全部 repo。
- 不把 repo 數量變成新的焦慮 backlog。

只有當某個系統訪談完成，且 Nick 明確要求，才進一步做 capability extraction、resume mapping 或 interview story。
