# Company System Deep Dive Project Value Map

狀態：候選學習價值地圖，不是履歷 claim map，也不是自動開工授權。

## 使用方式

Deep Dive 選題先看本檔，再看 `project-index.md` 與 `topic-list.md`。

高 / 中 / 低價值只代表：

```text
對系統理解、工程判斷、legacy code reading、architecture thinking、production troubleshooting 的學習 ROI。
```

不代表：

```text
可以放履歷
可以寫自傳
Nick direct owner
已完成 evidence
```

若某題要回填履歷 / 自傳 / 面試正式材料，必須另走 evidence boundary / claim gate。

## 評分維度

### 高價值

符合多項：

- 真實 production flow。
- 有跨系統互動。
- 有 money / order / wallet / state correctness。
- 有 DB / MQ / Redis / external provider。
- 有 failure window。
- 有 legacy constraint。
- 有 git history / bugfix / incident 線索。
- 能學 architecture / refactor / migration trade-off。

### 中價值

符合部分：

- 能補 code reading。
- 能補 DB / cache / auth / permission / observability。
- 能支撐某些面試追問。
- 但 production 風險或架構深度較有限。

### 低價值

通常是：

- DTO / enum / mapper / utility。
- 單純 CRUD。
- 沒有明顯 failure window。
- 沒有跨系統互動。
- 沒有可遷移的 engineering thinking。
- 只因為「看起來新 / 聽起來高級」但本地 evidence 不足。

## iwin

Local path: `/Users/nick/Git/iwin`

### 高價值

- Gameserver / third-party transfer in-out：provider integration、wallet mutation、transfer boundary、跨系統狀態收斂。
- Payment provider callback / request / query：callback trust boundary、timeout unknown、MQ retry、order state、reconciliation 邊界；但既有 KB 已很多，除非要補 code / git / DB 新 evidence，否則不應一直重跑。
- game_job / daily game data summary：batch、projection、report correctness、重跑、資料範圍、source of truth vs report。
- k3s-deploy / gameserver phased rollout：rollout / rollback / health check / observability，適合學 Platform / deployment decision，但不包裝成 DevOps owner。
- Legacy reconstruction across payment / gameserver / job：API -> DB -> MQ -> batch -> admin repair 的系統地圖與 git history。

### 中價值

- Payment provider adapter differences：多 provider mapping / signature / response handling，可學 adapter boundary，但若只重複 callback happy path價值下降。
- Admin / app_bi repair boundary：人工修訂單狀態、op log、月表 routing，適合補 manual operation risk。
- Service communication inventory：確認 HTTP / MQ / scheduler / config / discovery 等互動方式，適合作為架構 preflight。

### 低價值

- 單一 provider controller 的欄位 mapping 重述。
- 只看 DTO / VO / enum / mapper。
- 只因為想找 Netty / 微服務而硬掃；除非 code evidence 顯示有自研 network / gateway / service communication 核心。

## antplay

Local path: `/Users/nick/Git/antplay`

### 高價值

- Slot bet-settle-rollback：下注、派彩、rollback、money correctness、duplicate handling、state transition，是 Deep Dive 首選之一。
- Transfer wallet money in-out：wallet source of truth、partial success、transaction boundary、idempotency。
- Proxy user data report projection：MQ / projection、consumer、report rebuild、eventual consistency。
- Bet record sharding / schema route：高流量資料表、routing、partition / sharding、查詢與維運風險。
- DB partition job / report routing：分表 / 分區、job、報表 routing、補資料與跨表查詢風險。

### 中價值

- Slot math result contract / fixed multi bet / RTP simulation：遊戲 domain 差異化，可學 result contract、simulation validation、backend 與 math 邊界；不講成 math owner。
- Feature state transform 類 flow：可學遊戲狀態資料結構，但視 JD 決定深度。
- Redis / config / game setting 類 topic：若連到 runtime risk / cache consistency 才值得。

### 低價值

- 單純看 math formula 或遊戲玩法，不連到 backend contract / validation。
- 單一 DTO / mapper / enum。
- 平均掃所有 star-math repo。

## ugsoft

Local path: `/Users/nick/Git/ugsoft`

### 高價值

- Provider connector / integration boundary：若 code-backed，可補多 provider connector、adapter、request / callback / query 類能力。
- Admin auth / RBAC / JWT：若有實際 flow，可學 permission model、token lifecycle、admin operation risk。
- Legacy module boundary inventory：先盤點 repo / module / service，找可深挖的 production flow。

### 中價值

- API design / controller-service boundary。
- Config / routing / provider setting。
- Audit log / operation log。
- Error handling / exception mapping。

### 低價值

- 未盤點前就硬寫成履歷素材。
- 只看 CRUD / DTO / enum。
- 沒有 flow / production risk 的單點功能。

## usproject

Local path: `/Users/nick/Git/usproject`

### 高價值

- 目前待盤點。只有確認它包含真實 production-like backend、系統架構、交易 flow、integration、incident / troubleshooting 或可遷移 architecture thinking 後，才升高價值。

### 中價值

- Repo / module inventory：先確認專案性質、技術棧、是否有 backend / infra / product architecture 可學。
- Side context / architecture comparison：若不是公司 project，可作學習參考，但要標清楚不是公司 evidence。

### 低價值

- 未確認前不拿來當主力 deep dive。
- 不為了填排程硬讀。
- 不直接當履歷 / 自傳素材。

## DevOps

Local path: `/Users/nick/Git/DevOps`

### 高價值

- Rollout / rollback / health check：deployment safety、readiness / liveness、快速回退。
- Monitoring / log collection / alert：production observability、incident detection。
- Config / secret boundary：安全、設定管理、環境差異風險。
- Service deployment topology：如果能連到 iwin / antplay runtime，適合作為 architecture / platform deep dive。

### 中價值

- CI/CD pipeline reading。
- Kubernetes / k3s resource request / limit。
- Deployment template / Helm / manifest pattern。
- Operational runbook / manual operation。

### 低價值

- 不包裝成 DevOps owner。
- 不全掃 infra。
- 不保存 internal URL、token、credential。
- 不為了追雲端 / K8s 名詞而偏離 backend production learning。

## 跨 project 題目

這些題目可能比單一 project 更有學習價值，但要等各 project 有足夠 context 再做：

- Runtime architecture / service communication inventory：HTTP、MQ、scheduler、Redis、DB、service discovery、gateway / network stack。
- Money correctness map：payment、wallet、bet-settle、rollback、report projection 的 source of truth 比較。
- Async architecture map：MQ、job、projection、retry、rebuild、manual repair。
- Legacy modernization map：哪些地方適合 minimal improvement、哪些適合 strangler-style migration，哪些暫時不值得動。
- Observability map：trace key、log、metric、alert、dashboard、incident timeline。

## 下一步選題原則

如果目標是最大學習 ROI，優先順序通常是：

1. `antplay / slot bet-settle-rollback`
2. `antplay / MQ projection`
3. `iwin / third-party transfer in-out`
4. `iwin / service communication inventory`
5. `iwin / game_job daily summary`
6. `antplay / bet record sharding`
7. `ugsoft / module inventory`
8. `DevOps / rollout health check`
9. `usproject / repo inventory`

Payment callback 仍是高價值，但不是 Deep Dive 第一順位，因為既有 KB 已經很多；除非本輪目標是補 code / DB / git history 新 evidence，否則容易變成重包既有內容。
