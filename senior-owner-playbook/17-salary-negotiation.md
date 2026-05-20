# 薪資談判定位

> 狀態：2026-05-20 rolling 版。這份用來支援 104 / LinkedIn / 面試談薪，不是永久行情表。薪資行情會變，正式投遞或談 offer 前要重新查 104 / CakeResume / LinkedIn / 同業職缺，並依職缺職責、地點、年終、分紅、遠端與工時調整。

## 定位

Nick 不應只用「4 年 Java 後端」談薪，而應用以下定位：

```text
Java Backend + 金流 / 遊戲平台 + provider 串接 + MQ / batch / wallet / bet-settle production flow 經驗
```

這個定位的價值不是技術名詞多，而是能接手高交易、跨服務、第三方 provider、狀態一致性、retry / compensation、報表 projection、缺乏交接文件的既有平台與 legacy system takeover。

## 市場錨點

2026-05-20 快照，已依補讀舊 104 PDF 後的 legacy takeover / 兩套平台恢復可維護 claim 重新校正：

- 104 上 Senior Backend 常見職缺可見年薪約 `100-150 萬`。
- 部分 Senior Backend 職缺可見年薪約 `120-180 萬`。
- Java / Senior Backend 職缺可見月薪約 `80,000-150,000`。
- 部分 Mid~Senior Java Backend 可見年薪約 `100-160 萬`，資深 Java / 後端職缺常見 `100,000+` 或 `80,000-150,000`。

以上只作談薪錨點，不是保證可拿到。正式談薪前要重查當下資料，並看職缺是否真的需要金流、遊戲平台、provider gateway、交易一致性或 MQ / Kafka 經驗。

## 建議區間

### 保守可接受

```text
月薪 100,000-110,000
年薪約 140-154 萬
```

適用情境：

- 職稱只是 Backend Engineer / 中高級工程師。
- 職務不含核心金流、錢包、provider gateway 或高交易 flow。
- 公司總 package、穩定度、學習價值或工時條件明顯較好。
- 這已接近底線區，不應作為主談價。

### 合理主談

```text
月薪 115,000-130,000
年薪約 160-182 萬
```

這是目前主力談判區間。適用於：

- Senior Java Backend。
- Platform Backend。
- Gaming / Payment / Wallet / Provider Integration Backend。
- 職務需要接 legacy production flow、第三方 provider、MQ / batch、交易一致性或報表 projection。
- 職務需要整理缺乏交接文件的既有平台，讓系統恢復可維護、可交接狀態。

面試時主談可以抓：

```text
年薪 160-180 萬
```

### 進取開價

```text
月薪 135,000-145,000
年薪約 190-210 萬
```

適用情境：

- 職缺明確要求 Senior Backend / Platform Backend。
- 需要金流 / 錢包 / provider gateway / bet-settle / MQ / Kafka / 高交易系統經驗。
- 需要接手複雜 production flow、跨 repo service boundary 或 legacy system takeover。
- 需要協助團隊重建服務 / 部署 / 資料流脈絡，補齊維運文件與交接脈絡。
- 公司是高壓、高工時、高責任範圍，或期待接近 owner decision。

## 建議底線

```text
月薪 100,000
年薪約 140 萬
```

低於這個數字要很小心，除非同時符合：

- 公司穩定。
- 技術成長明確。
- 年終 / 分紅 / 股票或總 package 明顯補足。
- 職務能補強 Senior / Platform Backend 的下一階段能力。
- 工時與壓力合理。

不要因為擔心沒有 offer 就主動把自己壓到一般 4 年 CRUD Java 後端價位。舊 PDF 曾填 `96,000 元以上`，但在目前履歷補強後，`96,000` 只能視為底線附近，不應再作主談價。

## 104 期望待遇寫法

建議填：

```text
月薪 115,000-135,000，或年薪 160-190 萬，可依職務範圍、獎金結構與整體 package 討論。
```

若 104 欄位不適合寫區間，可用：

```text
待遇面議，期望年薪 160-190 萬。
```

## 面試口頭說法

### 標準版

```text
我目前會以年薪 160-180 萬作為主要期待，實際會看職務範圍與整體 package。如果職務內容包含核心金流、provider 串接、高交易 flow、MQ / Kafka、legacy 系統接手或平台穩定性維護，我會希望落在區間中上段；若團隊成長性與技術深度足夠，也可以依整體條件討論。
```

### 對方問「為什麼是這個區間」

```text
我的經驗不只是在單一 API 或 CRUD，而是長期接觸遊戲平台、金流 / 錢包、第三方 provider、下注結算、MQ / batch、報表 projection 與 legacy 系統維護，也有在缺乏完整交接文件時協助梳理兩套既有平台、恢復可維護狀態的經驗。這類工作需要處理 callback 重送、provider timeout、retry 重複副作用、狀態一致性、線上可追蹤性與跨 repo 系統理解，所以我會用 Senior Java Backend / Platform Backend 的職責範圍來評估待遇。
```

### 對方壓低到 9 萬以下

```text
我理解每家公司都有既有薪資區間。不過以我目前希望承擔的職務範圍，包含第三方 provider、金流 / 錢包、高交易 flow、缺交接文件的既有平台接手與系統維護，我會希望整體 package 至少能接近年薪 160 萬。如果月薪較低，可能需要看年終、分紅、職責範圍與成長性是否能補足。
```

## 不能這樣談

- 不要只說「我需要錢」。
- 不要用「我可以當架構師 / owner」當主要理由。
- 不要宣稱主導完整金流、完整 gameserver、完整 slot platform 或完整 provider gateway。
- 不要用沒有 metric 的「我改善很多效能」談價。
- 不要一開始就報底線。

## 對應履歷邊界

可支撐談薪的真實 / 保守定位：

- 參與第三方金流 provider request / callback / query / withdraw 對接與維護。
- 參與 payment / withdraw order consistency 類問題修正。
- 參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接。
- 參與 AntPlay slot game API / job / math module、UGSoft provider connector / gateway、RabbitMQ / Kafka / Quartz 非同步處理。
- 能透過 code reading、log、git history、DB / Redis / MQ 流向重建 production flow。
- 能在交接文件不足時，協助梳理服務、部署環境、資料流與維運脈絡，讓既有平台恢復可維護 / 可交接狀態。

不可誇大：

- 不說主導完整金流。
- 不說完整 wallet / ledger / reconciliation owner。
- 不說完整遊戲平台 / gameserver owner。
- 不說完整 slot math framework / RTP 策略 owner。
- 不說完整 Kafka / RabbitMQ architecture owner。

## 下一步

投遞前要把這份和 `08-application-autobiography-zh.md` 一起對齊：

```text
幫我依目標職缺調整 08 和 17：104 履歷欄位、期望待遇、面試談薪說法
```
