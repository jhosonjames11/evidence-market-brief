# 最小审计字段

每次运行应保留足以复盘的记录。字段可使用 Markdown、JSON、数据库或宿主的持久化记忆实现；不要保存私有凭据、账户或个人数据。

## 运行级字段

| 字段 | 说明 |
| --- | --- |
| `report_id` | 本次报告的唯一标识 |
| `generated_at` | 生成时间与时区 |
| `window_start` / `window_end` | 实际调查时间窗 |
| `previous_report_boundary` | 上轮已投递报告的边界或 ID |
| `result` | `material_updates`、`no_material_update` 或 `run_failed` |
| `coverage_notes` | 窗口扩展、资料不可得等限制 |
| `dedupe_method` | 使用的事件指纹或规则摘要 |

## 事件级字段

| 字段 | 说明 |
| --- | --- |
| `event_fingerprint` | `实体 | 类型 | 首次确认时间 | 核心事实` |
| `entities` | 标的、公司、协议或主题 |
| `event_time` | 发生/确认时间，含时区与时间类型 |
| `newness` | `new`、`material_update` 或 `excluded_duplicate` |
| `facts` | 可验证、无推断的事实摘要 |
| `sources` | 名称、URL、层级、访问/确认时间 |
| `verification_status` | 见来源政策中的枚举 |
| `reasoning` | 条件化研究推理与前提 |
| `research_priority` | `可买`、`观望`、`不买`；必须注明不是交易建议 |
| `risks_and_counterevidence` | 未确认点、相反事实、替代解释 |
| `next_trigger` | 何种可验证事实会值得重新报告 |

## 数字级字段（仅在使用数字时）

| 字段 | 说明 |
| --- | --- |
| `metric` | 价格、成交量、流量等指标名称 |
| `value` / `unit` | 原始数值与单位 |
| `as_of` | 数据对应时间与时区 |
| `methodology` | 净额/总额、交易对、采样窗口等口径 |
| `source_a` / `source_b` | 两个独立公开来源及 URL |
| `comparison_result` | 可比/不一致/无法核验；后两者不在报告给出数字 |

## 排除项字段

对每个候选至少记录 `candidate_reference`、`checked_at`、`exclusion_reason`（重复、旧闻、仅线索、无法核验、无材料性或不在范围）和（如适用）关联的事件指纹。

这些字段让下一次运行能正确建立增量边界，也让读者区分“无新闻”“无合格新增”和“运行失败”。
