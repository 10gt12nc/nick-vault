# ugsoft-admin-api

`ugsoft-admin-api` 是 UGSoft 後台 API / control plane repo，使用 Java / Spring Boot，包含後台登入 / 權限、商戶 / provider 設定、白名單、報表查詢、風控監控、RabbitMQ consumer、Quartz job 與 request / bet record 類資料處理。

## 目前整理狀態

| 項目 | 狀態 |
| --- | --- |
| contribution claim consolidation | 已完成 / rolling |
| Step 1 / Step 2 | 尚未正式建立；Flow Track 缺口 |
| Flow packages | 尚未建立；不得宣稱 flow 完整 |
| 正式履歷 | 可保守補入「後台控制面與非同步資料處理」 |

## 先讀

- [contribution-claim-consolidation.md](contribution-claim-consolidation.md)

## 履歷邊界

可用:

- 參與 `ugsoft-admin-api` 後台 API / control plane 開發與維護。
- 參與 login / JWT / RBAC、商戶與 provider 白名單、超級代理、報表查詢、風控監控、RabbitMQ request log / bet record、Quartz / report job 類功能。
- 可面試延伸：權限邊界、白名單 runtime cache、MQ 消費冪等、request log / bet record 非同步化、報表查詢時間窗口、job 重跑與資料一致性。

不可誇大:

- 不寫成主導完整 UG 平台、完整 provider gateway、完整 money / wallet owner。
- 不寫量化改善、完整事故 owner、完整 RabbitMQ architecture owner。
- `ugsoft-connector-api` 相關 provider gateway 要另掃，不能直接由 admin API 反推。
