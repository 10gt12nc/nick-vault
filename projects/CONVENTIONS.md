# Projects 規範

這份是 `projects/` 開工前的硬規則。

目前先建立規範，不開始寫個別 project case。

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

```text
projects/{domain}/{project}/
  README.md
  flows/
    {flow-name}/
      flow.md
      evidence.md
      interview.md
      claim-boundary.md
  career-interview.md
```

## 檔案責任

### README.md

只放專案定位：

- 專案類型
- 技術棧
- 主要 production 能力
- 可對標職缺
- 不可誇大的地方

### flow.md

只寫一條 flow：

- 業務問題
- 系統位置
- 入口
- 正常流程
- state transition
- DB / Redis / MQ / external API
- failure window
- owner decision

### evidence.md

只放可驗證證據：

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

### interview.md

把 flow 轉成面試可講內容：

- 30 秒摘要
- 3 分鐘版本
- 5 個追問
- Senior 能力點
- Lead / Architect 延伸追問
- 保守回答方式

### claim-boundary.md

明確分：

- Nick 實際做過
- Nick 參與維護
- Nick 分析整理
- 可作改善建議
- 不可寫進履歷
- 需要本人確認 / MR / commit / ticket / metric

### career-interview.md

專案層級的履歷與面試素材，只能由完成的 flows 彙整，不可憑空寫。

## Flow 完成定義

一條 flow 完成，必須同時具備：

- `flow.md`
- `evidence.md`
- `interview.md`
- `claim-boundary.md`

且每份都要分清楚：

- 已確認
- 推測
- 待確認

未完成前，不得更新：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

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

## 不開始動工前的允許檔案

目前允許先存在：

- `projects/README.md`
- `projects/CONVENTIONS.md`

還不要建立：

- `projects/iwin/...`
- `projects/ugsoft/...`
- `projects/antplay/...`
- `projects/devops/...`

等 Nick 指定第一個專案與 flow 後再建。
