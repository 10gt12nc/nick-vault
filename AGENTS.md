# AI 整理規則

本專案是 `nick-vault`，Nick 的個人資源整理庫。這不是程式專案，不需要複雜的工程式 notes/logs/analysis 結構。

## 目標

- 目前主入口是 `senior-owner-playbook/`。
- 目標是整理 Nick 的 Senior Java Backend / Platform Backend / System Owner 學習資料、專案 flow、面試案例與履歷素材。
- 把已經用過、等待 Nick 人工審查是否刪除的原始資料放在 `archive/`。
- 新內容必須重新整理、去重、結合、優化，不要直接複製舊檔。
- 使用繁體中文作為主要說明語言。
- 保留清楚索引，讓下一個 AI 能快速知道資料在哪裡。

## 目錄規則

- `senior-owner-playbook/`：放跨專案通用的方法論、提示詞、學習路線、面試案例、唯一履歷 master 與投遞用自傳。
- `projects/`：未來放各專案整理後的新分析，例如 `projects/iwin/payment/flows/{flow}/`。
- `archive/`：只放舊資料、舊中間稿、原始匯入、待分析與等待人工審查刪除的資料。
- 不要新增 `ai-notes/`、`docs/`、`.claude/`、`.codex/`、`resources/` 作為長期資料位置。
- 密鑰與 token 只能放本機 `.env`，不得寫入 Markdown 或 commit。

## Senior / Owner 原則

- 不要做 class summary。
- 不要平均整理所有功能。
- 優先整理 production flow。
- 每次只做一條 flow。
- 每條 flow 要分清楚已確認、推測、待確認。
- 核心關注：money correctness、transaction consistency、state transition、idempotency、retry / compensation、reconciliation、observability、owner decision。
- 履歷與面試不得誇大。沒有 evidence 前，不寫主導、獨立完成、改善 X%、正式架構師或全權 owner。

## Flow 格式

未來專案分析建議放：

```text
projects/{domain}/{project}/
  README.md
  flows/{flow-name}/flow.md
  flows/{flow-name}/evidence.md
  career-interview.md
```

## 檢查

更新後檢查：

- 是否只動 `nick-vault`？
- 是否沒有動公司專案？
- 是否沒有新增 code？
- 是否沒有 secret / token / internal IP / production URL？
- 是否有把推測與已確認分開？
- 是否有避免履歷誇大？
- 是否有更新 README 或 todo？
