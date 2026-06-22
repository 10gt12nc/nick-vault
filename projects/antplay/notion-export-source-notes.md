# AntPlay Notion export source notes

更新日期：2026-06-22

本檔整理 `/Users/nick/Git/antplay/notion-export` 的安全使用邊界與 supporting evidence 對照。這是 Step 1 + Step 2 的 source triage，不是新的 flow、不新增履歷 claim，也不取代既有 code-backed flow / contribution consolidation。

## 掃描範圍

| 項目 | 結論 |
| --- | --- |
| 來源 | `/Users/nick/Git/antplay/notion-export` |
| 檢視內容 | `README.md`、`pages/*.md` 的頁面清單、標題、代表內容與高價值頁面 |
| 本輪未做 | 未掃 AntPlay source code、未 fetch source repo、未比對 git history、未驗證 Notion export 是否為最新營運文件 |
| 用途 | supporting / learning evidence：API contract、商戶對接脈絡、request log 排查、風控監控、wallet mode / connector 切換風險 |
| 非用途 | 不作 direct commit evidence、不作 Nick 真實開發 claim、不直接更新 `05 / 08` |

## 安全分類

### 可整理進 vault 的內容

這類內容可以用重新整理後的抽象說法放進 AntPlay domain 文件或面試素材：

- Game API contract：`signTime`、簽章、金額格式、UTC timestamp、JSON header、錯誤碼、版本演進。
- Integration mode：單一錢包、轉帳錢包、投派整合、投派分離、bet / settle / rollback / bet-settle callback 的模式差異。
- Request log troubleshooting：從 request log、response body、error message、timeout window 判斷上游 / 下游問題。
- Merchant / admin control plane：商戶建立、商戶帳號、白名單、後台角色與營運查詢的工作脈絡。
- Risk monitor：商戶 RTP / 玩家 RTP exceed 的觀測條件、時間窗口與監控用途。
- Wallet / connector migration：切換前維護、原設定備份、URL / callback 配置、切換後恢復營運的風險意識。
- Game feature / RTP context：遊戲 RTP、hit rate、volatility、free game、max win 等 domain vocabulary，只作理解 slot domain，不作完整 math owner claim。

### 只可參考，不宜直接進 vault 的內容

這類內容可以幫助理解系統，但不建議原文搬入正式 KB：

- 架構圖截圖與外部圖檔連結：可作比對 existing architecture map 的參考，但不直接複製內部連結。
- 部署指令與 deploy package：只作環境脈絡，不轉成 DevOps / rollout claim。
- 後台操作截圖：只作理解 admin control plane，不當作 Nick direct evidence。
- 測試操作指令、一次性 SQL、UAT case：只作 troubleshooting / validation 參考，正式 KB 只保留抽象場景。

### 不得寫入 vault / commit 的內容

這類內容只能留在本機來源，不得搬進 Markdown：

- 帳號、密碼、API key、token、Authorization header、商戶金鑰。
- 內網 IP、正式環境 IP、production / pre-production endpoint、客戶或商戶實例資料。
- 可直接操作環境的 curl / SQL / admin URL 原文。
- deploy zip、外部共享連結、截圖中的敏感識別資訊。

## Source 對照

| Notion export 頁面 | 可抽象成 | 對應既有 AntPlay KB | Claim 邊界 |
| --- | --- | --- | --- |
| `Antplay API文件` 與各版 `Antplay 游戏API文件` / `Game API Document` | Game API contract、版本演進、錢包 / 投派模式、callback contract | [integration-map.md](integration-map.md) 的下注 / 派彩 / rollback runtime、Admin / merchant control | supporting / learning；不新增完整 provider integration owner claim |
| `资料流log盘查` | request log troubleshooting、error_message / response_body / timeout 排查 | [integration-map.md](integration-map.md) 的 Request log / async audit、Trace Map | 可作面試排查能力素材；不宣稱完整 observability platform owner |
| `风控说明` | merchant / player RTP exceed monitor、時間窗口、最低資料量與告警目的 | [architecture-map.md](architecture-map.md) 的 Admin / risk / control plane、[career-interview.md](career-interview.md) 追問防線 | 只作風控監控脈絡；不寫完整 RTP / risk platform owner |
| `新开商户流程` | merchant onboarding、商戶設定、白名單、integration mode 選擇、API validation checklist | [integration-map.md](integration-map.md) 的 Admin / whitelist / merchant control | 只作營運 / 對接 supporting；不寫主導商戶開通 |
| `Antplay 后台操作说明` / `Agent表功能说明` | admin / merchant role、menu、control plane 功能邊界 | [architecture-map.md](architecture-map.md) 的 admin-api 定位 | 後台控制面不等於 runtime owner |
| `Antplay切换UGsoft单一钱包` | wallet / connector 切換流程、維護開關、原配置備份、失敗可回復意識 | [integration-map.md](integration-map.md) 的 wallet / connector / merchant control 邊界 | interview supporting；不寫完整 wallet migration owner |
| `Antplay 游戏特色` | RTP、hit rate、volatility、free game、max win 等 slot domain vocabulary | [architecture-map.md](architecture-map.md) 的 Math / feature contract 主線 | domain learning；不寫完整遊戲數學模型 owner |
| `架构图` | domain map 參考資料、Queue / server / deploy 脈絡 | [architecture-map.md](architecture-map.md) / [integration-map.md](integration-map.md) | 只作 current / team context；不作 Nick direct evidence |

## 可回填的 supporting evidence

本 export 對目前 AntPlay KB 的補強點是「營運 / 對接 / 排查脈絡」，不是新的正式履歷 claim：

- `antplay-slot-game-api` 的 Game API runtime 可以補一層外部 API contract 視角：login、balance、bet、settle、rollback、bet-settle integration、bet record 與 transfer wallet check。
- `antplay-slot-admin-api` 的 merchant control plane 可以補一層營運流程視角：商戶建立、角色 / 白名單、Game API 設定、request log 查詢與風控監控。
- `antplay-slot-game-job` / reporting 類素材可保留為報表 / risk view 的下游脈絡，但本 export 本身不是 job code evidence。
- `math-core` / `*-math` 可吸收 RTP / volatility / hit rate vocabulary，幫助口說 slot domain，但不提升 math owner claim。

## 可面試講，但不可誇大

可講：

```text
我在看 AntPlay 類 slot platform 時，會把 runtime API、商戶 callback、request log、admin control plane、風控監控與 math contract 分開看。像 merchant integration 問題，我會先確認是單一錢包、轉帳錢包、投派整合還是投派分離，再看 request / response、error_message、timeout window 與對應 callback，避免把後台查詢、request log 或 report projection 誤判成交易 source of truth。
```

不可講：

- 不說主導 AntPlay 新商戶開通。
- 不說主導完整 AntPlay / UGSoft wallet migration。
- 不說完整風控平台、完整 RTP 策略或完整遊戲數學 owner。
- 不把 Notion export 當成 Nick direct commit / MR / ticket evidence。
- 不把架構圖 / deploy 指令包裝成完整 DevOps / SRE owner。

## Relationship Check

本輪事實變更是：AntPlay Notion export 已被歸類為 supporting / learning source，並建立安全使用邊界。

影響判斷：

- `projects/antplay/README.md`：需要加入本檔作為 source notes 入口。
- `projects/antplay/architecture-map.md` / `integration-map.md` / `career-interview.md`：現有 claim boundary 已能承接，不需本輪改內容。
- `05-resume-master-zh.md` / `08-application-autobiography-zh.md`：本輪不新增正式 claim，不更新。
- `04-interview-casebook.md` / `17-salary-negotiation.md`：本輪只做 source boundary，不更新。
- `06-todo.md`：本輪是指定 Step 1 + Step 2 並收口，不新增長期待辦。

## Step 4 resume / autobiography evaluation

更新日期：2026-06-22

結論：不回填 `05 / 08`。

評估依據：

- `05-resume-master-zh.md` 已有 AntPlay project-level claim：`antplay-slot-admin-api`、`antplay-slot-game-api`、`antplay-slot-game-job`、`math-core` / `*-math` 的 refreshed / grouped consolidation 已回填，且已包含商戶控制面、Game API 白名單、request log / bet record、RTP / 暗池 / 風控監控、transfer wallet、bet / settle / rollback、schema routing 與 slot math module。
- `08-application-autobiography-zh.md` 已有通用投遞版與 B 版遊戲 / Slot / Provider Backend 版，已涵蓋 provider connector、seamless / transfer wallet、bet-settle callback、request log / bet record MQ、後台白名單、風控監控與 slot math module。
- Notion export 的新增價值是「營運 / 對接 / 排查 / 口說」脈絡，不是新的 direct commit / ticket / production issue evidence。
- 若再把 Notion export supporting cases 寫進 `05 / 08`，會和既有 project-level claim 重複，且容易把 operational docs 誤升級成「主導商戶開通 / 完整 wallet migration / 完整風控平台」。

本輪保留方式：

- 正式履歷 / 自傳：不改。
- 面試口說：已回填 [career-interview.md](career-interview.md) 的 `Notion export supporting cases`。
- 後續若遇到 JD 明確要求 merchant onboarding、Game API integration support、production troubleshooting 或 operator-facing support，可在 JD-specific package 中引用這些 supporting cases，仍不得寫成正式主 claim。

Relationship Check 補充：

- `projects/antplay/README.md`：需要標示 Step 4 已評估且不回填 `05 / 08`。
- `05-resume-master-zh.md` / `08-application-autobiography-zh.md`：已檢查，既有內容足夠承接，不更新。
- `04-interview-casebook.md` / `17-salary-negotiation.md` / `06-todo.md`：本輪不新增正式 claim、不新增長期待辦，不更新。
