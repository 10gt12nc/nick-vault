# Claim Boundary - fixedMultiBet / Currency / Math-Core Compatibility

## Step 5 結論

這條 flow 通過單條 flow claim gate。

判定：

- 可放履歷：可以，但只能作為 `*-math` grouped 經驗的一部分。
- 可面試講：可以，且適合作為 Senior Backend / Platform Backend 差異化案例。
- 不可誇大：不能寫成完整 slot math platform owner、完整 RTP 策略 owner、全部 `*-math` module owner。
- 不更新 05 / 08：本輪是單條 flow Step 5，05 / 08 原則上吃 project-level contribution consolidation；目前 grouped `*-math` consolidation 已有保守句，後續 rolling resume package 再回填更精準 wording。

## 可放履歷

推薦句：

> 參與 AntPlay slot math core 與多個 slot math module 維護，處理 fixedMultiBet、currency、debug bet、totalBet / jackpot scaling 等相容性與驗證問題，確保遊戲數學輸出與上游下注金額語意一致。

也可縮短為：

> 參與多個 AntPlay slot math module 維護，處理 fixedMultiBet、currency、debug bet 與 total bet 相容性調整。

證據層級：

- `真實開發過 + code-backed`
- `math-core`、`sdt-math`、`sfm-math` 有 Nick / `10gt12nc` direct commits。
- `slc-math` 作 code-backed 旁證與部分 direct evidence。

## 可面試講

- `math-core` default fallback 如何在多 module 環境中降低改版風險。
- `sdt` / `slc` 如何把 fixedMultiBet 帶入 totalBet 與 jackpot scaling。
- `sfm` 如何呈現 currency multiplier 與 math-core compatibility 的差異。
- 為什麼 debug bet 也必須支援 currency / fixedMultiBet。
- 為什麼這類純計算 module 仍然要以 money-like correctness 看待。
- 為什麼 core interface 支援不等於全部 module 已完整支援。

## 只能面試講，不建議直接放履歷

- ThreadLocal currency multiplier 的風險與改善方向。
- 是否要把 fixedMultiBet / currency 提升到 core result contract。
- module compatibility checklist。
- fallback module 的支援程度矩陣。

這些是很好的 Senior 追問素材，但目前沒有完整 rollout / issue / PR evidence，不適合寫成正式履歷成就。

## 不能誇大

- 不說主導完整遊戲數學模型。
- 不說負責全部 `*-math` repo。
- 不說完整 RTP 策略 / simulator / certification owner。
- 不說所有 module 都已完整支援 fixedMultiBet / currency。
- 不說 math module 負責錢包 transaction rollback。
- 不說已處理 production incident / 線上事故，除非後續補到 ticket / incident evidence。
- 不說改善百分比、提升多少穩定性或降低多少錯誤率。

## 待補 evidence

- 上游 game-api / runtime caller 是否實際使用 `currency` / `fixedMultiBet` 新簽名。
- 實際 release / issue / MR context。
- test result 或 simulation output。
- 其他 high-evidence `*-math` module 的相同 pattern。

## Step 5 後續判斷

這條 flow 已完成 Step 5。依 KB，單條 flow 完成不代表整個 `*-math` Flow Track 完成；下一步應回 Step 2 ranking，做第二條代表 flow：

```text
antplay *-math rtp-reel-strip-simulation-validation Step 4
```
