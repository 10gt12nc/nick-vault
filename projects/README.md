# Projects

這裡放未來各專案整理後的新分析。

所有 project 都套用同一套共用維護規則，不需要 Nick 每次重複提醒「重讀 KB / 重讀 code」。

先讀：

- [CONVENTIONS.md](CONVENTIONS.md)
- [source-repo-inventory.md](source-repo-inventory.md)

規則：

- 只放整理後的新內容。
- 不複製舊檔。
- 不搬公司專案 code。
- 不寫 secret、token、內網 IP、production URL、客戶資料。
- 每次開始前，AI 必須自動重讀 KB、project 既有文件與相關 code 最新狀態。
- 每次完成後，AI 必須自動判斷是否要維護 README、Step 文件、flow `materials/evidence.md`、`materials/claim-boundary.md` 或共用 KB。
- 每次只做一條 flow。
- 做新 flow 前，先檢查 `projects/**/flows/` 是否已有同名或相近 flow。
- 尚未完成 evidence 的 flow，不更新履歷 master。
- 新建或重整後，Nick 預設只讀 `flow.md`；該 flow 的履歷 / 面試素材放 `career-interview.md`，其他素材放 `materials/`。

建議結構：

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

範例：

```text
projects/iwin/payment/
projects/iwin/game_api/
projects/ugsoft/ugsoft-connector-api/
projects/antplay/antplay-slot-game-api/
projects/devops/primestar/
```

來源 repo 清單只放索引，不當成 evidence。真正分析仍要在每次 Step 前重讀 code repo 的 branch、log、path-specific history 與主要入口。

跨專案通用的方法論、提示詞、學習路線、履歷與自傳，放在：

```text
senior-owner-playbook/
```
