# usproject ugsoft-apidoc source notes

更新日期：2026-06-22

本檔整理 `/Users/nick/Git/antplay/ugsoft-apidoc` 對 `/Users/nick/Git/usproject` 的來源安全邊界與初步對應關係。這是 Step 1 + Step 2 的 source triage，不是新的 flow、不新增履歷 claim，也不取代後續 code-backed Flow Track / contribution consolidation。

## 掃描範圍

| 項目 | 結論 |
| --- | --- |
| 文件來源 | `/Users/nick/Git/antplay/ugsoft-apidoc`，本機 VitePress API doc mirror；本身不是 git repo |
| usproject 來源 | `/Users/nick/Git/usproject` |
| 檢視內容 | `ugsoft-apidoc/README.md`、`guide/chap1..chap8/*.html` 的章節結構與代表 API 名稱、`usproject/game-api/README.md`、`admin-api/README.md` / `Standard.md`、`game-job/README.md`、usproject repo 清單 |
| 本輪未做 | 未 fetch usproject 各 repo、未掃 source code、未比對 git history、未確認 API doc mirror 是否為最新版本、未驗證各 API 在 usproject code 的實作位置 |
| 用途 | supporting / learning evidence：API contract、wallet mode、game entry、bet / settle / cancel、transfer order、bonus / free spin、signature、currency / language |
| 非用途 | 不作 Nick direct commit evidence、不作真實開發 claim、不直接更新 `05 / 08` |

## 安全分類

### 可整理進 vault 的內容

以下內容可用抽象說法整理為 usproject 的 source boundary 或後續面試素材：

- 文件定位：這是一份外部 API contract / mirror，可協助理解 game server 與 operator / platform 的對接行為。
- 錢包模式：轉帳錢包與單一錢包的流程差異。
- 通用入口：預登入 / 進入遊戲、遊戲紀錄連結。
- 轉帳錢包 API：轉出前準備、建立轉帳訂單、轉移資產、查餘額、查訂單、查遊戲紀錄。
- 單一錢包 API：查餘額、下注、結算、下注同時結算、取消下注、活動 / bonus。
- 營運 API：免費遊戲 / free spin 贈送。
- 附錄類資訊：簽名算法、多語系、幣別、遊戲清單等 contract vocabulary。

### 只可參考，不宜直接進 vault 的內容

- 本機 mirror refresh command、原始下載指令與外部來源 URL：正式 KB 只需知道這是本機 mirror，不需要保留可操作抓取指令。
- HTML 原文、完整 request / response 範例、完整商戶 / platform / cert / token 類參數：後續只抽象成欄位責任與風險，不複製原文。
- usproject README 裡的 local test token、localhost URL、Authorization header、內部 GitLab 範本連結或可操作指令：只作本機閱讀參考，不進 vault。

### 不得寫入 vault / commit 的內容

- token、Authorization header、API key、商戶金鑰、安全碼、cert 原文。
- 內網 IP、production / pre-production endpoint、客戶或商戶實例資料。
- 可直接操作環境的 curl / wget / SQL / admin URL 原文。
- deploy 指令、環境連線資訊、截圖中的敏感識別資訊。

## API doc 章節摘要

| 章節 | 抽象用途 | 後續可長出的素材 |
| --- | --- | --- |
| `1 概述` | API 簽名、通用參數、錢包模式與準備工作 | signature / encryption / wallet-mode boundary |
| `2 工作流程（轉帳錢包模式）` | 進入遊戲、交易、退出流程 | transfer-wallet state transition / order query / balance check |
| `3 工作流程（單一錢包模式）` | 進入遊戲、交易流程 | seamless wallet bet / settle / cancel boundary |
| `4 通用接口` | 預登入 / 建帳 / token、遊戲紀錄連結 | game entry / auth token / record link |
| `5 轉帳錢包模式接口` | prepare exit、create order、transfer chip、query balance / order / game record | transfer order correctness / query-after-timeout / reconciliation interview case |
| `6 單一錢包模式接口` | balance、bet、settle、bet-and-settle、cancel bet、bonus | bet-settle idempotency / cancel compensation / bonus correctness |
| `7 營運接口` | free spin 贈送 | campaign / reward operation supporting case |
| `8 附錄` | game list、signature、language、currency | i18n / currency / signature contract vocabulary |

## usproject 對應關係

| usproject repo | 與 API doc 的關係 | 目前證據層級 |
| --- | --- | --- |
| `game-api` | README 顯示它是 slot game runtime，包含 math jar loading、game init / bet、本地加解密測試、player / guest token session、錯誤處理、下注通知與 Quartz 補償 memo；可優先對照 API doc 的進入遊戲、單一錢包、bet / settle / cancel flow | 專案存在 / README-backed；未掃 code / git history |
| `game-job` | README 多為模板，但可作後續查通知 retry、Quartz job、補償 / projection 的候選 repo；目前只和 `game-api` README 的業務 memo 建立弱關聯 | 待確認；未掃 code |
| `admin-api` | README 多為模板，`Standard.md` 只確認 admin API coding convention；可作後續 control plane / public record API 入口，但本輪不展開 | 待確認；未掃 code |
| `game-web` / `admin-web` | 可能是 player / admin 操作入口；只作輔助，不當 Backend 主線 | 待確認 |
| `game-push` | 可能支撐 notification / push；本輪未對照 API doc | 待確認 |
| `nbt` / `nbt-playground` | 可能是 game / math / playground 類；本輪未定位 | 待確認 |
| `usproject-deploy` / `usproject-deploy-template` | deploy / environment supporting；只作邊界，不搬敏感資訊 | learning-only / supporting |
| `usproject-workspace` | workspace / source navigation supporting；不反向包裝成 production service | supporting / learning |

## 初步價值判斷

有價值，而且比一般筆記更接近 production flow；但它的價值在於「API contract / integration boundary」，不是個人履歷證據。

可支撐的 Senior / Owner 能力：

- 在讀 `game-api` 前先知道外部 API contract 的 wallet mode 與 state transition。
- 面試時可把 bet / settle / cancel、transfer order、query-after-timeout、bonus / free spin 拆成 money correctness 與 consistency 問題。
- 區分 runtime API、job compensation、admin control plane、frontend entry 與 deploy / workspace supporting repo。
- 避免把 API 文件、README memo、前端測試流程誤寫成 Nick direct development evidence。

暫時不支撐的 claim：

- 不支撐「真實開發過 usproject」。
- 不支撐「主導完整 UGSoft API / slot game platform」。
- 不支撐「完整 wallet / ledger / reconciliation owner」。
- 不支撐「完整 provider integration / operator onboarding owner」。

## 後續步驟建議

2026-06-22 更新：Step 3 已完成，面試素材整理在 [career-interview.md](career-interview.md)。已補 4 個 API contract supporting cases：game entry / wallet mode、single wallet bet / settle / cancel、transfer wallet order / query、Bonus / FreeSpin / contract boundary。這只作口說補強，不新增正式履歷 claim。

若只沿用剛剛 `notion-export` 的方式，下一步是 Step 4：

```text
做 usproject ugsoft-apidoc Step 4，評估是否需要對 05 / 08 做最小 supporting claim 回填；若不適合就只記錄不改。不得新增正式主 claim，完成後 Relationship Check 並 commit。
```

若 Nick 要把 `usproject` 正式納入 Flow Track，則應另開 project-level Step 1 + Step 2：

- Step 1：掃 usproject repo 清單、branch / README / 入口，找 candidate production flows。
- Step 2：比較 candidate flows 的技術點、風險、repo / module 邊界與 evidence 強度。
- 沒有 Step 2 前，不直接建立單條 flow Step 3。

## Relationship Check

本輪事實變更是：`ugsoft-apidoc` 已被歸類為 `/Users/nick/Git/usproject` 的 external API contract / supporting source，並建立 source safety boundary 與初步 repo 對應。

影響判斷：

- `projects/usproject/README.md`：Step 1 + Step 2 已新增 usproject domain 候選入口；Step 3 需要更新狀態、讀檔順序與下一步邊界。
- `projects/source-repo-inventory.md`：Step 1 + Step 2 已加入 `/Users/nick/Git/usproject` 與 `ugsoft-apidoc` 的來源索引；Step 3 不新增 source repo 事實，不需更新。
- `projects/README.md`：Step 1 + Step 2 已加入 usproject source notes 入口；Step 3 只更新 usproject domain 內部讀法，不需更新。
- `05-resume-master-zh.md` / `08-application-autobiography-zh.md`：本輪不新增正式 claim，不更新。
- `04-interview-casebook.md` / `17-salary-negotiation.md`：Step 3 只補 usproject domain 內的 supporting cases，未升級成全域主力 case，不更新。
- `06-todo.md`：本輪是 Nick 明確指定的 Step 3 並已收口；不新增長期待辦。
