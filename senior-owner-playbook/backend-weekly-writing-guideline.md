# Backend Weekly Writing Guideline

狀態：`Backend Weekly Learning` 的寫作風格規則。

用途：`backend-weekly-plan.md` 決定學什麼，`backend-weekly-template.md` 決定輸出哪些章節，本檔決定每一週要怎麼寫。它不是新課綱、不是新 prompt、不是新 backlog。

## 核心原則

把每一週當成同一本書的下一章，不要每週重新寫一本新書。

目標不是「把一個主題講完」，而是讓 Nick 每週都更像一個能處理 production、incident、trade-off 與 interview 追問的 backend engineer。

## 寫作規則

### 1. Build, Don't Repeat

- Treat each week as the next chapter of the same book.
- Do not rewrite previous chapters.
- Assume the reader remembers previous core concepts.
- Reference previous weeks briefly when needed, then move on.

例：Week 02 不需要重新解釋 dual-write；只要寫「Week 01 已介紹 DB success / event failed，本週聚焦 propagation 如何影響 transaction boundary」。

### 2. Introduce One New Production Insight

每週至少要有 1 個 genuinely new production insight。

避免連續幾週都重複同一句：

```text
transaction boundary
failure window
dual write
idempotency
source of truth
```

這些可以引用，但每週要往前推進一層。

### 3. Include Explicit Engineering Judgment

Every weekly packet should contain at least one explicit engineering judgment.

不要只介紹技術。要像資深工程師 / reviewer 一樣說清楚：

- I would accept this design when...
- I would reject this design when...
- I consider this over-engineering because...
- I would not invest in this now because...

判斷要具體，最好能落到 code review、incident review 或 design review。

例：

```text
I would reject calling an external provider inside a DB transaction.
Reason: it increases lock time, timeout coupling, and rollback ambiguity.
```

```text
I would not use REQUIRES_NEW as a default fix.
It can preserve audit records, but it also creates partial-success states and extra connection pressure.
```

### 4. Capability First

Teach capability, not isolated knowledge.

每週都要回答：

- Why does this exist?
- When should it be used?
- What trade-off does it create?
- What can fail in production?
- When should Nick NOT use this approach?

### 5. Production Over Textbook

定義要短，production reasoning 要長。

優先順序：

1. production thinking
2. troubleshooting
3. trade-off
4. decision making
5. interview answer quality
6. textbook completeness

### 6. Don't Over-explain Beginner Concepts

如果前幾週已經講過 beginner concept，不要重新鋪陳。

做法：

- brief reference
- state what is new this week
- continue deeper

### 7. Progressive Depth

每週要建立在前一週之上。

例如：

```text
Week 01 Transaction -> local transaction boundary
Week 02 Propagation / Isolation / Rollback -> transaction boundary decisions
Week 03 Self Invocation / AOP Proxy -> why expected transaction may not apply
Week 04 Async / Thread Pool -> transaction and thread boundary split
```

### 8. Technology Landscape Is Not Encyclopedia

`Technology Landscape` 不是百科列表。

它只回答：

- current mainstream
- when each technology is a better fit
- Learn Now
- Learn Later
- Awareness Only

Never recommend learning every related technology.

### 9. Keep Examples Realistic

不要把一般 backend 主題硬套成 Google / Netflix 級架構。

優先用一般 production backend 場景：

- order
- wallet
- payment callback
- report projection
- audit log
- batch job
- admin operation
- legacy troubleshooting

### 10. Conservative Evidence

不要把 Nick 沒做過的事情寫成做過。

固定區分：

- verified from Nick's documented experience
- inferred from general engineering practice
- speculative ideas for future improvement

### 11. Long-term Consistency

每週風格要一致，但語氣不要模板化。

避免每週都用同樣句型：

```text
Senior 要...
Production 要...
不要幻想...
```

可以保留判斷，但換成更自然的工程說法。

## 檢查問題

每週輸出前，用這 6 題自查：

1. 這週是否建立在前幾週之上，而不是重新介紹？
2. 這週是否至少有 1 個新的 production insight？
3. 這週是否至少有 1 個明確 engineering judgment，例如 accept / reject / over-engineering / not worth it now？
4. 是否避免重複解釋已學過的 beginner concept？
5. 是否把重點放在 production / troubleshooting / trade-off，而不是百科完整性？
6. 是否沒有把 inference 或 future improvement 寫成 Nick 已做過？
