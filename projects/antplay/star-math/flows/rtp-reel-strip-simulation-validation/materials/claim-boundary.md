# Claim Boundary - RTP / 輪帶模擬與驗證

## 1. 可放履歷

只建議併入 `*-math` grouped bullet：

> 參與 AntPlay 多個 slot math module 維護與驗證，處理 RTP / reel strip、simulation validation、buy free / scatter、jackpot / symbol 與 fixedMultiBet / currency 類調整。

證據層級:

- `真實開發過 + code-backed`
- `sph-math`、`spn-math` 有 Nick / `10gt12nc` direct commits。
- `*-math` contribution consolidation 已確認 49 / 71 repo 有 Nick / `10gt12nc` commits。

## 2. 可面試講

- RTP target / tolerance 如何拆成 Base RTP、FG trigger、Free RTP、Jackpot hit rate。
- 候選輪帶如何透過 `GenerateByRatio` 產生。
- `RTP_REEL_STRIP_OPTIMIZER` 如何讓 simulation 使用候選輪帶。
- 為什麼 validation 要走真實 `P21008SlotMath` / `AbstractSlotMath#spin`。
- 為什麼 `lastSymbols` 這種 feature state 會影響 simulation correctness。

## 3. 只能說參與 / 維護 / 驗證

目前可以說：

- 參與 slot math module 維護與驗證。
- 參與 RTP / reel strip / simulation 相關調整。
- 能分析 validation flow 的 failure window。

目前不應說：

- 主導完整 RTP 策略。
- 獨立設計遊戲數學模型。
- 負責 certification。
- 負責完整 simulator platform。
- 全部 `*-math` repo 都是 Nick 主導。

## 4. 待補 Evidence

若未來要把 claim 升級，需要補：

- GDD / math spec 對應版本。
- 正式 validation report 或 QA / certification output。
- production reel strip diff 與 release ticket。
- MR / ticket / reviewer comment。
- 上線後 RTP / hit rate monitoring 或 incident evidence。

## 5. 本 flow Step 5 判定

本 flow 已完成 Step 5 單條 flow claim gate。正式履歷 master 不因單條 flow 直接新增 standalone bullet；要更新 05 / 08，仍以 `*-math` project-level contribution consolidation 的 grouped claim 為準。

Step 5 gate 結論:

- 可放履歷：只併入 `*-math` grouped bullet，作 RTP / reel strip / simulation validation 的強化 evidence。
- 可面試講：可作完整 3 分鐘 high-risk domain validation case。
- 不可誇大：不寫成完整 RTP 策略 owner、完整遊戲數學模型 owner、certification owner 或 full simulator platform owner。
- 本輪 05 / 08：不直接更新；後續 rolling resume update 可引用本 flow evidence。

下一步回 Step 2 ranking，做 Rank 3:

```text
antplay *-math buy-free-scatter-rtp3-result-contract Step 4
```
