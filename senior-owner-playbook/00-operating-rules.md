# 維護規則

## 只保留新資料

外層只保留新的長期結構：

- `senior-owner-playbook/`：通用方法論、提示詞、學習路線、面試與履歷。
- `projects/`：之後各專案整理後的新分析。
- `archive/`：舊資料、舊中間稿、原始匯入、待分析、待刪與重複筆記，日後由 Nick 人工審查是否刪除。

## 不複製舊檔

新增內容必須重寫，不能直接把舊檔搬回來。可以讀舊資料，但輸出要整理成：

- 更少檔案
- 更清楚分類
- 更保守 claim
- 更容易讀
- 更適合面試與履歷

## 每條 flow 的完成標準

完整 flow 學習包至少要能回答：

1. 這條 flow 解決什麼業務問題。
2. 入口在哪裡。
3. 主要 code 路徑是什麼。
4. DB / Redis / MQ / 外部 API 有哪些。
5. 正常流程怎麼跑。
6. 失敗、重試、補償、對帳怎麼處理。
7. Senior / Owner 應該注意什麼設計取捨。
8. 面試怎麼講。
9. 履歷怎麼保守寫。
10. 哪些不能誇大。

## Senior / Lead / Architect 對標

Senior 不是背技術名詞，而是能把 production flow 講清楚：

- money correctness
- transaction consistency
- idempotency
- retry / compensation
- auditability
- observability
- deployment risk
- operation recovery
- trade-off
- ownership boundary

Lead / Architect 更進一步，要能回答：

- 系統邊界怎麼切。
- 哪裡是 source of truth。
- 哪些資料只是 projection / cache / report。
- 怎麼讓團隊可以維護。
- 怎麼降低事故半徑。
- 怎麼用漸進式方案落地，而不是空談重構。

## 安全線

不得寫入：

- token
- password
- private key
- internal IP
- production URL
- 可識別客戶或公司機密的資訊

寫履歷時不得把分析成果包裝成已上線成果。
