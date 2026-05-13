# Vault 結構規劃

這份回答兩個問題：

1. `senior-owner-playbook` 要不要改名？
2. 之後專案相關資料要放哪裡？

## 結論

`senior-owner-learning-2026-05-13` 已改名為：

```text
senior-owner-playbook/
```

原因：

- 不帶日期，適合長期維護。
- `learning` 太像讀書筆記，`playbook` 比較像可執行方法論。
- 這裡放的是總方法、提示詞、學習路線、面試與履歷，不應該塞滿各專案細節。

## 建議外層結構

未來不要把所有專案都塞進 `senior-owner-playbook/`。

建議外層長這樣：

```text
nick-vault/
  AGENTS.md
  senior-owner-playbook/
    README.md
    00-operating-rules.md
    01-senior-owner-flow-inventory.md
    02-learning-roadmap.md
    03-flow-learning-package-template.md
    04-interview-casebook.md
    05-resume-master-zh.md
    06-todo.md
    07-core-positioning.md
    08-application-autobiography-zh.md
    09-ai-prompt-manual.md
    10-vault-structure-plan.md
  projects/
    iwin/
      app_bi/
      game_api/
      payment/
    ugsoft/
      ugsoft-admin-api/
      ugsoft-connector-api/
    antplay/
      antplay-slot-game-api/
      antplay-slot-game-job/
    devops/
      primestar/
  archive/
```

## 各資料夾責任

### senior-owner-playbook/

放跨專案通用的東西：

- Senior / Owner 方法論
- AI 維護規則
- 提示詞
- 學習路線
- 面試案例索引
- 唯一履歷 master
- 投遞用自傳

不要放：

- 單一專案完整 flow 細節
- 大量 evidence
- 舊檔複製品
- code

### projects/

之後新增專案相關資料放這裡。

每個專案只放整理後的新分析：

```text
projects/{domain}/{project}/
  README.md
  flows/
    {flow-name}/
      flow.md
      evidence.md
  career-interview.md
```

### archive/

只當參考、待分析與待刪區。

可以讀，但不要把它當長期資料庫。

如果之後真的清空 archive，已經整理進 `senior-owner-playbook/` 和 `projects/` 的內容才會留下。

## AI 會不會自動維護？

不會。

AI 不會自己定期整理，也不會在你沒有要求時自動掃 repo。

但只要你之後用 `09-ai-prompt-manual.md` 的提示詞開新任務，AI 就能依規則維護：

- 先讀規則
- 再讀 playbook
- 再讀指定專案
- 只整理一條 flow
- 產出新分析
- 必要時更新履歷或面試資料

如果你要「定期自動跑」，那要另外建立 automation。現在先不建議，因為你目前更需要慢慢讀、人工確認，而不是讓 AI 自動產一堆你消化不了的文件。

## 建議工作節奏

每次只做一件事：

1. 選一個專案。
2. 先檢查是否已有相同或相近 flow。
3. 跑 Step 1 找 flow。
4. 只挑一條 flow。
5. 深挖成 `flow.md` + `evidence.md`。
6. 轉成面試 case。
7. 判斷是否更新履歷。

不要一次掃全部專案。

真正會讓你升級的不是資料量，而是你能不能把一條高價值 flow 講到：

- business goal
- state transition
- failure window
- owner decision
- interview story
- resume boundary

## 重複 Flow 規則

如果 Nick 選到以前讀過的 flow，AI 必須先告知：

- 讀過的位置
- 完成狀態
- 是否已有 evidence
- 是否已轉面試 case
- 是否已更新履歷
- 建不建議重讀

不要直接重做。

只有在 evidence 不完整、code 有新分支、舊分析太粗、尚未轉面試 case，或 Nick 明確要求重做時，才重新深挖。
