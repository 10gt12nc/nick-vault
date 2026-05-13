# Projects

這裡放未來各專案整理後的新分析。

目前先只建立規範，還不開始寫個別 project case。

先讀：

- [CONVENTIONS.md](CONVENTIONS.md)

規則：

- 只放整理後的新內容。
- 不複製舊檔。
- 不搬公司專案 code。
- 不寫 secret、token、內網 IP、production URL、客戶資料。
- 每次只做一條 flow。
- 做新 flow 前，先檢查 `projects/**/flows/` 是否已有同名或相近 flow。
- 尚未完成 evidence 的 flow，不更新履歷 master。

建議結構：

```text
projects/{domain}/{project}/
  README.md
  flows/
    {flow-name}/
      flow.md
      evidence.md
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

跨專案通用的方法論、提示詞、學習路線、履歷與自傳，放在：

```text
senior-owner-playbook/
```
