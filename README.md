# Evidence Market Brief

> Evidence-first public-news research briefs for US equities and liquid crypto assets.
>
> 面向美股与高流动性加密资产的证据优先公开新闻研究简报。

Evidence Market Brief is a reusable **Agent Skill** for evidence-first market-news research.

Evidence Market Brief 是一个可复用的 **Agent Skill**，用于证据优先的市场新闻研究。

[English](#english) · [中文](#中文) · [Skill specification](SKILL.md) · [Chinese schedule prompt](templates/cron-prompt.txt) · [English schedule prompt](templates/cron-prompt.en.txt)

---

<a id="english"></a>

## English

### What it is

Evidence Market Brief turns publicly available news leads into concise, auditable research briefs. It is designed for Hermes, OpenClaw, and similar agent environments that need a repeatable workflow for finding material updates across US equities and liquid crypto assets.

The output is a deliberately **research-only** artifact—not a trade signal. It separates verifiable facts from conditional reasoning, records source quality and uncertainty, and treats “no material update” as a valid result.

### What it is not

- Not personalized investment, legal, tax, or financial advice.
- Not a promise of performance, a price forecast, or a recommendation to trade.
- Not an automated trading, rebalancing, transfer, leverage, or derivatives tool.
- Not a substitute for primary-source due diligence.

`Buy / Watch / Do not buy` labels, when used, are **research-priority labels only**:

| Label | Meaning |
| --- | --- |
| `Buy` | Evidence and materiality justify prioritizing further due diligence. |
| `Watch` | Evidence, impact, or counterevidence remains incomplete. |
| `Do not buy` | The lead is stale, weak, unverified, or risk-dominant. |

They never mean that a user should place a trade.

### Evidence standard

1. **Primary sources first:** regulatory filings, company IR and statutory disclosures, exchanges, ETF issuers, official project announcements, and verifiable protocol/on-chain sources.
2. **Otherwise, two independent reliable outlets:** two reports must corroborate the same core fact without sharing a wire copy, single anonymous source chain, or recycled press release.
3. **Leads are not evidence:** RSS, search snippets, social posts, aggregators, screenshots, and single blogs can discover candidates but cannot support a conclusion.
4. **Numbers require two independent public sources:** prices, returns, volume, market cap, ETF flows, funding flows, and on-chain metrics are omitted when the source, timestamp, unit, or methodology cannot be reconciled.

See the full [source policy](references/source-policy.md), [event taxonomy](references/event-taxonomy.md), and [audit schema](references/audit-schema.md).

### How incremental runs work

Each run starts from the latest delivered report, not from an RSS timestamp. It creates an event fingerprint:

```text
entity or theme | event type | first confirmed time | core fact
```

Reposts, commentary, refreshed headlines, and price recaps are excluded. An already reported event is only reported again when there is a material, verifiable development—for example, a new primary source, official correction, regulatory or exchange action, or a material financial, governance, security, or on-chain status change.

If there is no verified material update, output exactly `[SILENT]` when the host supports silent delivery. Otherwise, use the visible [no-material-update example](examples/no-material-update.md). A failed run is not the same as “no news.”

### Quick start

1. Read [SKILL.md](SKILL.md) for the complete workflow and safety boundaries.
2. Choose a schedule prompt:
   - [中文提示词](templates/cron-prompt.txt)
   - [English prompt](templates/cron-prompt.en.txt)
3. Configure your own schedule, model, web access, state storage, and delivery destination in your agent environment.
4. Manually inspect the first run with the [report template](templates/report.md). Confirm the time window, source links, verification status, event fingerprints, risks, and exclusion log.

Do not place tokens, cookies, account details, positions, private chat IDs, or production schedule configuration in this repository.

### Install in your agent

There is no universal one-click installer: each host discovers Skills differently. A public Skill is agent instruction content, so **do not install from the mutable default branch and do not use a blind `git pull` update**. First select a reviewed, immutable **full 40-character commit SHA**. A release tag may help identify a release, but resolve it to—and pin—the full commit SHA; do not rely on a movable tag name or the branch tip. Until signed releases are published, obtain the reviewed SHA through a maintainer-trusted channel rather than from an unreviewed branch head.

Choose the target directory for your host:

| Host | Set `SKILL_DIR` to | Then |
| --- | --- | --- |
| **Hermes Agent** | `~/.hermes/skills/finance/evidence-market-brief` | Reload the Skill catalog or start a new task after verification. |
| **Codex** | `~/.codex/skills/evidence-market-brief` | Start a new Codex task and invoke the installed Skill. |
| **Claude Code** | `.claude/skills/evidence-market-brief` | Start a new session; confirm your Claude Code version supports project Skills. |
| **OpenClaw** | `<your-openclaw-skills-directory>/evidence-market-brief` | Use the configured discovery path, then reload or start a new task. |

Install the complete repository without checking out the default branch, then activate only the reviewed revision:

```bash
# Replace both placeholders. SKILL_REVISION must be a reviewed, full 40-character commit SHA.
SKILL_REPOSITORY="https://github.com/jhosonjames11/evidence-market-brief.git"
SKILL_REVISION="<FULL_40_CHARACTER_COMMIT_SHA>"
SKILL_DIR="$HOME/.hermes/skills/finance/evidence-market-brief" # replace for your host

# Refuse an existing target; inspect it manually instead of overwriting it.
test ! -e "$SKILL_DIR" || { echo "Target already exists: $SKILL_DIR"; exit 1; }
git clone --no-checkout "$SKILL_REPOSITORY" "$SKILL_DIR"
git -C "$SKILL_DIR" fetch origin "$SKILL_REVISION"
git -C "$SKILL_DIR" checkout --detach "$SKILL_REVISION"
test "$(git -C "$SKILL_DIR" rev-parse HEAD)" = "$SKILL_REVISION"
```

Only reload or invoke the Skill after the final check succeeds. Do not copy only `SKILL.md`: the linked templates and reference policies are part of the workflow. Use a native Skills/import UI only when it displays and pins the same reviewed full commit SHA; a UI that accepts only a repository or branch URL has the same mutable-branch risk.

**Agent-install message.** For an agent that has Git and local file access, paste this message instead of manually running a command:

```text
Install Evidence Market Brief from https://github.com/jhosonjames11/evidence-market-brief,
but only at this reviewed full 40-character commit SHA:
<FULL_40_CHARACTER_COMMIT_SHA>

Do not install or update from a branch tip. Clone with --no-checkout, fetch and
checkout that detached SHA, verify that `git rev-parse HEAD` equals it, then use
this host's standard Skills directory. Keep the full repository together; do
not copy secrets or private configuration, modify existing Skills, or activate
the Skill before the revision check succeeds.
```

**Verify and update.** Ask the host: “Use Evidence Market Brief to produce a research-only brief for the last 12 hours. If there is no verified material update, return `[SILENT]`.” Never update with `git pull`. Instead, select a new reviewed full commit SHA, confirm the expected `origin` URL and a clean working tree, fetch it, inspect the complete diff, and only then activate it:

```bash
SKILL_REPOSITORY="https://github.com/jhosonjames11/evidence-market-brief.git"
SKILL_DIR="$HOME/.hermes/skills/finance/evidence-market-brief" # replace for your host
NEXT_SKILL_REVISION="<NEXT_REVIEWED_FULL_40_CHARACTER_COMMIT_SHA>"
CURRENT_SKILL_REVISION="$(git -C "$SKILL_DIR" rev-parse HEAD)"

test "$(git -C "$SKILL_DIR" remote get-url origin)" = "$SKILL_REPOSITORY"
git -C "$SKILL_DIR" status --short # must produce no output
git -C "$SKILL_DIR" fetch origin "$NEXT_SKILL_REVISION"
git -C "$SKILL_DIR" diff "$CURRENT_SKILL_REVISION" "$NEXT_SKILL_REVISION" # inspect before continuing

# Run only after reviewing the diff and choosing to accept it.
git -C "$SKILL_DIR" checkout --detach "$NEXT_SKILL_REVISION"
test "$(git -C "$SKILL_DIR" rev-parse HEAD)" = "$NEXT_SKILL_REVISION"
```

Re-open the agent task only after the final check succeeds.

### Scheduling prompt

Use one of the prompt files above. They are portable instructions, not a scheduler configuration: the host decides frequency, persistence, delivery target, and available web tools. A sensible starting cadence is every 6–12 hours, but only after a manual validation run. Never treat a retrieval or delivery failure as a silent result.

### Output contract

For each material item, the brief should include:

- event time or first confirmed time, with timezone when known;
- source URL or identifiable source name;
- verified fact, clearly separated from research reasoning;
- verification status and evidence quality;
- unresolved risks, counterevidence, and the next condition that would justify an update.

The standard report sections are: scope and cutoff; cross-asset view; US equities; crypto; research-priority ranking; excluded leads; sources and verification; risks and next-cycle triggers.

### Repository layout

```text
SKILL.md                         Complete behavior specification
templates/report.md              Auditable report structure
templates/cron-prompt.txt        Chinese portable scheduling prompt
templates/cron-prompt.en.txt     English portable scheduling prompt
references/source-policy.md      Source hierarchy and numeric-data policy
references/event-taxonomy.md     Materiality, event identity, and de-duplication
references/audit-schema.md       Minimal run, event, metric, and exclusion fields
examples/                        Fictional, non-live output examples
```

### Publishing and contribution notes

- Keep examples fictional or fully reproducible from public sources; never fabricate live market results, backtests, or performance claims.
- Do not add secrets, credentials, personal data, account data, holdings, chat identifiers, or production cron configuration.
- Do not add automatic order placement, leverage guidance, regulatory evasion, or personalized recommendations.
- Check data-source terms and applicable local laws before operating a scheduled job.
- “Hermes” and “OpenClaw” are mentioned for interoperability only; this repository does not claim affiliation or official endorsement.

Licensed under the [MIT License](LICENSE).

---

<a id="中文"></a>

## 中文

### 这是什么

Evidence Market Brief 将公开新闻线索整理成简洁、可追溯、可复盘的研究简报，覆盖美股与高流动性加密资产。它适用于需要持续发现实质新增事件的 Hermes、OpenClaw 及其他 Agent 环境。

输出是研究材料，不是交易信号。它会区分可核验事实与条件化推理，记录来源质量和不确定性，并把“无重大新增”视为有效结果。

### 这不是什么

- 不是个性化投资、法律、税务或财务建议；
- 不是收益承诺、价格预测或交易推荐；
- 不会自动下单、调仓、转账、使用杠杆或操作衍生品；
- 不能替代对原始资料的独立尽调。

出现 `可买 / 观望 / 不买` 时，它们只表示**研究优先级**：

| 标签 | 含义 |
| --- | --- |
| `可买` | 证据与材料性足以优先做进一步尽调。 |
| `观望` | 证据、影响或反证尚不完整。 |
| `不买` | 线索已过时、证据薄弱、未核验或风险占优。 |

这些标签绝不等于应该下单。

### 证据标准

1. **一级来源优先：** 监管文件、公司 IR 与法定披露、交易所、ETF 发行方、项目官方公告以及可复核的协议/链上来源。
2. **否则使用两家独立可靠媒体：** 两篇报道要独立确认同一核心事实，不能是同一通讯社稿、同一匿名消息链或同一新闻稿的改写。
3. **线索不等于证据：** RSS、搜索摘要、社交媒体、聚合站、截图和单一博客只能发现候选，不能直接支持结论。
4. **数字必须双源：** 价格、涨跌幅、成交量、市值、ETF/资金流和链上指标，如果来源、时间、单位或口径无法相互印证，就删除具体数字。

完整细则请参阅[来源政策](references/source-policy.md)、[事件分类](references/event-taxonomy.md)和[审计字段](references/audit-schema.md)。

### 增量与去重

每次运行都以最近一次**已投递报告**为边界，而不是 RSS 时间。为候选生成事件指纹：

```text
实体或主题 | 事件类型 | 首次确认时间 | 核心事实
```

转载、评论、刷新标题和价格复述都应排除。已报告事件只有在出现可核验的实质进展时才可重报，例如新的一级来源、官方更正、监管/交易所措施，或财务、治理、安全、链上状态的实质变化。

没有可核验的重大新增时，如果宿主支持静默投递，应严格只输出 `[SILENT]`；若宿主需要可见审计记录，请使用[无重大新增示例](examples/no-material-update.md)。运行失败绝不等于“没有新闻”。

### 快速开始

1. 阅读 [SKILL.md](SKILL.md)，了解完整流程与安全边界。
2. 选择一个定时任务提示词：
   - [中文提示词](templates/cron-prompt.txt)
   - [English prompt](templates/cron-prompt.en.txt)
3. 在自己的 Agent 环境中配置频率、模型、网页访问、状态存储和投递目标。
4. 首次运行后，用[报告模板](templates/report.md)人工检查时间窗、来源链接、核验状态、事件指纹、风险和排除记录。

不要在此仓库保存 token、cookie、账户信息、持仓、私有聊天 ID 或生产 cron 配置。

### 一键安装到自己的 Agent

不同 Agent 的 Skill 发现机制不同，因此不存在真正通用的“一个按钮安装”。公开 Skill 属于会被 Agent 解释执行的指令内容，所以**不要从可变的默认分支安装，也不要盲目执行 `git pull` 更新**。先选择一个经过审阅、不可变的**完整 40 位 commit SHA**。发布 tag 只能帮助定位版本；应解析并固定到完整 commit SHA，不能只依赖可能被移动的 tag 名称或分支最新提交。在签名发布版本出现前，应从维护者信任的渠道取得已审阅 SHA，而不是从未审阅的分支头取得。

先为你的宿主选择目标目录：

| Agent | 将 `SKILL_DIR` 设为 | 然后做什么 |
| --- | --- | --- |
| **Hermes Agent** | `~/.hermes/skills/finance/evidence-market-brief` | 核验完成后重新加载 Skill 列表，或新开任务。 |
| **Codex** | `~/.codex/skills/evidence-market-brief` | 核验完成后新开 Codex 任务并调用已安装的 Skill。 |
| **Claude Code** | `.claude/skills/evidence-market-brief` | 新开会话；先确认 Claude Code 版本支持项目级 Skills。 |
| **OpenClaw** | `<你的-openclaw-skills-目录>/evidence-market-brief` | 使用已配置的发现目录，然后重新加载或新开任务。 |

完整安装仓库但不要检出默认分支；只在已审阅版本通过核验后激活：

```bash
# 替换两个占位符。SKILL_REVISION 必须是经过审阅的完整 40 位 commit SHA。
SKILL_REPOSITORY="https://github.com/jhosonjames11/evidence-market-brief.git"
SKILL_REVISION="<完整_40_位_COMMIT_SHA>"
SKILL_DIR="$HOME/.hermes/skills/finance/evidence-market-brief" # 按宿主替换

# 若目标已存在则拒绝操作；人工检查后再决定，不要覆盖。
test ! -e "$SKILL_DIR" || { echo "目标已存在：$SKILL_DIR"; exit 1; }
git clone --no-checkout "$SKILL_REPOSITORY" "$SKILL_DIR"
git -C "$SKILL_DIR" fetch origin "$SKILL_REVISION"
git -C "$SKILL_DIR" checkout --detach "$SKILL_REVISION"
test "$(git -C "$SKILL_DIR" rev-parse HEAD)" = "$SKILL_REVISION"
```

只有最后一项检查成功后，才重新加载或调用这个 Skill。不要只复制 `SKILL.md`，因为模板和来源政策也是工作流的一部分。只有原生 Skills/导入界面能显示并固定相同的、已审阅的完整 commit SHA 时才可使用；若它只能接受仓库或分支 URL，仍有可变分支风险。

**让 Agent 自行安装的消息。** 如果你的 Agent 能使用 Git 并访问本地文件，可以直接把下面这段话发给它：

```text
请从 https://github.com/jhosonjames11/evidence-market-brief 安装
Evidence Market Brief Skill，但只能使用这个经过审阅的完整 40 位 commit SHA：
<完整_40_位_COMMIT_SHA>

不要从分支最新提交安装或更新。请使用 --no-checkout 克隆，抓取并以 detached
状态检出该 SHA，确认 `git rev-parse HEAD` 与它一致后，再放入当前宿主的标准
Skills 目录。请保留完整仓库；不要复制密钥、私有配置或生产数据，不要修改已有
Skill，也不要在版本检查成功前激活此 Skill。
```

**验证与更新。** 安装后可对 Agent 说：“使用 Evidence Market Brief 为最近 12 小时生成一份仅研究用途的简报；没有经过核验的重大新增时返回 `[SILENT]`。”绝不要用 `git pull` 更新。应先选择新的、已审阅的完整 commit SHA，确认 `origin` URL 正确且工作区干净，抓取新版本并完整审阅差异后，才激活它：

```bash
SKILL_REPOSITORY="https://github.com/jhosonjames11/evidence-market-brief.git"
SKILL_DIR="$HOME/.hermes/skills/finance/evidence-market-brief" # 按宿主替换
NEXT_SKILL_REVISION="<下一个已审阅的完整_40_位_COMMIT_SHA>"
CURRENT_SKILL_REVISION="$(git -C "$SKILL_DIR" rev-parse HEAD)"

test "$(git -C "$SKILL_DIR" remote get-url origin)" = "$SKILL_REPOSITORY"
git -C "$SKILL_DIR" status --short # 必须没有输出
git -C "$SKILL_DIR" fetch origin "$NEXT_SKILL_REVISION"
git -C "$SKILL_DIR" diff "$CURRENT_SKILL_REVISION" "$NEXT_SKILL_REVISION" # 继续前完整审阅

# 仅在审阅差异并决定接受后执行。
git -C "$SKILL_DIR" checkout --detach "$NEXT_SKILL_REVISION"
test "$(git -C "$SKILL_DIR" rev-parse HEAD)" = "$NEXT_SKILL_REVISION"
```

只有最后一项检查成功后，才重新打开 Agent 任务。

### 定时任务提示词

上面的提示词是可移植的任务说明，不是某个具体调度器的配置。调度频率、持久化方式、投递目标和网页工具由宿主环境决定。建议先手动验证一次，再考虑每 6–12 小时运行；检索或投递失败必须记录为失败，不能静默。

### 输出契约

每个实质条目至少应包含：

- 事件发生/首次确认时间，以及已知时区；
- 来源 URL 或可识别的来源名称；
- 已核验事实，并与研究推理分开；
- 核验状态和证据质量；
- 未解决风险、反证，以及下次值得更新的触发条件。

标准报告章节为：本期范围与截至时间、跨资产总览、美股、加密货币、研究关注点排序、已排除线索、来源与核验状态、风险与下一周期触发条件。

### 发布与贡献说明

- 示例必须是虚构的，或能完全从公开来源复现；不得编造实时市场结果、回测或收益表现。
- 不得提交密钥、凭据、个人数据、账户数据、持仓、聊天 ID 或生产 cron 配置。
- 不得加入自动下单、杠杆建议、规避监管或个性化推荐。
- 运行定时任务前，请自行核对数据源使用条款和所在地法律法规。
- 文中提及 Hermes 和 OpenClaw 仅用于说明互操作性，不代表官方关联或背书。

本项目采用 [MIT License](LICENSE)。
