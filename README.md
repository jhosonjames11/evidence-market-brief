# us-crypto-news-buy-brief

一个面向 Hermes、OpenClaw 及同类 Agent 的中文市场新闻研究 Skill。它把公开新闻线索整理为有来源、可去重、可复盘的美股与加密货币研究简报。

它是 research-only 工具：不自动交易、不提供个性化投资建议，也不承诺收益。`可买 / 观望 / 不买` 仅表示事件的研究优先级与证据质量判断，不能当作下单信号。

## 适合的场景

- 定时整理上一报告周期之后的公开市场事件；
- 要求每个重要事实能追溯到原始出处；
- 需要把重复新闻、未核验传言和不合格数字排除在简报外；
- 希望把“无实质新增”作为有效、可审计的结果。

不适用于自动下单、资产配置、杠杆/衍生品建议、收益预测或单一资产的完整基本面研究。

## 快速开始

1. 阅读 [SKILL.md](SKILL.md) 的边界、来源政策和去重规则。
2. 将 [定时任务提示词](templates/cron-prompt.txt) 粘贴到自己的 Agent 任务中。
3. 由你自己的运行环境配置频率、模型、网页访问和投递目标；不要在此仓库保存 token、cookie、chat ID 或生产配置。
4. 首次运行后，用 [报告模板](templates/report.md) 检查来源、时间窗、数字双源核验、事件指纹和不确定性披露。

如果没有完成核验的实质新增，任务应根据宿主的投递语义输出 `[SILENT]` 或使用 [无重大新增示例](examples/no-material-update.md)，不要用旧闻填充。

## 内容结构

```text
SKILL.md                    行为规范与工作流
templates/report.md         可审计的报告结构
templates/cron-prompt.txt   可移植的定时任务提示词
references/source-policy.md 来源分级与数字政策
references/event-taxonomy.md 事件材料性、身份与去重规则
references/audit-schema.md  最小可追溯字段
examples/                   虚构、非实时的格式示例
```

## 设计原则

- **Evidence-first**：优先监管、IR、交易所、发行方与协议官方资料；若只能依赖媒体，至少需要两家彼此独立的可靠媒体。
- **Incremental**：以“上次已投递报告”为边界，而非 RSS 发布时间；重复转载与评论不重复报送。
- **Auditable**：重要结论保留时间、来源、事实/推理分界、核验状态、风险与待验证问题。
- **Conservative with numbers**：价格、流量、市值等数字未经两个独立公开来源确认时不写。
- **No fabricated causality**：同时发生的新闻与涨跌不能自动构成因果关系。

## 贡献

欢迎提交能提高可追溯性、来源独立性、去重准确性和安全边界的改进。请勿提交私有数据、凭据、实时交易记录、收益宣传或个性化投资建议。新增示例必须明确标为虚构或可公开复现，并附其验证方法。

## 许可证

本项目采用 [MIT License](LICENSE)。
