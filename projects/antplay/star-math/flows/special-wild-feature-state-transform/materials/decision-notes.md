# Decision Notes

## 1. 中間 marker 與 scoring symbol 分離

觀察:

- Special Wild transform 會把 parent / child 暫時標成 family marker。
- 算分前再把 marker 收斂成一般 wild `W=50`。

Owner decision:

- 內部 feature state 可以比對外 contract 更豐富，但 scoring contract 要保持穩定。
- 若 payline / symbol count 需要理解每個 special family id，複雜度會擴散到整個 math module。

面試可講:

- 這類設計像後端 domain model 和 external DTO 的分層。內部可以保留細節，對外輸出要收斂成穩定 contract。

## 2. `extraData` 不是 log，而是 result contract

觀察:

- `addExtraDataForWilds` 記錄 launch / transform indexes。
- `P21008SlotMath#checkFreeGame` 寫 `gameType`。
- `SFMMathFactory` 依 `extraData[0].gameType` 決定 free game spin state。

Owner decision:

- `extraData` 改動要視為 contract change，不是無害附加欄位。
- 任何 transform / fallback 修正都要檢查前端動畫、free game routing、debug replay。

面試可講:

- 我會把它拆成 scoring result、display result、state routing 三種 contract，一起檢查一致性。

## 3. 找不到 parent 時要顯式失敗或 skip

觀察:

- `acac921 找不到父 wild 前端卡` 將找不到 parent 的 fallback 從有效 reel 改成 `-1`，呼叫端 skip。

Owner decision:

- 不要用有效 domain value 表示 unknown / not found。
- 對 feature transform 來說，錯誤 fallback 可能比明確 skip 更危險，因為它會製造一個看似合法但實際錯誤的動畫位置。

面試可講:

- 這和金流狀態也類似：unknown 不能 default 成 success 或第一個 channel，要有明確狀態。

## 4. Random transform 要能 replay / debug

觀察:

- W1 / W3 的 child 位置由 RNG 決定。
- code 保存 `originalSymbols`、final `symbols` 與 `extraData`。

Owner decision:

- Randomness 要可觀測，不一定要把每一步都持久化，但至少 result contract 要足夠支援客服 / QA / debug。
- 若要追 production dispute，最少需要原盤、終盤、transform metadata、RNG / request id / round id。

面試可講:

- Slot math 的可重現性不只靠 log message，而是靠 result contract 與 debug input 設計。

## 5. `slc` sticky tracker 對照

觀察:

- LuckyClover 是跨 free spin 的 board tracker。
- locked positions、gold / blue coin value、extra spin 都會影響下一轉。

Owner decision:

- `sfm` 是單局 transform 後收斂，`slc` 是跨 spin state 延續。兩者都要定義清楚 state owner。
- 若 state owner 不清楚，factory、ThreadLocal tracker、round result payload 可能會出現不同步。

面試可講:

- 我會先問 state 是 single-round、multi-round 還是 cross-service，再決定是否需要持久化、snapshot、idempotency 或 replay contract。
