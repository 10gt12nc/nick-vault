# JD 客製投遞包

這個資料夾放特定職缺的投遞 / 面試客製包。

原則：

- 一個公司 / 一個 JD / 一個資料夾。
- `05-resume-master-zh.md` 與 `08-application-autobiography-zh.md` 維持通用主版本。
- 有明確 JD 時，先做 fit analysis，判斷是否值得投、值得 market check、或只是略過。
- 只有 Nick 想投、值得 market check，或 Nick 明確要求「客製履歷 / 自傳 / 問答」時，才建立完整客製包。
- 一般職缺只需要簡短 fit analysis；不要每看到一個 JD 就產完整履歷包。

客製包可以包含：

- JD fit analysis。
- 104 客製工作經驗、專長、自傳、自我推薦。
- HR / 薪資 / 文化 / 技術問答。
- 20% 基本功最小複習清單：只針對 JD 與主力 cases 可能追問的 Java / Spring / SQL / Redis / MQ / microservice 原理，不建立泛用八股題海。
- 投遞前閱讀與練習排序。

流程固定：

```text
JD -> fit analysis -> 決定是否客製 -> 客製履歷 / 自傳 / 問答 -> 若有面試回饋再回填
```

面試準備比例仍維持：

```text
70% production case / system design / claim boundary
20% JD-specific Java / Spring / SQL / Redis / MQ 基本功
10% coding test / LeetCode 保險
```

保守邊界：

- 客製履歷仍必須對齊 `05 / 08 / 04 / 17` 的 claim boundary。
- 不因 JD 寫 Senior、架構、mentor，就把 Nick 包裝成正式 Tech Lead、架構師或完整系統 owner。
- 薪資與 HR 說法可依 JD 調整，但不得用未證實的 owner / architect / 量化改善抬價。

命名建議：

```text
applications/{company-or-role}-{date}/
```
