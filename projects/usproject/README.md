# usproject

用途: 整理 `/Users/nick/Git/usproject` 相關 repo 與外部 API contract 文件的 Senior Backend / Platform Backend 學習價值與安全邊界。

本 domain 目前是候選 source domain。2026-06-22 只完成 `ugsoft-apidoc Step 1 + Step 2`，尚未建立正式 Flow Track，也未做履歷 / 自傳 claim gate。

來源 repo:

```text
/Users/nick/Git/usproject
```

相關文件 mirror:

```text
/Users/nick/Git/antplay/ugsoft-apidoc
```

## Project Status

2026-06-22 `ugsoft-apidoc Step 1 + Step 2` 已完成：新增 [ugsoft-apidoc-source-notes.md](ugsoft-apidoc-source-notes.md)，把本機 API doc mirror 定位成 `/Users/nick/Git/usproject` 的 external API contract / supporting source，整理可用範圍、安全邊界與 usproject repo 對應關係。這不新增正式履歷 claim，也不更新 `05 / 08`。

## 讀檔順序

1. [ugsoft-apidoc-source-notes.md](ugsoft-apidoc-source-notes.md)：先看 API doc mirror 的安全邊界、可引用內容、不可搬入 vault 的內容，以及和 usproject repo 的初步對應。
2. `projects/source-repo-inventory.md`：確認 `/Users/nick/Git/usproject` 只是來源索引，不是 evidence 或履歷 claim。
3. 若後續 Nick 指定做 usproject Flow Track，再補 `step1-candidate-flows.md` 與 `step2-flow-comparison.md`；沒有 Step 2 前不得直接建立單條 flow Step 3。

## 初步 module 對應

| Source repo | 初步定位 | 本輪判斷 |
| --- | --- | --- |
| `game-api` | Slot game runtime / player entry / game init / bet / token session / math loading | 目前只讀 README；可和 API doc 的進入遊戲、單一錢包、下注 / 結算 / 取消下注、通知補償脈絡對照 |
| `admin-api` | Admin / control plane API | README 多為模板；只確認有後台 API 與 public feature doc 線索，不作履歷 claim |
| `game-job` | Job / retry / async notification candidate | README 多為模板；可和 `game-api` README 的下注通知 / 取消注單 / Quartz 補償脈絡對照，尚未深掃 code |
| `game-web` / `admin-web` | 前端入口 | 只作 runtime / admin 操作入口，不當 Backend 主履歷主線 |
| `game-push` | Push / notification candidate | 本輪未深掃；只列為待確認 supporting repo |
| `nbt` / `nbt-playground` | 可能的 game / math / playground related repo | 本輪未深掃；不作 claim |
| `usproject-deploy` / `usproject-deploy-template` | Deploy / environment template | 只作部署脈絡；不搬內部環境資訊，不作 DevOps claim |
| `usproject-workspace` | Workspace / documentation / reconstruction support | 只作 source navigation / KB supporting；不作 production service claim |

## Claim Boundary

- 可安全說：`ugsoft-apidoc` 能作為 usproject 的 API contract / integration learning source，幫助理解 wallet mode、bet / settle / cancel、transfer order、bonus / free spin、signature、currency / language 等外部行為邊界。
- 暫不可說：Nick 主導 usproject、主導 UGSoft API、主導完整 wallet / reconciliation、完整 game platform owner、完整 provider integration owner。
- 暫不可寫進 `05 / 08`：本輪沒有掃 code branch / git history / Nick direct evidence，也沒有本人確認。

## 下一步邊界

若 Nick 後續要繼續，下一步應是 `usproject ugsoft-apidoc Step 3`：只補 API contract 可講 case，不改履歷 / 自傳，不新增正式 claim。

若 Nick 要正式建立 usproject Flow Track，必須先做 project-level `Step 1 -> Step 2`，並在掃 code repo 前確認 remote refs / local HEAD / branch 狀態；不能從 API doc 直接跳到單條 flow Step 3。
