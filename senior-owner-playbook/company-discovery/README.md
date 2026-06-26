# Company Discovery

狀態：候選第二個 weekly automation；先建立資料夾、清單與輸出規則，尚未設定排程。

## 一句話定位

`Backend Weekly Learning` 是外部技術學習；`Company Discovery` 是從本地 code、git history、DB / SQL、config、architecture 與既有系統裡找出 Nick 還不知道的新工程知識。

這條線不是整理漂亮筆記，也不是把既有 KB 重新包裝。它的 KPI 是：

```text
每次找到 0-3 個新的高價值 discovery。
如果沒有新發現，就明確回報 No new high-value knowledge discovered.
```

## Mission

Do NOT summarize existing KB.

Primary goal:

```text
Find unknown engineering knowledge.
```

假設既有 flow 文件已經描述 happy path、面試講法、30 秒 / 90 秒版本與 evidence boundary。Company Discovery 不重複這些內容。

優先尋找：

- hidden architecture decisions。
- unexpected data flow。
- unusual SQL / index / table routing。
- retry logic。
- rollback handling。
- compensation。
- edge cases。
- production hacks。
- legacy constraints。
- historical refactors。
- important git history。
- TODO / FIXME。
- technical debt。
- duplicated logic。
- dead code。
- configuration risks。
- code 假設與 DB / MQ / Redis 實際約束不一致。

## 與 Backend Weekly Learning 的差異

| 項目 | Backend Weekly Learning | Company Discovery |
| --- | --- | --- |
| 來源 | 官方文件、工程文章、通用知識 | 本地 code、git history、DB / SQL、config、既有系統 |
| 目的 | 補 Senior Backend 基本功 | 找出未知工程洞察、風險、歷史脈絡 |
| 輸出 | 一週一主題 learning packet | 0-3 個 discovery；沒有就不更新 |
| 禁止 | 建學習債務 | 摘要既有 KB、重講 happy path |
| 邊界 | 不改履歷 / flow | 不碰公司機密、不產生未證實 claim |

## 選題方式

仍然採二維選題：

```text
先選一個 Project
再選一個 Discovery Target
```

專案清單看 [project-index.md](project-index.md)，target 清單看 [topic-list.md](topic-list.md)。

啟用前資料結構看 [structure.md](structure.md)。手動跑一次用 [manual-discovery-prompt.md](manual-discovery-prompt.md)。輸出格式看 [discovery-template.md](discovery-template.md)。

## Forbidden

除非有新 evidence，不要解釋：

- callback flow。
- wallet flow。
- bet flow。
- MQ flow。
- architecture happy path。
- 既有 30 秒 / 90 秒面試稿。
- 既有 trade-off。
- 既有 evidence boundary。

如果只找到既有 KB 已寫過的內容，輸出：

```text
No new high-value knowledge discovered.
```

## Discovery 評級

| Level | 定義 | 是否記 KB |
| --- | --- | --- |
| A | 新發現，直接支撐 production risk、troubleshooting、system design trade-off 或面試追問 | 記 |
| B | 新發現，但偏背景知識或需更多 evidence | 可簡記 |
| C | 只是重複既有 KB、一般常識、無直接價值 | 不記 |

每次最多記 3 個 discovery。寧可少，不要灌水。

## Discovery 格式

每個 discovery 必須回答：

```text
What surprised me?
Evidence
Why it matters
Risk / opportunity
How to verify next, if needed
KB action
```

## 現代化比較規則

可以比較新舊技術，但只能在發現真實痛點後比較。

例如：

- 看到 MQ publish fail 只 log，才討論 outbox / transactional event。
- 看到 RabbitMQ / XXL-MQ 的 ordering / retry 風險，才討論 Kafka 是否適合。
- 看到 Zookeeper / service discovery 真實痛點，才討論 Eureka / K8s discovery。
- 看到 polling job 的補資料痛點，才討論 CDC / event-driven。

每次都要先回答：

```text
現有痛點是什麼？
這是新 evidence 還是既有 KB 已知？
替代方案解決哪個問題？
會增加什麼成本？
現在值得做嗎？
```

若沒有新 evidence，不討論現代化。

## 收錄標準

值得收錄的內容必須至少符合一項：

- Nick 以前不知道。
- 既有 KB 沒寫清楚。
- 能暴露 production failure window。
- 能支撐 incident / troubleshooting。
- 能形成 architecture / migration decision。
- 能補強面試追問的真實底層 evidence。

不收錄：

- 公司機密、商戶、token、真實交易資料。
- 每日工作流水帳。
- 單純 class summary。
- 既有 flow happy path 摘要。
- 沒有新 evidence 的技術名詞。
- 把他人 commit 或 code-backed 素材包裝成 Nick direct owner。

## Automation 邊界草案

未來若設定排程，prompt 應要求：

- 讀本資料夾與必要 KB。
- 先讀 `project-index.md`，選一個 project；再讀 `topic-list.md`，選一個 discovery target。
- 每次只碰一個 project 的小 scope。
- 不全掃公司 repo。
- 不修改履歷、自傳、Story、正式 flow、`04 / 05 / 08 / 17 / 19`。
- 不產生 summary packet。
- 找不到 A / B 級 discovery 就不新增檔案，只更新 log 為 `No new high-value knowledge discovered`。

## 目前下一步

先不用設定 automation。先用 `manual-discovery-prompt.md` 手動跑 1-2 次，確認它真的能找到新東西，而不是重講既有 KB。
