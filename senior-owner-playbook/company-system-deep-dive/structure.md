# Company System Deep Dive Structure

狀態：啟用前資料結構圖；尚未設定 automation。

## Tree

```text
senior-owner-playbook/company-system-deep-dive/
├── README.md
├── curriculum.md
├── project-index.md
├── topic-list.md
├── deep-dive-template.md
├── manual-deep-dive-prompt.md
├── deep-dive-log.md
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
| `README.md` | Deep Dive 線的定位、章節、學習層級、邊界 |
| `curriculum.md` | 一年公司系統深度研究課綱 |
| `project-index.md` | 五個 project 的 local path、價值、邊界與輪詢順序 |
| `topic-list.md` | deep dive topic 清單 |
| `deep-dive-template.md` | 每次 deep dive 的固定輸出格式 |
| `manual-deep-dive-prompt.md` | 手動請 Codex 跑一次 deep dive 的 prompt；不是 automation prompt |
| `deep-dive-log.md` | 只記 deep dive 摘要與是否有 new findings，不保存聊天逐字稿 |
| `projects/{project}/README.md` | 單一 project 的 deep dive 邊界與候選 topic |

## 未來 deep dive 放置規則

若某次 deep dive 值得保存，放在：

```text
company-system-deep-dive/projects/{project}/YYYY-MM-DD-{topic-slug}.md
```

例如：

```text
company-system-deep-dive/projects/iwin/2026-07-06-payment-callback.md
company-system-deep-dive/projects/antplay/2026-07-13-bet-settle-rollback.md
```

packet 檔只放去識別化後的 learning / production / decision knowledge，不放公司機密、商戶、token、內部 URL 或真實交易資料。

## Relationship Check

Deep Dive 預設不回填正式履歷 / 面試材料。只有當 deep dive 產生明確可用的 production risk、troubleshooting evidence、system design trade-off 或面試追問答案，且 Nick 明確要求回填時，才檢查是否要更新：

- `04-interview-casebook.md`
- `05-resume-master-zh.md`
- `08-application-autobiography-zh.md`
- `17-salary-negotiation.md`
- `19-interview-coaching-question-bank.md`
- 對應 production flow 的 `career-interview.md`

否則只更新本資料夾與 `deep-dive-log.md`。
