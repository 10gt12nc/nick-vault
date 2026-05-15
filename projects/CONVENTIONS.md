# Projects 規範

這份是 `projects/` 開工前的硬規則。

本規範適用所有 `projects/{domain}/{project}`。不是單一專案專用。

## 自動維護共用規則

Nick 不需要每次提醒「重讀 KB / 重讀 code / 維護 README」。

每次處理任何 project / flow / Step 前，AI 必須自動：

- 重讀 `AGENTS.md`。
- 重讀 `senior-owner-playbook/00-operating-rules.md`。
- 重讀 `senior-owner-playbook/09-ai-prompt-manual.md`。
- 重讀 `senior-owner-playbook/03-flow-learning-package-template.md`。
- 重讀該 project 既有 `README.md`、Step 文件、flow 文件。
- 重讀相關 code repo 的 branch、log、path-specific log 與主要入口。
- 掃公司 / 來源 code repo 前先 `git fetch --all --prune` 或用等效方式更新 remote refs，並記錄 local HEAD、remote HEAD、是否 ahead / behind。
- 公司 / 來源 code repo 只能讀；不得自動 `pull`、merge、checkout、rebase 或改工作樹。若本機落後遠端，標示「本機未更新 / 待 Nick 確認」。
- 判斷本次是 Level 1 / Level 2 / Level 3。
- 寫清楚已掃、未掃、待確認。

每次完成後，AI 必須自動判斷是否需要同步維護：

- project README
- Step 文件
- flow evidence
- claim boundary
- project-level career/interview 素材
- 共用 KB

不需要更新時，也要簡短說明原因。

## Step 主線防跳規則

新 project 的固定主線是：

```text
Step 1：找 candidate flows
Step 2：比較 candidate flows 的技術點、風險、證據強度與 module / repo / service 邊界
Step 3：選定一條 flow 後才建立單條 flow 學習包
Step 4：轉面試 case
Step 5：整理履歷 / 自傳邊界
```

如果 project 只有 Step 1，下一步必須是 Step 2。沒有 `step2-flow-comparison.md` 或等價 Step 2 文件時，不得直接建議或建立單條 flow Step 3，除非 Nick 明確指定跳過。

## 多 module / monorepo 專案規則

若來源 repo 是 multi-module、monorepo、多 service instance 或多 repo 關聯專案，Step 1 / Step 2 必須先建立定位用邊界：

- root module、submodule、service instance、tooling、config 分層。
- 哪些是 runtime service，哪些是 common library、離線工具或部署 / instance config。
- 候選 flow 橫跨哪些 module / upstream / downstream。
- 未掃 module 要明確標示未掃。

這不是要求平均整理所有 class；module map 只用來支撐 flow 排序，避免在還沒理解架構邊界時急著建立單條 flow。

## 目的

`projects/` 用來放整理後、可長期維護的專案分析。

它不是：

- 公司 code 備份區
- 舊 ai-notes 搬運區
- 大量 class summary
- 履歷誇大素材庫

它是：

- Senior / Platform Backend / Owner 面試 case 的 evidence base
- 每條 production flow 的可讀整理
- 履歷與自傳更新前的證據區

## 固定結構

以下是新建或重整後的固定結構。既有 `projects/iwin/...` 若仍是舊平鋪格式，先標註「舊格式 / 待遷移」，不要在 Nick 未要求時批量搬檔。

```text
projects/{domain}/{project}/
  README.md
  architecture-map.md
  flows/
    {flow-name}/
      flow.md
      career-interview.md
      materials/
        evidence.md
        decision-notes.md
        interview.md
        claim-boundary.md
  career-interview.md
```

閱讀順序：

1. Nick 預設只需要讀 `flow.md`。
2. 若要看這條 flow 可以怎麼轉面試 / 履歷，再讀同層 `career-interview.md`。
3. 要查 code evidence、技術比較、面試稿細節、不可誇大邊界時，才進 `materials/`。

`flow.md` 必須自己能讀懂，不可以讓 Nick 需要在 `materials/evidence.md`、`materials/interview.md`、`materials/claim-boundary.md` 裡拼答案。每份 `flow.md` 要先有初階 / 中階可讀區，再進 Senior / Owner 分析。

## 檔案責任

### README.md

只放專案定位：

- 專案類型
- 技術棧
- 主要 production 能力
- 可對標職缺
- 不可誇大的地方

### flow.md

只寫一條 flow。`flow.md` 就是這條 flow 的研究分析報告主文，也是唯一預設閱讀入口，不要另外新增重複的 `research-analysis-report.md`：

- 閱讀定位：這條 flow 是什麼、完成到 Step 幾、證據層級、是否只看到入口。
- 白話導讀：誰使用、什麼情境觸發、按下去或事件發生後系統做什麼、成功後狀態變成什麼。
- 初中階 Code 對照：Route / Controller、Service / Business、Model / DAO、SQL / Table、Redis、MQ / 下游通知、Log / Audit。
- 最小架構圖：只畫本 flow 相關上下游，未確認要標 `待確認`。
- 正常流程圖：用簡單圖或步驟表達 5 到 12 個主要步驟。
- 業務問題
- 系統位置
- 入口
- 正常流程
- state transition
- DB / Redis / MQ / external API
- failure window
- owner decision
- 證據層級標註：真實開發過 / 專案存在 / 分析素材 / 外部案例

建議 `flow.md` 內部順序：

1. 先讀懂：白話導讀、Code 分層、架構圖、流程圖、正常流程。
2. 再看風險：資料狀態、transaction boundary、consistency、idempotency、failure window。
3. 最後轉資深價值：owner decision、trade-off、面試 / 履歷邊界摘要。

若是 PHP / ThinkPHP、前端、後台、BI 或非 Java 專案，必須主動轉譯成 Nick 熟悉的後端分層語言，例如：

```text
Route / Controller：
Service / Business：
Model / DAO：
SQL / Table：
Redis：
MQ / 下游通知：
Log / Audit：
```

### flows/{flow-name}/career-interview.md

單條 flow 對應的保守履歷 / 面試素材。它不是正式履歷 master，只能當候選素材：

- 這條 flow 可以怎麼面試講
- 可安全使用的履歷 bullet
- 只能說分析 / 參與 / 對接的地方
- 不可寫成主導或 owner 的地方
- 證據層級與待補證據

### materials/evidence.md

只放可驗證證據。它是 `flow.md` 的證據附錄，不是另一份研究報告：

- repo path
- class / method / config 名稱
- SQL / table 名稱
- MQ topic / queue 名稱
- job / scheduler 名稱
- 已確認 / 推測 / 待確認

禁止摘錄：

- token
- password
- private key
- internal IP
- production URL
- 客戶資料
- 完整商業機密規格

### materials/interview.md

把 flow 轉成面試可講內容。它是面試稿附錄，不取代 `flow.md`：

- 30 秒摘要
- 3 分鐘版本
- 5 個追問
- Senior 能力點
- Lead / Architect 延伸追問
- 保守回答方式

### materials/claim-boundary.md

明確分：

- Nick 實際做過
- Nick 參與維護
- Nick 分析整理
- 可作改善建議
- 不可寫進履歷
- 需要本人確認 / MR / commit / ticket / metric

### project-level career-interview.md

專案層級的履歷與面試素材，只能由完成的 flows 彙整，不可憑空寫。

### architecture-map.md

專案 / 子專案地圖，只用來定位 repo、module、入口、上下游與候選 flow。

地圖不是主線，不要畫沒有 evidence 的大架構。當地圖已足夠定位 flow，就回到單條 flow 深挖。

### materials/decision-notes.md

單條 flow 的技術硬底子與決策比較。

只補和該 flow 直接相關的技術選型、trade-off、owner decision、外部通用模式。不要寫成技術大全，也不要當成 Nick 已做過的 evidence。

## Flow 完成定義

一條 flow 完成，必須同時具備：

- `flow.md`
- `career-interview.md`
- `materials/evidence.md`
- `materials/decision-notes.md`
- `materials/interview.md`
- `materials/claim-boundary.md`

且每份都要分清楚：

- 已確認
- 推測
- 待確認

並且每條履歷 / 面試相關說法都要標註證據層級：

| 標籤 | 意義 | 可否進履歷 |
| --- | --- | --- |
| `真實開發過` | 有 Nick 本人 MR / ticket / commit / production issue / 本人確認 | 可考慮，但仍要保守 |
| `專案存在 / code-backed` | code 確認有這條線，但 Nick 貢獻未確認 | 不直接寫成 Nick 成果 |
| `分析素材 / learning-only` | AI 讀 code 後整理成學習或面試理解 | 不寫進正式履歷 |
| `外部案例 / non-local` | 網路案例、官方文件、通用設計模式 | 只能補理解與面試追問 |
| `待確認` | 目前證據不足 | 不寫進履歷 |

未完成前，不得更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

## 履歷與自傳最終更新門檻

`senior-owner-playbook/05-resume-master-zh.md` 與 `08-application-autobiography-zh.md` 不是每條 flow 做完就急著改。

只有在同一批專案資料整理到足夠完整，且 Nick 明確要求更新正式版時，AI 才能開始最終整合。更新前必須：

- 深度掃描相關 code repo 的主分支、近期分支、path-specific history 與重要 diff。
- 深度掃描 `projects/` 既有 flow、project-level `career-interview.md`、`archive/` 舊履歷自傳與所有 KB 履歷素材。
- 去重、合併、降誇大。
- 每條 claim 標註 `真實開發過` / `專案存在` / `分析素材` / `待確認`。
- 沒有 evidence 的內容只可保留在素材或待確認，不寫進正式投遞語句。

## 外部案例使用規則

可以查外部案例，但必須標注。

格式：

```text
來源類型：外部案例 / 非本地 code evidence
來源：
用途：
可參考點：
不可主張：
```

外部案例只能用來：

- 補強理解
- 設計改善方案
- 準備面試追問
- 對照本地 code 缺口

外部案例不能用來：

- 寫成 Nick 已做過
- 寫進履歷 bullet
- 當成本地系統 evidence

## 對標職缺標籤

每條 flow 至少標一個：

- `Senior Backend`
- `Platform Backend`
- `System Owner`
- `Lead / Architect Candidate`

標籤標準：

### Senior Backend

能講清楚 code path、state、failure、DB / Redis / MQ。

### Platform Backend

能講清楚跨服務、provider contract、共用能力、runtime integration。

### System Owner

能講清楚成功定義、終態、補償、對帳、監控、人工介入邊界。

### Lead / Architect Candidate

能講清楚 trade-off、migration risk、rollback plan、observability、team maintenance。

## 新專案開工前的允許檔案

尚未由 Nick 指定的 project，先不要預建資料夾。已經開工過的 project 可保留既有資料，之後再依本規則逐步重整。

最小允許先存在：

- `projects/README.md`
- `projects/CONVENTIONS.md`

未指定前不要新增：

- `projects/iwin/...`
- `projects/ugsoft/...`
- `projects/antplay/...`
- `projects/devops/...`

等 Nick 指定第一個專案與 flow 後再建。
