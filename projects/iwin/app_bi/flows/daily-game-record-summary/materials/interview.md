# Interview - app_bi daily-game-record-summary

更新時間：2026-05-15
完成狀態：Step 5 已完成
文件角色：`materials/interview.md` 詳細面試稿附錄
掃描等級：Level 2 Flow 深掃延伸
證據層級：分析素材 / learning-only；code 功能為專案存在 / code-backed；Nick 貢獻待確認

## 本次結論

這條 flow 可以轉成面試 case，但只能保守講成：

```text
我分析過一條每日遊戲資料彙總的 reporting flow。
表面上是 app_bi 後台查詢，實際要往上追 game_job producer，
拆出 source log、type=0 projection、type=1 summary 與後台 pivot 查詢。
我會從批次重跑、時區切分、資料成熟度、金額單位與補跑對帳角度看風險。
```

不能講成：

```text
我主導每日遊戲資料彙總。
我負責 game_job 報表批次。
我設計資料平台或報表架構。
我建立完整補跑 / 對帳 / 告警機制。
```

原因：目前只確認 code 功能存在，Nick 個人實作貢獻仍是 `待確認`。這條適合作為 reporting / batch consistency 的面試分析素材，不適合直接放正式履歷。

## Step 4 前檢查

已重讀 KB：

- `AGENTS.md`
- `senior-owner-playbook/00-operating-rules.md`
- `senior-owner-playbook/09-ai-prompt-manual.md`
- `senior-owner-playbook/03-flow-learning-package-template.md`

已重讀 vault：

- `projects/iwin/app_bi/README.md`
- `projects/iwin/app_bi/step1-candidate-flows.md`
- `projects/iwin/app_bi/step2-flow-comparison.md`
- 本 flow 的 `flow.md`
- 本 flow 的 `career-interview.md`
- 本 flow 的 `materials/evidence.md`
- 本 flow 的 `materials/decision-notes.md`
- 本 flow 的 `materials/interview.md`
- 本 flow 的 `materials/claim-boundary.md`

已重讀 code repo：

- `/Users/nick/Git/iwin/app_bi`
- `/Users/nick/Git/iwin/game_job`
- app_bi 目前分支：`main`
- game_job 目前分支：`main`
- app_bi path-specific log：`Payment.php`、`public/views/jjsj/mrucsjhz/**`
- game_job path-specific log：`GameDailyIwinJob.java`、`GameDailyAntplayAndPGJob.java`、`GameDailyDao.xml`

既有 Step 狀態：

| Step | 狀態 | 判斷 |
| --- | --- | --- |
| Step 1 | 可沿用 | 已列 candidate flows，且已同步此 flow 進度 |
| Step 2 | 可沿用 | 已同步 Step 5 為下一步 |
| Step 3 | 已完成 | 已確認 app_bi 查詢端與 game_job producer |
| Step 4 | 本次完成 | 轉成保守面試 case |
| Step 5 | 已完成 | 已判定不更新正式履歷 / 自傳 |

本次不做：

- 不更新正式履歷 / 自傳。
- 不宣稱 Nick 實作。
- 不新增 Step。
- 不把補跑 / 對帳升級成新任務名稱。

## 30 秒版本

我分析過一條每日遊戲資料彙總 flow。它不是單純後台查表，因為後台看到的數字是 game_job 每天從遊戲戰績日表整理出來的 projection。資料會先寫成 `log_game_daily_record type=0` 個體層，再聚合成 `type=1` summary，app_bi 後台才查 summary 顯示玩家數、投注、盈虧、殺率和留存。這條的重點是報表數字是否可信，例如 job 沒跑、時區切錯、重跑半成品、金額單位錯或留存窗口未成熟。

## 3 分鐘版本

```text
這條 flow 我會定位成 reporting projection，而不是交易主流程。

後台入口在 app_bi 的每日遊戲資料彙總頁，使用者選日期和平台後，前端呼叫 Payment::DailyGameDataSummary。這個 controller 查的是 log_game_daily_record type = 1，再用 SUM(CASE WHEN sub_type = ... THEN value1 ELSE 0 END) 把多種 summary row pivot 成一列報表。

但我不會只停在 app_bi controller，因為報表數字真正的風險在 producer。往上追到 game_job 後，可以看到 Antplay / PG 和 Iwin 分別有 daily job。job 會先刪除當日平台舊資料，再從 slots_logv1.log_reel_{date} 查原始戰績，寫入 type = 0 個體資料，再聚合出 type = 1 summary，最後計算新增玩家和 1 / 2 / 3 / 7 日留存。

這裡的 production 風險主要有幾個。

第一是批次重跑的一致性。delete + insert 如果中途失敗，可能造成當日資料空掉或只完成一半。

第二是時區與資料日。Antplay、PG、Iwin 的資料窗口不同，game_job history 也有時區修正紀錄，所以日期看起來同一天，實際抓取範圍可能不同。

第三是報表 projection 被誤當 source of truth。log_game_daily_record type=1 是 summary，不是原始交易真相。報表錯不一定代表交易錯，但會影響營運判斷。

第四是 observability。UI 有固定生成時間提示，但我沒有看到報表直接顯示 job 最後成功時間、row count、補跑狀態或 source log 到 summary 的對帳結果。

如果我是 owner，我會先補 job run status、last success time、row count / amount 對帳、補跑審計，再考慮 staging table 或版本化 summary，避免重跑時留下半成品。
```

## 面試主軸

### 面試官問：這不就是後台報表嗎？

可以回答：

不是只看後台查詢。這條 flow 的價值在於後台報表背後有 producer job、source log、projection 和 summary。報表本身不是 source of truth，但營運會拿它做判斷，所以 senior 要看的不是 controller 有沒有查出資料，而是資料怎麼產生、是否成熟、能不能重跑、出錯時能不能對帳。

### 面試官問：source of truth 是哪裡？

可以回答：

比較接近 source log 的是 `slots_logv1.log_reel_{date}`。`log_game_daily_record type=0` 是每日玩家層級 projection，`type=1` 是後台查詢用 summary。app_bi 報表本身不是交易 truth source。

### 面試官問：如果 job 跑一半失敗會怎樣？

可以回答：

從 code evidence 看，producer 會先 delete 當日平台舊資料，再寫 type=0、聚合 type=1、寫留存。如果中途失敗，可能出現空資料、只有 type=0、summary 不完整，或留存缺漏。這需要 job run status、補跑流程、row count 檢查，甚至 staging table 或版本化寫入，避免使用者看到半成品。

### 面試官問：時區為什麼重要？

可以回答：

因為這不是單一平台。Antplay、PG、Iwin 的資料日界線不同，game_job history 也有 PG 從 GMT+1 改 GMT+8、Antplay 改 GMT+0 的修正。報表日期看起來同一天，但實際抓取時間窗可能不同。時區錯會讓整天的投注、盈虧、留存都錯位。

### 面試官問：SUM pivot 有什麼風險？

可以回答：

app_bi 查詢端用 `SUM(CASE WHEN sub_type = ... THEN value1 ELSE 0 END)` 把 summary rows 轉成欄位。這對查詢很方便，但假如 producer 寫出重複 type=1 summary row，查詢端會把它加總放大。也就是查詢端假設 producer 端對每個 date / platform / sub_type 已經去重或重跑乾淨。

### 面試官問：留存數據為什麼要特別小心？

可以回答：

留存不是即時數據，它需要後續日期資料成熟。1 / 2 / 3 / 7 日留存需要對應天數的玩家集合。如果在資料窗口還沒成熟或 job 還沒跑完時查，數字可能偏低。這類報表最好標示資料生成時間、資料日窗口與最後成功 job 狀態。

### 面試官問：如果你接手，會先改什麼？

可以回答：

我會先補可觀測性和重跑安全，而不是先大重構。第一，補每個日期 / 平台的 job run status、開始結束時間、最後成功時間、row count、錯誤訊息。第二，補 source log 到 type=0 到 type=1 的 row count / amount 對帳。第三，補補跑審計，知道誰補跑、補哪天、補哪個平台、結果如何。第四，如果重跑風險高，再考慮 staging table 或版本化 summary，避免 delete 後失敗留下半成品。

## Senior 追問清單

- source log、type=0 projection、type=1 summary 誰可以被信任？
- delete + insert 中途失敗如何收斂？
- 重跑同一天是否 idempotent？
- app_bi 查詢端 SUM pivot 是否會放大重複資料？
- 金額單位在哪裡轉換？不同平台是否一致？
- Antplay / PG / Iwin 的資料窗口如何定義？
- 留存資料何時才算成熟？
- Excel 匯出是目前頁面還是全量結果？
- job 是否有 last success time？
- source log 到 summary 有沒有 reconcile？

## Lead / Architect 追問清單

- 報表 projection 是否需要 staging table？
- 是否需要 job run status table？
- 補跑 API 是否要有 approval / audit？
- 若同一天重跑多次，報表使用者怎麼知道目前是哪一版？
- 報表資料要 eventual consistency 還是要強一致確認後才可見？
- 如何避免 UI 固定提示時間和實際 job 狀態不一致？
- source log 到 projection 的資料品質檢查怎麼設計？
- 若有多平台不同時區，是否應把資料日與自然日分開建模？
- 這份報表是否會被其他下游當 source 使用？如果會，契約是什麼？

## 可展示 Senior 能力

- 從 app_bi 後台查詢往上追到 game_job producer。
- 分辨報表 projection 與交易 source of truth。
- 看懂 batch job 的重跑、時區、資料成熟度與 partial failure。
- 能用 code evidence 講風險，不腦補自己做過。
- 能把 owner decision 落到 job status、對帳、補跑審計與 staging table。

## 可說 / 不可說

### 可以說

- 我分析過每日遊戲資料彙總的 reporting flow。
- 我能從 app_bi 查詢端追到 game_job producer。
- 我能說明 source log、type=0 projection、type=1 summary、後台 pivot 查詢的邊界。
- 我能指出重跑、時區、金額單位、留存成熟度與 observability 風險。

### 必須保守說

- 這是 code-backed 分析素材。
- Nick 個人實作貢獻待確認。
- 目前不放正式履歷。

### 不能說

- 我主導每日遊戲資料彙總。
- 我負責 game_job 報表批次。
- 我設計資料平台或報表架構。
- 我建立完整補跑 / 對帳 / 告警機制。
- 我改善報表準確率、效能或穩定性百分比。

## 履歷候選句

目前不建議寫進正式履歷。

如果未來 Nick 補到本人 evidence，才可考慮非常保守版本：

```text
參與每日遊戲資料彙總 / 報表相關功能維護，協助釐清遊戲戰績來源、批次彙總、報表查詢與資料一致性邊界，提升報表數字排查與補跑風險判斷能力。
```

Step 5 結論：目前證據層級不足，不放入：

- `senior-owner-playbook/05-resume-master-zh.md`
- `senior-owner-playbook/08-application-autobiography-zh.md`

## Step 4 / Step 5 結論

這條 flow 已可作為保守面試 case。

Step 5 已完成履歷 / 自傳更新判定：不更新正式履歷 / 自傳。

目前定位：

- `flow.md`：主研究報告。
- `career-interview.md`：Nick 預設閱讀的保守面試 / 履歷素材。
- `materials/interview.md`：本 Step 4 詳細面試稿附錄。
- `materials/claim-boundary.md`：履歷 / 自傳更新邊界與 Step 5 結論。

下一步只推薦一件事：

```text
app_bi game-round-record-query Step 3
```

原因：

- `daily-game-record-summary` 已完成 Step 5，且不更新正式履歷 / 自傳。
- 依 KB，一條 flow 完成後回同 project candidate ranking。
- `game-round-record-query` 可作 troubleshooting flow，但 Step 3 要先補查詢端、log writer 與下游 source evidence。
