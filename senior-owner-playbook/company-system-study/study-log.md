# Company System Study Log

狀態：已開始執行 packet。

本檔只記完成後的摘要，不保存聊天逐字稿，也不建立待辦債務。

## Log Template

```text
## YYYY-MM-DD - Project / Topic

- Packet:
- Evidence level:
- Why it mattered:
- Main takeaway:
- Interview usefulness: High / Medium / Low
- KB suggestion:
- Backfilled to formal materials: No / Yes, where
```

## Completed Packets

## 2026-06-26 - iwin / Provider callback

- Packet: `projects/iwin/2026-06-26-provider-callback.md`
- Evidence level: `專案存在 / code-backed` + `分析素材 / learning-only`
- Why it mattered: 第一包用來驗證 `Company System Study` 是否能把既有 flow 轉成 production decision knowledge；此 topic 直接支撐 Provider Integration、callback trust boundary、idempotency、MQ retry 與 troubleshooting。
- Main takeaway: provider callback 不是單純 API，而是 provider state、payment order、MQ consumer 與玩家餘額副作用的狀態收斂；最高風險是 callback ack / MQ enqueue failure window 與提現失敗退款 retry。
- Interview usefulness: High
- KB suggestion: 先留在 `company-system-study`，不要回填正式面試材料；若 Nick 後續偏好本包 30 / 90 秒口吻，再明確要求回填。
- Backfilled to formal materials: No
