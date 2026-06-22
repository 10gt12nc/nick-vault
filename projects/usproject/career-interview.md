# usproject career / interview notes

更新日期：2026-06-22

本檔目前只整理 `ugsoft-apidoc` API contract 可轉成的面試素材。它是 supporting / learning material，不是正式履歷 master，也不是 Nick direct development evidence。正式履歷仍以 `senior-owner-playbook/05-resume-master-zh.md` 與 `08-application-autobiography-zh.md` 為準。

## 定位

可安全說：

```text
我會把這類 slot game API contract 拆成 wallet mode、game entry、bet / settle / cancel、transfer order、bonus / free spin、signature / currency / language 幾層來看。重點不是背 API 文件，而是先判斷哪裡是 runtime source of truth、哪裡只是外部 callback contract、哪裡需要 query-after-timeout 或補償觀念。
```

不可說：

- 不說真實開發過 `usproject`。
- 不說主導 UGSoft API 規格。
- 不說主導完整 slot game platform。
- 不說完整 wallet / ledger / reconciliation owner。
- 不把 API 文件理解包裝成 Nick direct commit / ticket / production issue evidence。

## ugsoft-apidoc supporting cases

來源：[ugsoft-apidoc-source-notes.md](ugsoft-apidoc-source-notes.md)。以下只作 API contract / 面試口說補強；不得搬入 token、Authorization header、API key、商戶金鑰、安全碼、內網 IP、正式環境 URL、客戶資料或可直接操作環境的指令。

### Case 1：Game entry / wallet mode 怎麼講

30 秒版：

```text
這份 API contract 我會先從 game entry 和 wallet mode 切開看。進入遊戲前通常會有預登入、帳號或 token 驗證；接著要判斷是轉帳錢包還是單一錢包。轉帳錢包重點是轉入 / 轉出與查單，單一錢包重點是每次下注、結算或取消下注時和外部平台保持一致。
```

追問時怎麼接：

- `game entry` 不是單純導頁，它牽涉帳號建立 / 驗證、token、幣別、語系與遊戲紀錄入口。
- `轉帳錢包` 偏向先把資產轉入遊戲側，交易過程內部承接；風險會集中在 transfer order、query order、退出 / 回收餘額。
- `單一錢包` 偏向每次交易都和外部平台互動；風險會集中在 bet / settle / cancel 的 idempotency、timeout 與狀態不確定。
- 真的讀 code 時，要回到 runtime service、job compensation、admin query 與 frontend entry 分層，不把 API 文件當 source of truth。

不要這樣講：

- 不說自己設計了 game entry / wallet mode。
- 不說這份文件證明自己做過 usproject。
- 不把轉帳錢包與單一錢包混成同一種交易模型。

### Case 2：Single wallet bet / settle / cancel 怎麼講

30 秒版：

```text
單一錢包模式下，我會把 Bet、Settle、BetNSettle、CancelBet 看成一組 money correctness 問題。下注成功代表扣款或鎖定投注成立；結算代表派彩結果回寫；下注同時結算是合併型 contract；取消下注則通常用來處理下注後流程異常或結果不能成立的補償。
```

追問時怎麼接：

- `Bet` 要關注同一 bet id 重送時是否會重複扣款，以及餘額不足、幣別、遊戲局資訊不一致時怎麼回應。
- `Settle` 要關注它是否一定對應已存在的 bet，以及重送時是否能回傳一致結果。
- `BetNSettle` 方便短流程遊戲，但要更小心同一 request 同時產生扣款與派彩兩種副作用。
- `CancelBet` 不是隨便 rollback；它要處理的是下注流程異常、timeout 不確定、遊戲流程失敗等場景，面試可以接到 idempotency / compensation / query-before-retry。

不要這樣講：

- 不說已實作 single wallet。
- 不說所有 single wallet 都一定有完整 transaction / outbox / exactly-once。
- 不說 API success 就等於後台報表或玩家畫面一定已同步完成。

### Case 3：Transfer wallet order / query 怎麼講

30 秒版：

```text
轉帳錢包模式我會看三件事：先建立或準備轉帳訂單，再執行資產轉移，最後能查餘額與查訂單。這類 flow 的核心不是 API 名稱，而是 order state 是否清楚、timeout 後能不能 query、重送會不會造成重複轉入或轉出。
```

追問時怎麼接：

- `CreateTransferOrder` 類流程要看 order id 是否唯一、建立後是否有明確狀態，以及失敗時是否能安全重試。
- `TransferChip` 類流程要看 money side effect 是否和 order state 綁定，避免 API 重送造成重複上分 / 下分。
- `QueryBalance` / `QueryOrder` 是 timeout 後判斷結果的重要工具；不能只靠 client timeout 直接認定成功或失敗。
- `PrepareExitGame` / 退出流程要關注遊戲側剩餘資產、轉出、查單與回到平台餘額的邊界。

不要這樣講：

- 不說自己 owner 轉帳錢包。
- 不說查單等於完整 reconciliation。
- 不把 transfer wallet 文件包裝成完整 ledger / wallet source of truth。

### Case 4：Bonus / FreeSpin / contract boundary 怎麼講

30 秒版：

```text
Bonus 和 FreeSpin 我會把它當成 reward correctness 問題，不只看能不能發送活動。要看贈送對象、幣別 / 語系 / 遊戲清單、有效條件、是否會和 bet / settle 流程交錯，以及重送或查詢時如何避免重複發獎。
```

追問時怎麼接：

- `Bonus` / `FreeSpin` 通常是營運入口，但最後會影響玩家可玩的局、可領的獎或後續結算，所以不能只用 CRUD 思維看。
- 需要確認 campaign / reward id、player identity、game identity 與 currency 是否一致。
- 如果 reward 下發失敗或 timeout，面試可以接到 retry、dedupe、查詢狀態與人工補償邊界。
- 附錄裡的 signature、language、currency 看似瑣碎，但在跨國遊戲 / 多幣別場景會直接影響對接穩定性與排查成本。

不要這樣講：

- 不說完整活動 / reward platform owner。
- 不說完整 free spin engine owner。
- 不把營運 API 理解包裝成正式活動系統開發 claim。

## 面試追問防線

| 追問 | 回答方向 |
| --- | --- |
| 你是不是做過 usproject？ | 目前不能這樣說。這份是 API contract / supporting material；除非後續有 Nick 本人確認、commit、ticket 或 code-backed consolidation，否則只說用來理解類似系統的交易邊界。 |
| API 文件是不是 source of truth？ | 不是。API 文件是外部 contract；真正狀態仍要看 runtime DB / wallet state / bet record / transaction record / job 補償與下游回應。 |
| Bet / Settle timeout 怎麼處理？ | 不直接認定成功或失敗。先用 request id / bet id / order id 查狀態，確認是否已有副作用，再決定 retry、cancel 或補償。 |
| 你會怎麼避免重複扣款或重複派彩？ | 用唯一 business id、idempotency check、狀態機、查單與重送一致回應；但不宣稱這份文件已證明現有 code 做到了。 |
| 轉帳錢包和單一錢包差在哪？ | 轉帳錢包先轉入遊戲側再玩，重點是 transfer order / balance / exit；單一錢包每次交易和外部平台互動，重點是 bet / settle / cancel 的 consistency。 |

## Relationship Check

本輪事實變更是：`ugsoft-apidoc` 已從 source boundary 進一步整理成可面試講的 API contract supporting cases。

影響判斷：

- `projects/usproject/README.md`：需要加入 Step 3 狀態與本檔入口。
- `projects/usproject/ugsoft-apidoc-source-notes.md`：需要補充 Step 3 已完成與本檔關聯。
- `05-resume-master-zh.md` / `08-application-autobiography-zh.md`：本輪不新增正式 claim，不更新。
- `04-interview-casebook.md`：本輪只是 usproject 候選 domain 的 supporting cases，未升級成全域主力 case，不更新。
- `17-salary-negotiation.md` / `06-todo.md`：不影響談薪或長期待辦，不更新。
