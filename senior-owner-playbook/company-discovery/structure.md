# Company Discovery Structure

狀態：啟用前資料結構圖；尚未設定 automation。

## Tree

```text
senior-owner-playbook/company-discovery/
├── README.md
├── project-index.md
├── topic-list.md
├── discovery-template.md
├── manual-discovery-prompt.md
├── discovery-log.md
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
| `README.md` | Discovery 線的 mission、禁止事項、收錄標準與邊界 |
| `project-index.md` | 五個 project 的 local path、價值、邊界與輪詢順序 |
| `topic-list.md` | discovery target 清單；不是 flow summary 清單 |
| `discovery-template.md` | 每次 discovery 的固定輸出格式 |
| `manual-discovery-prompt.md` | 手動請 Codex 跑一次 discovery 的 prompt；不是 automation prompt |
| `discovery-log.md` | 只記 discovery 結果與 rejected output，不保存聊天逐字稿 |
| `projects/{project}/README.md` | 單一 project 的 discovery 邊界與候選 target |

## 未來 discovery 放置規則

只有 A / B 級 discovery 才保存檔案：

```text
company-discovery/projects/{project}/YYYY-MM-DD-{discovery-slug}.md
```

例如：

```text
company-discovery/projects/iwin/2026-07-06-mq-produce-fail-window.md
company-discovery/projects/antplay/2026-07-13-bet-record-index-risk.md
```

如果沒有新發現，不新增 discovery 檔，只在 `discovery-log.md` 記：

```text
No new high-value knowledge discovered.
```

## Relationship Check

Discovery 預設不回填正式履歷 / 面試材料。只有當 discovery 產生明確可用的 production risk、troubleshooting evidence 或面試追問答案，且 Nick 明確要求回填時，才檢查是否要更新：

- `04-interview-casebook.md`
- `05-resume-master-zh.md`
- `08-application-autobiography-zh.md`
- `17-salary-negotiation.md`
- `19-interview-coaching-question-bank.md`
- 對應 production flow 的 `career-interview.md`

否則只更新本資料夾與 `discovery-log.md`。
