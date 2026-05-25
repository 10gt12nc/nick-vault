# math-core Contribution Claim Consolidation

日期: 2026-05-20

## 結論

`math-core` 可以列為 Nick / `10gt12nc` 真實開發過 + code-backed 的 slot math core library。Direct commits 觸及 `SlotMath` interface、`DebugBetVO`、`RtpType`、`Symbol` 等共用 contract / debug / symbol / RTP 類路徑。

履歷可保守寫:

> 參與 AntPlay slot math core 維護，處理 SlotMath contract、debug bet VO、currency / fixedMultiBet、RTP type 與 jackpot / symbol 類相容調整。

不要寫:

- 不得寫成主導完整 slot math framework。
- 主導遊戲數學模型、RTP 策略或派彩設計。
- 不得寫成主導完整 simulator / validation platform。

## Evidence Summary

| 類別 | 判斷 | Evidence |
| --- | --- | --- |
| 直接開發 | 真實開發過 | `shortlog --all` 顯示 `10gt12nc` 約 19 筆、`nick` 約 1 筆 |
| math core contract | 真實開發過 + code-backed | `SlotMath` 增加 currency / fixedMultiBet 類 overload |
| debug tooling | 真實開發過 + code-backed | `DebugBetVO` 增加 / 修正 `fixedMultiBet`、`currency` |
| symbol / RTP | 真實開發過 + code-backed | `Symbol` jackpot symbol、`RtpType` RTP_3 / RTP_4 |
| final flow | 待補 | 尚未建立 Step 1 / Step 2 與 flow packages |

## Source Scan Record

來源 repo:

```text
/Users/nick/Git/antplay/math-core
```

遠端狀態:

- 已嘗試執行 `git fetch --all --prune`，但 remote refs 未能更新。
- local branch: `master`
- local HEAD: `7f1533bcede2601940204b9d6ab2a032d175ebf3`
- 本機既有 `origin/master`: `7f1533bcede2601940204b9d6ab2a032d175ebf3`
- local vs 本機既有 `origin/master`: `0 / 0`
- 重要限制：fetch 失敗，不能宣稱已看最新 remote。

本次掃描範圍:

- `git shortlog -sne --all`
- `git log --all --author='10gt12nc|Nick|nick'`
- key commits: `ef7ff33`、`d08109b`、`274c1ea`、`5327a19`、`792882a`、`e34413b`、`c254b57`
- source code: `SlotMath`、`DebugBetVO`

## Important Evidence

重要 commits:

- `ef7ff33`: fix debugBet VO。
- `d08109b`: debugBet VO 加 currency。
- `274c1ea`: fixedMultiBet。
- `e34413b`: `SlotMath` 增加 fixedMultiBet 類 overload。
- `5327a19` / `792882a`: jackpot symbol。
- `c254b57`: 加 `RTP_3`、`RTP_4`。

可放履歷:

- 參與 slot math core contract / debug bet tooling / RTP enum / symbol 相容調整。
- 參與支援多幣別與 fixedMultiBet 的 math debug / runtime contract 維護。

可面試講:

- `SlotMath` interface 如何作為 game-api / math module 的 contract。
- 為什麼 debug bet VO 要帶 `currency`、`fixedMultiBet`、RTP option。
- 共用 enum / symbol 變更對多個 math module 的相容性風險。

不可誇大:

- 不說完整 math framework owner。
- 不說設計完整 RTP / 派彩模型。
- 不說完整 math release / validation owner。

## Suggested Next

`math-core` 已有可用履歷素材，但要變成面試 case，下一步先做 Step 1，比較 `SlotMath contract compatibility`、`debug bet tooling`、`RTP / symbol extension` 哪個最值得深挖。

```text
antplay math-core Step 1
```
