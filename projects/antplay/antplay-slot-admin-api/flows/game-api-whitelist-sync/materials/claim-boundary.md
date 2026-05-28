# game-api-whitelist-sync Claim Boundary

日期: 2026-05-28

## 可說

- 參與 AntPlay Game API 白名單控制面開發與維護。
- 處理後台新增 / 刪除 / 查詢白名單，並同步 Redis runtime cache。
- 理解 game-api `WhiteIpFilter` 如何讀 Redis 判斷 request source IP 是否放行。
- 能分析 DB / Redis 雙寫一致性、batch sync 部分成功、operation log 與 role scope。

## 只能保守說

- 可說「參與」或「開發維護相關流程」。
- 可說「支援商戶 API 來源 IP 管控」。
- 可說「code-backed，且有 Nick / `10gt12nc` direct commits」。
- Step 5 已完成單條 flow claim gate，可作 project-level supporting evidence，但不直接寫入 `05 / 08`；正式履歷要等 project-level contribution refresh 或 rolling resume package 統一回填。

## 不可說

- 不說主導完整 security platform。
- 不說完整 API gateway / WAF / IAM owner。
- 不說完整 Game API runtime owner。
- 不說 DB / Redis 強一致已完全解決。
- 不說完整 production incident owner。
- 不說量化改善。

## Step 5 判斷

- 可回填 `antplay-slot-admin-api` project-level contribution refresh，作為「後台 API / 商戶控制面 / Game API 白名單同步」的 supporting evidence。
- 不建議單獨寫成履歷主成果；若履歷篇幅有限，併入 AntPlay 後台 API / control plane 一句即可。
- 可和 UGSoft `game-api-provider-white-ip-control-plane` 合併成「後台 control plane / provider IP whitelist」通用面試素材，但不得擴張成完整 access-control platform owner。
