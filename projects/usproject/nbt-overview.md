# usproject nbt Overview

用途：先把 `/Users/nick/Git/usproject/nbt` 的定位、可參考價值與 claim boundary 固定下來，避免之後被誤當成已上線 production flow 或正式履歷 claim。

本檔是 KB 最小防誤判補丁，不是完整 Step 1 / Step 2，也不是單條 flow Step 3。

## Current Position

`nbt` 目前可定位為 `active migration / AI-assisted reconstruction / local validation` 素材：

- 目標方向：把既有 Game51 / eyd 類玩法移植到 usproject Java 21 / Spring Boot 微服務。
- 目前狀態：以 fake-first local playable core、source map、local regression / validation、部署前 gate 設計為主。
- 參考來源：`nbt` repo 內 KB、`usproject-workspace` 的 service / architecture docs、既有 iwin Game51 source-of-truth mapping。
- 尚未定位為：已上線 production service、正式 wallet integration、正式 game-job / admin-api / report flow integration、完整 platform owner evidence。

## 可參考價值

這條線值得保留，因為它能支撐幾種面試可講的工作方法：

- AI-assisted legacy reconstruction：用 AI / KB / source map 拆既有玩法、狀態機、結算語意與風險假設。
- Fake-first validation：在未串真錢包、真 DB、真 push 前，先建立可啟動、可 curl、可 self-play、可 regression 的最小驗證面。
- Migration boundary：區分玩法語意、runtime adapter、wallet / bet / payout idempotency、部署驗證與正式整合 gate。
- Deployment readiness thinking：用 local / fake profile、CI / deploy gate 與 health check 思維降低正式串接前的不確定性。

這些是「工作方法 / migration preparation / AI collaboration」素材，不等同於 production 事故、上線成效或正式 owner claim。

## Claim Boundary

可在非履歷脈絡安全說：

- `nbt` 近期可作為 active migration / AI-assisted reconstruction 素材。
- 這條線可以作為「如何用 AI 協作拆 legacy system、建立 source map、先做 fake-first validation、再設計正式整合 gate」的面試輔助案例。
- 目前可當作 learning / supporting evidence，用於補強系統拆解、遷移準備、驗證策略與 AI workflow。

不可說：

- `nbt` 已正式上線。
- Nick 已主導完整 production game service。
- Nick 已完成正式 wallet / bet / payout / report / push integration。
- Nick 已 owner 完整 usproject platform、完整 Game51 migration 或完整部署體系。
- 本檔內容可直接回填 `05 / 08` 成正式履歷 claim。

Evidence level:

- `分析素材 / learning-only`：本檔本身。
- `專案存在 / code-backed`：`nbt` repo、`usproject-workspace` docs、既有 source map / local validation artifacts。
- `真實開發過`：需要另補 Nick 本人確認、commit / MR / ticket / 上線紀錄或其他 direct evidence，且仍要標示是否已上線。

## Personal Reference Boundary

`/Users/nick/Git/nick/*` 底下的個人專案可以作參考，但不能混成公司專案 evidence：

- `test001_RenPy` / `test001_unity`：可參考 AI 協作、0 到 1 產品工作台、Producer / Design / Engineering / QA / Release gate、scope control。
- 其他個人 repo：只作 personal lab / learning / workflow reference，除非 Nick 明確要求另做 side project 整理。
- 不得把 personal repo 寫成公司 production flow、公司上線成果或 Senior Backend 主履歷 evidence。

## Relationship Check

- `projects/usproject/README.md`：已把本檔加入讀檔順序與 Project Status，並標明不做完整 Step 1-5。
- `projects/source-repo-inventory.md`：已把 `nbt` / `usproject-workspace` 的 active migration / personal reference boundary 補進 usproject source 索引。
- `projects/README.md`：已新增本檔入口，方便下一輪 AI 不只看到 `ugsoft-apidoc`。
- `senior-owner-playbook/06-todo.md`：已記錄本輪是 KB 防誤判補丁，不新增 project flow 待辦。
- `05 / 08 / 04 / 17`：已檢查，不需更新；本輪未新增正式履歷 claim、面試正式 case 或薪資定位。
