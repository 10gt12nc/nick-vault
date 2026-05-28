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
- Step 4 已可作正式面試素材，但不直接寫入 `05 / 08`；是否回填 project-level claim 等 Step 5。

## 不可說

- 不說主導完整 security platform。
- 不說完整 API gateway / WAF / IAM owner。
- 不說完整 Game API runtime owner。
- 不說 DB / Redis 強一致已完全解決。
- 不說完整 production incident owner。
- 不說量化改善。

## 待 Step 5 判斷

- 是否回填 `antplay-slot-admin-api` project-level contribution refresh。
- 是否只作 project-level supporting evidence。
- 是否與 UGSoft `game-api-provider-white-ip-control-plane` 合併成「後台 control plane / provider IP whitelist」通用履歷素材。
