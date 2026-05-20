# iwin bi_share

`bi_share` 是 iwin 早期 PHP / Laravel 分享、推廣、佣金與 BI 報表相關 repo。

目前只完成 Career Track 的 rolling / scoped contribution claim consolidation，尚未建立 Flow Track 的 Step 1 / Step 2，也沒有把任何單條 flow 整理成正式 `flow.md` package。

## 目前文件

- [contribution-claim-consolidation.md](contribution-claim-consolidation.md)：先釐清 Nick 是否能把 `bi_share` 放進履歷 / 自傳。

## 目前判斷

`bi_share` 可作 code-backed 面試分析素材，但不建議放正式履歷主成果。

可分析的題材：

- 分享 / 推廣關係樹與邀請鏈路。
- 代理佣金設定、臨時返佣、佣金計算與 Redis / MySQL / MongoDB projection。
- 每日 / 每週財務統計、分享明細、邀請明細與 Excel 匯出。
- GM repair / Redis rebuild / batch recount 類營運修復入口。
- Legacy Laravel / PHP 升級、死碼路由與 k3s containerization 風險。

履歷邊界：

- 目前未掃到 Nick / `10gt12nc` 在 `bi_share` 的 direct production commits。
- 未有 Nick 本人確認 `bi_share` 實際開發範圍。
- 不寫 Nick 主導 `bi_share`、Laravel 升級、佣金系統、BI 報表系統、推廣分享系統或 k3s 遷移。
- 若面試提到，只能說「分析過 legacy BI / 分享 / 佣金報表系統，理解後台入口如何追到 batch、Redis、MongoDB 與營運修復邊界」。

## 下一步

若 Nick 要正式深挖 `bi_share`，應先做 Flow Track Step 1 / Step 2，不能直接寫履歷 claim。

```text
iwin bi_share Step 1
```
