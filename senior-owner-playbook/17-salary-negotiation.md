# 薪資談判定位

> 狀態：2026-05-28 Resume / 104 final check 版。這份用來支援 104 / LinkedIn / 面試談薪，不是永久行情表。薪資行情會變，正式投遞或談 offer 前要重新查 104 / CakeResume / LinkedIn / 同業職缺，並依職缺職責、地點、年終、分紅、遠端與工時調整。

> 2026-05-22 履歷包同步：`third_games_api` 本批代表 flows 與 `k3s-deploy gameserver-phased-rollout` 已完成 Step 5，但都維持 interview-only，不新增談薪主 claim。談薪主軸仍放在 payment、iwin_gameserver / game_api / game_job、UGSoft、AntPlay game-api / game-job / math 的保守可用履歷 evidence；K3s / rollout / observability 只能作 Platform Backend 加分案例，不可當作完整 DevOps / SRE owner 來抬價。

> 2026-05-25 履歷包同步：`antplay-slot-game-job` contribution refresh 已回填。談薪主軸可新增 / 強化 AntPlay job / event processing、Kafka / Quartz、proxy report projection / summary、big-win notification、activity supporting flow、partition / report path；settle pool / darkpool 只作 analysis-first 面試素材，不可當作完整 Kafka event platform、reward platform、DB sharding / schema routing、BI / report platform 或 risk / jackpot owner 來抬價。

> 2026-05-26 code-first claim audit：payment 談薪口徑降級為 provider / 商戶 integration，不再把 `iwin/payment` 單獨當成完整 money correctness 或 reconciliation 深度。談薪價值仍成立，但要靠「payment provider 對接 + 遊戲 wallet / bet-settle + MQ / batch projection + high-traffic partition + slot math / legacy reconstruction」的組合，而不是只靠 payment 組。

> 2026-05-27 履歷包同步：`ugsoft-admin-api` / `ugsoft-connector-api` contribution refresh 已回填。談薪可補 UGSoft 後台 API / control plane、Game API / provider IP 白名單控制面、RequestLog / BetRecord MQ、Quartz / report job、provider connector / transfer wallet / callback / job sync；但不得把它說成完整 UGSoft 平台、完整 provider gateway、完整 wallet / money flow、完整 RabbitMQ architecture owner 或完整 access-control platform owner。

> 2026-05-28 Resume / 104 final check：`antplay-slot-admin-api` contribution refresh 可作後台 control plane / async audit / whitelist sync 補充，但不單獨抬高談薪主軸；iwin / AntPlay / UGSoft system maps 只支撐架構說明與面試敘事，不新增正式履歷 claim 或薪資 claim。談薪主軸仍是 payment provider / 商戶對接、遊戲 wallet / bet-settle、MQ / batch / projection、partition / high-traffic data、slot math / legacy reconstruction 的組合。

## 定位

Nick 不應只用「4 年 Java 後端」談薪，而應用以下定位：

```text
Java Backend + payment / game provider 串接 + 遊戲 wallet / bet-settle + MQ / batch / high-traffic production flow 經驗
```

這個定位的價值不是技術名詞多，而是能接手高交易、跨服務、第三方 provider、狀態一致性風險、retry / compensation、報表 projection、缺乏交接文件的既有平台、legacy system takeover，以及 AI-assisted engineering workflow 的可追蹤開發閉環。payment 只作 provider integration evidence，不單獨代表完整帳務 / wallet / reconciliation 能力。

## 通用目標職缺版

目前沒有特定 JD 時，預設使用這個通用目標職缺：

```text
Senior Java Backend / Platform Backend
偏 payment / game provider gateway、遊戲 wallet / bet-settle、MQ / batch、跨系統狀態一致性風險、legacy production flow takeover
```

投遞與談薪主軸：

- 主軸 1：第三方金流 provider / 商戶 request / callback / query / withdraw，能講簽章、callback ack / 重送風險、timeout unknown、訂單狀態與查單 / 人工補償邊界；不包裝成完整 wallet / reconciliation。
- 主軸 2：第三方遊戲 provider / gameserver 錢包與下注結算，能講 bet / settle / refund / transfer-in-out、wallet boundary、round log 與 projection。
- 主軸 3：MQ / Kafka / Quartz / batch，能講 request log、bet record、UGSoft admin-api RequestLog / BetRecord MQ ingestion、report projection / summary、big-win notification、partition / report path、retry、DLT、重跑與營運查詢。
- 主軸 4：legacy takeover / system reconstruction，能講如何用 code reading、git history、DB / Redis / MQ / log flow 重建可維護脈絡。
- 加分但不當主 claim：K3s rollout / observability analysis、AI-assisted engineering workflow、slot math module 維護與驗證。

投遞時若沒有特定 JD，直接使用 `08` 與本檔的通用版；不要為了客製而要求 Nick 先貼 JD。若 JD 寫「payment / wallet / provider / reconciliation / settlement / Kafka / high traffic / legacy system」，再把這些關鍵字往前放；若 JD 只寫一般 CRUD、後台管理或內部系統，應降調金流 / slot math 比重，避免對方覺得履歷太窄或過度博弈領域。

履歷版本策略：

- 公開主版本只放 A 版：通用高交易 Senior Java Backend / Platform Backend。
- B 版：遊戲 / Slot / Provider Backend，只在 JD 明確吃遊戲、slot、provider gateway、math module 時客製。
- C 版：Platform / Legacy Takeover，只在 JD 明確吃 legacy system、platform backend、cross-repo reconstruction、MQ / batch、observability 時客製。

三版不是同等強度。A 是主身份，B 是 domain 差異化，C 是工作方法與平台視角。談薪時也以 A 的 provider integration、遊戲 wallet / bet-settle、MQ / batch、partition / high-traffic data 與 legacy takeover 作主軸；payment 只作 provider 對接證據，不單獨拿來抬成完整金流 owner。B / C 只在對方職務吃得到時用來拉高匹配度。

## 市場錨點

2026-05-20 快照，已依補讀舊 104 PDF 後的 legacy takeover / 兩套平台恢復可維護 claim 重新校正：

- 104 上 Senior Backend 常見職缺可見年薪約 `100-150 萬`。
- 部分 Senior Backend 職缺可見年薪約 `120-180 萬`。
- Java / Senior Backend 職缺可見月薪約 `80,000-150,000`。
- 部分 Mid~Senior Java Backend 可見年薪約 `100-160 萬`，資深 Java / 後端職缺常見 `100,000+` 或 `80,000-150,000`。

以上只作談薪錨點，不是保證可拿到。正式談薪前要重查當下資料，並看職缺是否真的需要 payment provider integration、遊戲平台、provider gateway、wallet / bet-settle、交易一致性風險或 MQ / Kafka 經驗。

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
月薪 115,000-135,000
年薪約 160-190 萬
```

這是目前主力談判區間。適用於：

- Senior Java Backend。
- Platform Backend。
- Gaming / Payment / Wallet / Provider Integration Backend。
- 職務需要接 legacy production flow、第三方 provider、MQ / batch、交易一致性或報表 projection。
- 職務需要整理缺乏交接文件的既有平台，讓系統恢復可維護、可交接狀態。
- 職務重視 AI-assisted development、knowledge-base driven development、code review / diff review 與工程流程提效。

面試時主談可以抓：

```text
年薪 160-180 萬
```

### 進取開價

```text
月薪 135,000-145,000
年薪約 190-210 萬
```

`進取` 不是亂喊價，也不是保證能拿到；意思是「這個價位不是每家公司都會給，但遇到職務範圍剛好吃 Nick 的金流 / provider / legacy takeover / AI-assisted workflow 經驗時，可以拿來開價或談上限」。

適用情境：

- 職缺明確要求 Senior Backend / Platform Backend。
- 需要金流 / 錢包 / provider gateway / bet-settle / MQ / Kafka / 高交易系統經驗。
- 需要接手複雜 production flow、跨 repo service boundary 或 legacy system takeover。
- 需要協助團隊重建服務 / 部署 / 資料流脈絡，補齊維運文件與交接脈絡。
- 需要把 AI coding tools 導入可控流程，而不是只追求產碼速度。
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

## 現實邊界

Nick 可以投 10 萬以上職缺，也可以把 `100,000` 當底線，但不能把 10 萬以上視為穩拿。更準確的判斷是：

```text
有資格去談 10 萬以上，但需要用 3 條以上 production flow case 證明，不是靠履歷文字或 AI 整理結論。
```

AI 對薪資的判斷可能偏樂觀，因為 KB 主要整理 Nick 的 code evidence、舊履歷、本人描述與可包裝素材；它能提高定位清晰度，但不能保證面試表現、公司預算、競爭者強度或 offer 結果。

若面試時對方只把職缺當一般 CRUD / 維護工程師，合理落點可能只到 `90,000-110,000`。若對方需要金流、provider、MQ / batch、legacy takeover 或 AI-assisted workflow，才有機會談到 `115,000-135,000` 甚至進取區間。

## 目前 8 萬薪資判讀

目前卡在月薪 `80,000`，不能直接解讀成 Nick 只值 8 萬。比較準確的說法是：

```text
8 萬是目前公司 / 組織 / 職級 / 薪資帶給 Nick 的價格，不是外部市場對 Nick 能力的最終定價。
```

可能原因：

- 公司薪資帶偏低。
- 內部調薪不跟市場走。
- 初始薪資錨點偏低。
- 組織沒有清楚 Senior ladder。
- 做了大量維護、救火、補文件、補流程，但沒有被正式轉成職級與薪資。
- 公司把 Nick 當穩定產出的人，卻沒有用外部市場價重新估值。

因此，8 萬在目前公司內部可能是組織可接受的價格，但不應自動變成下一份工作的自我定價。下一份工作應用 production case 驗證市場：

- 如果投出去完全沒面試，先修履歷與職缺定位。
- 如果有面試但被打穿，回來補 flow 與追問。
- 如果拿到接近 `100,000-120,000` 的 offer，就能證明主要問題不是 Nick 只值 8 萬，而是目前組織沒有給到市場價。

目前策略仍是：不要裸辭，先把 3 條主力 flow 練熟，再用外部面試驗證市場。

## 是否急著跳槽的判斷

若目前公司多年不調薪，但工時低、不太加班、上班有空檔能整理 flow / 履歷 / 面試 case / Java 與 SQL 基本功，而且 Nick 仍喜歡公司正在做的遊戲、slot math、`*-math` 邏輯包與高交易 domain，結論不是「一定要立刻跳」。

比較準確的策略是：

```text
不急著逃，但要開始市場化。
```

可以暫時留下的理由：

- 低壓與少加班讓 Nick 有時間把 `8-10` 條 production flow、`05 / 08 / 04 / 17`、面試口說與基本功補到穩定。
- `*-math` / 遊戲邏輯包是差異化 domain，不是一般 CRUD，若能整理成 code-backed case，對遊戲、payment、wallet、high-transaction backend 都有可轉譯價值。
- 團隊縮編後，Nick 可能更常接觸核心維護、跨 repo flow、缺交接文件的 legacy takeover 與 owner decision，這些都能變成 Senior 面試素材。
- 若目前信心還不穩，硬跳可能只是把焦慮帶進面試；先在低壓環境補齊 case，勝率更高。

不能無限期留下的警訊：

- 多年不調薪通常代表公司不是用市場價留人。
- 擴編承諾很久但沒有發生，甚至縮編，代表組織風險仍在。
- 「很多專案只有 Nick 會」若沒有對應職稱、授權、薪資與團隊支援，長期會變成責任增加但回報不變。
- 若空閒時間沒有轉成可投遞履歷、可面試 case 或真實 production ownership，外部市場不會自動承認這段時間的價值。

建議節奏：

```text
先留著拿穩定收入與低壓時間，把自己整理到外面願意用 10 萬以上買單；
同時做低壓 market check，讓市場回饋決定是否跳，而不是只靠焦慮或公司承諾。
```

判斷要不要跳時，看的是「外部 offer 是否明顯優於目前的總條件」，而不是只看月薪。要同時比較薪資、年薪、工時、技術深度、團隊品質、職涯 title、是否能接觸核心系統、是否有 senior mentor / review culture，以及是否會落入更高壓但學不到東西的環境。

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
我目前會以年薪 160-180 萬作為主要期待，實際會看職務範圍與整體 package。如果職務內容包含 payment / game provider 串接、遊戲 wallet / bet-settle、高交易 flow、MQ / Kafka、legacy 系統接手、AI-assisted engineering workflow 或平台穩定性維護，我會希望落在區間中上段；若團隊成長性與技術深度足夠，也可以依整體條件討論。
```

### 通用目標職缺版

```text
我會以年薪 160-180 萬作為主要期待，實際會依職務範圍、獎金結構與整體 package 討論。這個區間主要是因為我過去經驗不只是一般 API 維護，而是長期接觸 payment / game provider 串接、遊戲 wallet / bet-settle、MQ / batch、跨系統狀態一致性風險與 legacy production flow 接手。如果這個職缺確實需要處理 provider gateway、payment provider 對接、遊戲 wallet、retry / compensation、report projection 或既有系統重建，我會希望落在區間中上段。
```

### 對方問「為什麼是這個區間」

```text
我的經驗不只是在單一 API 或 CRUD，而是長期接觸遊戲平台、payment provider 對接、第三方遊戲 provider、遊戲 wallet / 下注結算、MQ / batch、報表 projection 與 legacy 系統維護，也有在缺乏完整交接文件時協助梳理兩套既有平台、恢復可維護狀態的經驗。近期我也把 AI coding tools 放進可控工程流程，用於 code reading、需求拆解、diff review、文件同步、測試檢查、commit 收斂與 KB 回填。這類工作需要處理 callback 重送、provider timeout、retry 重複副作用、狀態一致性風險、線上可追蹤性、跨 repo 系統理解與工程流程控管，所以我會用 Senior Java Backend / Platform Backend 的職責範圍來評估待遇。
```

### 對方擔心「博弈 / 遊戲領域太窄」

```text
我會把領域經驗轉成通用後端能力來看：第三方 provider 串接、payment provider 對接、遊戲 wallet / 下注結算、MQ / batch、報表 projection，本質上都是高交易系統的狀態邊界、冪等、retry、補償、對帳與可觀測性問題。這些能力可以轉到 payment provider integration、wallet、B2B platform、high traffic API 或需要接 legacy production flow 的後端團隊；但我不會把 payment 經驗誇大成完整 fintech 帳務 owner。
```

### 對方壓低到 9 萬以下

```text
我理解每家公司都有既有薪資區間。不過以我目前希望承擔的職務範圍，包含第三方 provider、payment provider 對接、遊戲 wallet / bet-settle、高交易 flow、缺交接文件的既有平台接手與系統維護，我會希望整體 package 至少能接近年薪 160 萬。如果月薪較低，可能需要看年終、分紅、職責範圍與成長性是否能補足。
```

### 對方問「K3s / AI-assisted 能不能算主力」

```text
我會把 K3s / rollout / observability 和 AI-assisted workflow 當作加分項，不會把它們說成主力 owner 經驗。我的主力仍是 Java backend 的 provider 串接、payment provider 對接、遊戲 wallet / bet-settle、MQ / batch 與 legacy production flow reconstruction。K3s 和 AI 工具的價值在於我能更有效率地理解系統、整理風險、做 diff review 與交接文件，而不是取代工程判斷。
```

## 不能這樣談

- 不要只說「我需要錢」。
- 不要用「我可以當架構師 / owner」當主要理由。
- 不要宣稱主導完整金流、完整 gameserver、完整 slot platform 或完整 provider gateway。
- 不要用沒有 metric 的「我改善很多效能」談價。
- 不要一開始就報底線。

## 對應履歷邊界

可支撐談薪的真實 / 保守定位：

- 參與第三方金流 provider / 商戶 request / callback / query / withdraw 對接與維護。
- 參與 payment / withdraw order consistency 類問題修正。
- 參與第三方遊戲 provider 投派整合與 gameserver 錢包 / 投注流水串接。
- 參與 AntPlay slot game API / job / math module、UGSoft 後台 API / control plane、UGSoft provider connector / gateway、RabbitMQ / Kafka / Quartz 非同步處理；game-job 可講 proxy report projection / summary、big-win notification、activity supporting flow 與 partition / report path，UGSoft 可講 RequestLog / BetRecord MQ、白名單控制面與 provider fail-fast。
- 能透過 code reading、log、git history、DB / Redis / MQ 流向重建 production flow。
- 能在交接文件不足時，協助梳理服務、部署環境、資料流與維運脈絡，讓既有平台恢復可維護 / 可交接狀態。
- 能建立 AI-assisted 開發閉環：code reading、需求拆解、diff review、文件同步、測試檢查、commit 收斂與 KB 回填。
- 能用 code-backed analysis 討論 K3s rollout / rollback / observability 風險，但目前只作面試加分，不作正式履歷主成果。

不可誇大：

- 不說主導完整金流。
- 不說完整 wallet / ledger / reconciliation owner。
- 不說完整遊戲平台 / gameserver owner。
- 不說完整 slot math framework / RTP 策略 owner。
- 不說完整 Kafka / RabbitMQ architecture owner。
- 不說完整 reward platform、完整 DB sharding / schema routing owner 或完整 BI / report platform owner。
- 不說完整 DevOps / K3s migration / SRE owner。
- 不把「會用 AI 工具」包裝成 AI 自動完成 production 開發；要強調自己負責判斷、驗證、review 與收斂。

## 當前狀態

若暫時沒有特定 JD，`04 / 面試 case 對齊檢查` 與三條主力 case 90 秒 / 3 分鐘口說稿已完成，不需要重新客製履歷；面試口說練習先暫停，等 Nick 明確要求才開始。若之後有明確 JD，才把 JD 關鍵字回填到 `08-application-autobiography-zh.md` 和本檔：

```text
通用談薪定位可用；有明確 JD / offer 前不重新客製
```

## 台灣轉職月份策略

這段是投遞節奏參考，不是保證。每年景氣、年終發放、農曆年時間、公司預算與職缺類型都會影響高峰；正式主攻前仍要重查 104 / 1111 / CakeResume / LinkedIn 與獵頭回饋。

台灣一般轉職旺季：

1. `2-4 月`：主旺季。年後轉職潮、新年度 budget / HC 開出來，職缺與流動通常最大。2026 因春節較晚與轉職季拉長，市場資料顯示高峰可延到 `4-5 月`。
2. `6-8 月`：畢業 / 校招波。新鮮人、MA、實習轉正與一般職缺較多；對 Senior Backend 不是最佳主戰場，但仍可能有補人。
3. `9-10 月`：第二波。科技 / 工程 / 外商 / 中高階缺可能因 Q4 前補人、專案啟動或預算調整再開一波。
4. `11-1 月`：準備 / 卡位期。不一定是錄取高峰，但適合開履歷、跟獵頭聊、同步 104 / LinkedIn，提前卡年後職缺。

對 Nick 的建議節奏：

```text
2026 下半年：整理履歷、case study、低壓面試與 market check
2026/9-10：跑一波第二波市場測試，確認外面怎麼看 Nick
2026/11-2027/1：履歷 / LinkedIn / 104 / 獵頭池 / 口說 case 整到可投
2027/2-4：正式主攻轉職
2027/9-10：若春季沒有理想 offer，作秋季備案
```

重點不是押某個月份，而是不要等旺季才準備。旺季只是職缺流量變大；真正會影響薪資與錄取的是 `08 / 04 / 17` 是否對齊、3-5 條 production case 能否抗追問，以及面試回饋是否有被回填修正。
