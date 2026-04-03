<div align="center">

# AutoResearch for OpenClaw / OpenClaw版 AutoResearch

**Turn OpenClaw/OpenCode into a relentless improvement engine.**
**将 OpenClaw/OpenCode 变成不知疲倦的优化引擎。**

Based on [Karpathy's autoresearch](https://github.com/karpathy/autoresearch) — constraint + mechanical metric + autonomous iteration = compounding gains.
基于 [Karpathy 的 autoresearch](https://github.com/karpathy/autoresearch) —约束 + 可量化指标 + 自主迭代 = 复合增长。

[![OpenClaw Skill](https://img.shields.io/badge/OpenClaw-Skill-blue?logo=cli&logoColor=white)](https://openclaw.ai)
[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://github.com/edwardqiu1976/autoresearch-1)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

[![Based on](https://img.shields.io/badge/Based_on-Karpathy's_Autoresearch-orange)](https://github.com/karpathy/autoresearch)
[![Original](https://img.shields.io/badge/Original-Claude_Code_Version-purple)](https://github.com/uditgoenka/autoresearch)

<br>

*"Set the GOAL → Agent runs the LOOP → You wake up to results"*
*"设定目标 → 代理运行循环 → 你醒来看到结果"*

*You don't need AGI. You need a goal, a metric, and a loop that never quits.*
*你不需要 AGI。你需要一个目标、一个指标，和一个永不放弃的循环。*

<br>

[How It Works / 工作原理](#how-it-works-工作原理) · [Quick Start / 快速开始](#quick-start-快速开始) · [Examples / 示例](#examples-示例) · [Comparison / 对比](#comparison-对比)

</div>

---

## Why This Exists / 为什么存在这个项目

[Karpathy's autoresearch](https://github.com/karpathy/autoresearch) demonstrated that a 630-line Python script could autonomously improve ML models overnight — **100 experiments per night** — by following simple principles: one metric, constrained scope, fast verification, automatic rollback, git as memory.

[Karpathy 的 autoresearch](https://github.com/karpathy/autoresearch) 证明了只需630行Python脚本就能在一夜之间自主改进ML模型——**每晚100次实验**——遵循简单的原则：单一指标、受限范围、快速验证、自动回滚、git作为记忆。

**This version adapts those principles for OpenClaw/OpenCode.** Not just ML — code, content, marketing, sales, HR, DevOps, or anything with a number you can measure.

**这个版本将这些原则适配到 OpenClaw/OpenCode。** 不只是ML——代码、内容、营销、销售、HR、DevOps，或任何有可量化数字的领域。

---

## How It Works / 工作原理

```
LOOP (FOREVER or N times) / 循环（无限或N次）:

  1. Review current state + git history + results log/ 回顾当前状态 + git历史 + 结果日志
  2. Pick the next change / 选择下一个改动
  3. Make ONE focused change / 做一个专注的改动
  4. Git commit / Git提交
  5. Run mechanical verification / 运行机械验证
  6. If improved → keep. If worse → revert. / 改进→保留。变差→回滚。
  7. Log the result / 记录结果
  8. Repeat. Never stop until you interrupt. / 重复。直到你中断才停止。
```

Every improvement stacks. Every failure auto-reverts. Progress is logged in TSV format.
每次改进都会累积。每次失败自动回滚。进度以TSV格式记录。

### The Setup Phase / 设置阶段

Before looping, the agent performs a one-time setup:
循环开始前，代理执行一次性设置：

1. **Read context** — reads all in-scope files / **读取上下文** —读取所有范围内的文件
2. **Define goal** — extracts or asks for a mechanical metric / **定义目标** —提取或询问可量化指标
3. **Define scope** — which files can be modified vs read-only / **定义范围** —哪些文件可修改，哪些只读
4. **Establish baseline** — runs verification on current state / **建立基线** —在当前状态运行验证
5. **Confirm and go** — shows setup, then begins the loop / **确认并开始** —显示设置，然后开始循环

### 8 Critical Rules / 8条关键规则

| # | Rule (English) | 规则（中文） |
|---|----------------|-------------|
| 1 | **Loop until done** — unbounded: forever. Bounded: N times | **循环直到完成** — 无限模式：一直循环。有限模式：N次后停止 |
| 2 | **Read before write** — understand full context before modifying | **先读后写** —修改前先理解完整上下文 |
| 3 | **One change per iteration** — atomic changes | **每次迭代只改一处** —原子化改动 |
| 4 | **Mechanical verification only** — no subjective "looks good" | **只用机械验证** —不用主观判断"看起来不错" |
| 5 | **Automatic rollback** — failed changes revert instantly | **自动回滚** —失败的改动立即回滚 |
| 6 | **Simplicity wins** — equal results + less code = KEEP | **简洁优先** —同样结果+更少代码=保留 |
| 7 | **Git is memory** — experiments committed, use `git revert` | **Git是记忆** —实验提交，用`git revert` |
| 8 | **When stuck, think harder** — re-read, combine, try radical | **卡住时深思** —重读、组合、尝试激进方案 |

---

## Quick Start / 快速开始

### Installation / 安装

```bash
# Clone the repository / 克隆仓库
git clone https://github.com/edwardqiu1976/autoresearch-1.git

# Copy to OpenClaw skills directory / 复制到 OpenClaw skills 目录
mkdir -p ~/.openclaw/skills/autoresearch
cp -r autoresearch-1/openclaw/* ~/.openclaw/skills/autoresearch/
```

### Usage / 使用方法

In OpenClaw/OpenCode, just say:
在 OpenClaw/OpenCode 中，只需说：

```
用 autoresearch 把测试覆盖率从 72% 提升到 90%
```

Or in English:
或用英文：

```
Use autoresearch to increase test coverage from 72% to 90%
```

The agent will automatically start the iteration loop.
代理会自动开始迭代循环。

### Activation Triggers / 激活触发词

- "autoresearch" / "自动研究"
- "work autonomously" / "自主工作"
- "iterate until done" / "迭代直到完成"
- "keep improving" / "持续改进"
- "自动迭代" / "自主优化" / "循环改进"

---

## Examples / 示例

### Example 1: Test Coverage / 示例1：测试覆盖率

```
用户: 用 autoresearch 把测试覆盖率从72%提升到90%

代理: 好的，让我设置 autoresearch 循环：

Goal: Increase test coverage to 90%
Scope: src/**/*.ts
Metric: coverage %
Verify: npm test -- --coverage
Guard: npm run build

Baseline: 72%

开始迭代...

[Iteration 1] add tests for utils.ts → 74.1% (+2.1%) KEEP
[Iteration 2] refactor test helpers → 73.8% (-0.3%) DISCARD
[Iteration 3] add tests for api/auth.ts → 76.2% (+2.1%) KEEP
...
```

### Example 2: API Performance / 示例2：API性能

```
用户: 优化 API 响应时间到 100ms 以下

代理: 设置 autoresearch：

Goal: Reduce API response time to <100ms
Scope: src/api/**/*.ts
Metric: p95 latency (ms)
Verify: wrk -t2 -c10 -d10s http://localhost:3000/api

开始迭代...
```

### Example 3: ML Training / 示例3：ML训练

```
用户: 用 autoresearch 优化模型准确率到95%

代理: 设置 autoresearch：

Goal: Improve model accuracy to 95%
Scope: train.py, model.py
Metric: validation accuracy %
Verify: python train.py --eval

开始迭代...
```

---

## Domain Adaptations / 领域适配

| Domain / 领域 | Metric / 指标 | Scope / 范围 | Verify Command / 验证命令 |
|--------------|--------------|-------------|-------------------------|
| Backend / 后端代码 | coverage % | `src/**/*.ts` | `npm test` |
| Frontend / 前端UI | Lighthouse score | `src/components/**` | `npx lighthouse` |
| ML Training / ML训练 | val_loss / accuracy | `train.py` | `python train.py --eval` |
| Performance / 性能 | Benchmark time (ms) | Target files | `npm run bench` |
| Security / 安全 | Vulnerabilities | API/auth | Security scan |

---

## Comparison / 版本对比

| Feature / 特性 | This Version / 本版本 | karpathy/autoresearch | uditgoenka/autoresearch |
|----------------|---------------------|----------------------|------------------------|
| Platform / 平台 | OpenClaw/OpenCode | Standalone | Claude Code |
| Domain / 领域 | Any / 任意 | LLM training only | Any |
| Git memory / Git记忆 | ✅ | ✅ | ✅ |
| Guard mechanism / Guard机制 | ✅ | ❌ | ✅ |
| Noise handling / 噪声处理 | ✅ | ❌ | ✅ |
| Sub-commands / 子命令 | ❌ | ❌ | ✅ (10 commands) |
| Bilingual docs / 双语文档 | ✅ | ❌ | ❌ |

---

## File Structure / 文件结构

```
autoresearch-1/
├── openclaw/                    # OpenClaw/OpenCode version/ OpenClaw/OpenCode 版本│
   ├── SKILL.md                 # Main skill file/ 主技能文件│
   └── references/              # Reference documents/ 参考文档│
       ├── core-principles.md   # 7 universal principles/ 7条通用原则│
       └── results-logging.md   # TSV tracking format/ TSV追踪格式
├── claude-plugin/               # Claude Code version (original)/ Claude Code 版本（原版）
├── guide/                       # User guides/ 用户指南
└── scripts/                     # Utility scripts/ 工具脚本
```

---

## Integration with OpenClaw / 与OpenClaw集成

This skill integrates with OpenClaw's features:
此技能与OpenClaw的功能集成：

- **Sessions**: Can run in background sessions / **会话**：可在后台会话运行
- **Approvals**: Guard commands respect exec approvals / **审批**：Guard命令遵循exec审批
- **Memory**: Results logged to OpenClaw memory system / **记忆**：结果记录到OpenClaw记忆系统
- **Skills**: Can be combined with other skills / **技能**：可与其他技能组合

---

## Credits / 致谢

- **[Andrej Karpathy](https://github.com/karpathy)** — original autoresearch concept / 原始autoresearch概念
- **[Udit Goenka](https://github.com/uditgoenka)** — Claude Code adaptation / Claude Code适配
- **[Edward Qiu](https://github.com/edwardqiu1976)** — OpenClaw/OpenCode adaptation / OpenClaw/OpenCode适配

---

## License / 许可证

MIT License — see [LICENSE](LICENSE) file.
MIT许可证 — 见[LICENSE](LICENSE)文件。