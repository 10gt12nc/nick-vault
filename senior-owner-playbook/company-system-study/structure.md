# Company System Study Structure

狀態：啟用前資料結構圖；尚未設定 automation。

## Tree

```text
senior-owner-playbook/company-system-study/
├── README.md
├── project-index.md
├── topic-list.md
├── packet-template.md
├── manual-run-prompt.md
├── study-log.md
├── structure.md
└── projects/
    ├── iwin/
    │   └── README.md
    ├── antplay/
    │   └── README.md
    ├── ugsoft/
    │   └── README.md
    ├── usproject/
    │   └── README.md
    └── DevOps/
        └── README.md
```

## 檔案責任

| Path | 責任 |
| --- | --- |
| `README.md` | 這條 study 線的定位、輸出規則、收錄標準與邊界 |
| `project-index.md` | 五個 project 的 local path、價值、邊界與輪詢順序 |
| `topic-list.md` | 可輪詢 topic 清單；每週只選一個 |
| `packet-template.md` | 每週 study packet 的固定輸出格式 |
| `manual-run-prompt.md` | 手動請 Codex 跑一次 study packet 的 prompt；不是 automation prompt |
| `study-log.md` | 只記已完成 packet 的摘要與是否值得回填，不保存聊天逐字稿 |
| `projects/{project}/README.md` | 單一 project 的 study 邊界與候選 topic |

## 未來 packet 放置規則

若某週真的產出 study packet，放在：

```text
company-system-study/projects/{project}/YYYY-MM-DD-{topic-slug}.md
```

例如：

```text
company-system-study/projects/iwin/2026-07-06-provider-callback.md
company-system-study/projects/antplay/2026-07-13-bet-settle-rollback.md
```

packet 檔只放去識別化後的 production / decision knowledge，不放公司機密、商戶、token、內部 URL 或真實交易資料。

## Relationship Check

本資料夾的 packet 預設不回填正式履歷 / 面試材料。只有當 packet 產生明確可用的面試句子，且 Nick 明確要求回填時，才檢查是否要更新：

- `04-interview-casebook.md`
- `05-resume-master-zh.md`
- `08-application-autobiography-zh.md`
- `17-salary-negotiation.md`
- `19-interview-coaching-question-bank.md`
- 對應 production flow 的 `career-interview.md`

否則只更新本資料夾與 `study-log.md`。
