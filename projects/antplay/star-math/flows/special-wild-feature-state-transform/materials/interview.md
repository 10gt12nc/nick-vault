# Interview Notes

## 1. 面試主軸

這條 flow 的主軸不是「我改了一個 wild」，而是:

> Slot math 的特殊 feature 會產生中間 state，但最後必須讓 scoring result、front-end display contract、free game state routing 三者一致。

## 2. 3 分鐘回答

我讀過一條 SFM 的 Special Wild flow。它的特殊 wild 不是直接把盤面某一格改成 wild，而是在 `initTable` 後依 game state 選不同 special wild type，進到 `changeWildState`。

裡面會先把 3x5 盤面轉成 15 格狀態，標出 parent wild，再依 W1 / W2 / W3 的規則生成 child wild。W2 是比較固定的同 reel 展開，W1 / W3 則會在可用位置裡隨機挑 child。這些 parent / child 會先用 family marker 表示，方便 feature state 計算。

但這些 marker 不能直接流到算分。算分前會透過 `revertSpecialWildsToNormal` 收斂成一般 wild `W=50`，再寫回 `symbols`。同時，前端動畫需要知道哪顆 wild 發動、哪些位置被轉換，所以會用 `extraData.launchIndex` 和 `transformIndex` 回傳。free game type 也會放在 `extraData[0].gameType`，factory 後面會依它進入不同 free game state。

這裡最重要的風險是中間 state 和對外 contract 不一致。例如我看到一個 direct commit 是「找不到父 wild 前端卡」，它把找不到 parent 的 fallback 從預設 reel 改成 `-1` 並 skip。這表示錯誤 default 值會把不存在的 parent 變成一個看似合法的位置，造成前端動畫卡住或顯示錯誤。

所以我會把這類 feature 當成 result contract 來看，不只是 math 內部邏輯：原始盤面、轉換後盤面、extraData、free game state 都要一致，debug / replay 才有辦法定位問題。

## 3. 常見追問

### Q1: 為什麼不直接讓 W1 / W2 / W3 參與算分？

因為 W1 / W2 / W3 在這條 flow 裡代表 feature state，而不是 paytable 要長期理解的 symbol。先用 marker 表示 parent / child family，算分前收斂成一般 W，可以讓 payline 判斷維持穩定，避免 feature 細節擴散到所有 scoring code。

### Q2: `extraData` 為什麼重要？

它不是單純 debug log。前端動畫需要 `launchIndex` / `transformIndex`，factory 也依 `gameType` 路由到不同 free game state。所以 `extraData` 是 display contract 和 state transition contract。

### Q3: 這和 backend consistency 有什麼關係？

這和交易系統裡「內部狀態」與「對外結果」一致很像。內部可以有 pending / retry / compensation 等中間狀態，但對外結果、客戶端展示、後續流程的輸入必須一致，而且不能用錯誤 default 值掩蓋 unknown。

### Q4: 你怎麼 debug 這類問題？

我會先收集原始盤面 `originalSymbols`、轉換後 `symbols`、`extraData`、RNG / round id，再確認前端使用的 index mapping 是否和 math module 一致。若是找不到 parent / transform index 不一致，就先檢查 fallback 與 eligible position 過濾條件。

### Q5: `slc-math` LuckyClover 和這條差在哪？

SFM Special Wild 是單局內的 parent / child transform，最後收斂成普通 wild。SLC LuckyClover 是跨 free spin 的 sticky board tracker，狀態會延續到下一轉。兩者都需要 result contract，但 state lifetime 不同。

## 4. Lead / Architect 追問

### Q6: 這條 flow 的 source of truth 是哪個？

對算分來說，最後寫回的 `symbols` 是 scoring source of truth；對前端動畫來說，`extraData` 是 display contract；對 free game routing 來說，`extraData[0].gameType` 又是 state transition input。所以這條 flow 不是單一欄位 source of truth，而是三個 contract 要彼此一致。

### Q7: 如果前端說動畫卡住，但金額看起來正確，你會怎麼查？

我會先看該 round 的 `originalSymbols`、final `symbols` 和 `extraData`，確認 parent / transform indexes 是否能在盤面上找到，再比對 math module 的 index mapping 和前端 animation mapping。若是 missing parent，就檢查是否有 fallback 到有效 reel 或 transform index 超出合法位置。

### Q8: 為什麼這類 bug 不能只靠 try-catch？

try-catch 只能避免 process 掛掉，不能保證 contract 正確。錯誤 fallback 可能讓系統回傳看似完整但語意錯誤的 result，這比明確 skip / fail 更難排查。

### Q9: 你會怎麼設計回歸測試？

我會固定 RNG 或指定盤面，覆蓋 W1 / W2 / W3、沒有 parent、parent 在不同 reel、多 parent、excluded symbol、free game type routing。測試不只看 win amount，也要 assert `symbols`、`originalSymbols`、`extraData.launchIndex`、`transformIndex` 和 `gameType`。

### Q10: 面試時怎麼保守講 Nick 的貢獻？

可以說 Nick / `10gt12nc` 在 `sfm-math` 相關 path 有 direct commits，且有 `找不到父 wild 前端卡` 這類 feature contract bugfix evidence；但不說主導完整 Special Wild 設計，也不說主導 LuckyClover。

## 5. 面試回答公式

```text
先說業務現象：特殊 wild 會改變玩家看到的盤面。
再說 code 狀態：internal marker、final symbols、extraData 三層。
接著說風險：display contract、scoring result、free game state 不一致。
然後說 owner decision：unknown 不用有效值 fallback，marker 不外漏到算分。
最後補 claim boundary：這是 `sfm` direct evidence，不誇大成完整 math owner。
```

## 6. 一句話版

> 我會把 slot feature transform 當成 contract consistency 問題：math 內部可以有 parent / child marker，但最後 scoring symbol、前端 extraData、free game state 必須一致，否則就會出現顯示卡住或 replay 困難。
