---
name: autoresearch
description: Autonomous Goal-directed Iteration for OpenClaw/OpenCode. Apply Karpathy's autoresearch principles to ANY task. Loops autonomously — modify, verify, keep/discard, repeat.
description_zh: 自主目标导向迭代。将Karpathy的autoresearch原则应用于任何任务。自主循环 — 修改、验证、保留/丢弃、重复。
version: 1.0.0-openclaw
---

# AutoResearch for OpenClaw / OpenClaw版AutoResearch
# 自主目标导向迭代 — Autonomous Goal-directed Iteration

Inspired by [Karpathy's autoresearch](https://github.com/karpathy/autoresearch) and [uditgoenka/autoresearch](https://github.com/uditgoenka/autoresearch). Applies constraint-driven autonomous iteration to ANY work — not just ML research.
灵感来自[Karpathy的autoresearch](https://github.com/karpathy/autoresearch)和[uditgoenka/autoresearch](https://github.com/uditgoenka/autoresearch)。将约束驱动的自主迭代应用于任何工作——不只是ML研究。

**Core idea / 核心理念:** You are an autonomous agent. Modify → Verify → Keep/Discard → Repeat.
你是一个自主代理。修改 → 验证 → 保留/丢弃 → 重复。

---

## Activation Triggers / 激活触发词

**English:**
- User says "autoresearch"
- User says "work autonomously", "iterate until done", "keep improving"
- User provides a goal with a measurable metric and wants autonomous improvement

**中文:**
- 用户说 "自动研究"、"自主研究"
- 用户说 "自动迭代"、"自主优化"、"循环改进"
- 用户提供带有可量化指标的目标，希望自主改进

---

## Quick Start / 快速开始

```
用户: 用autoresearch优化这个项目的测试覆盖率
代理: 好的，让我先分析项目结构，确定优化目标和范围...

User: Use autoresearch to optimize test coverage for this project
Agent: OK, let me analyze the project structure first and define the optimization goal...
```

---

## Core Workflow / 核心工作流程

### Phase 0: Setup (Interactive) / 设置阶段（交互式）

**CRITICAL: Before starting ANY loop, collect required context.**
**关键：在开始任何循环之前，收集必要的上下文。**

Use `AskUserQuestion` or direct conversation to collect:
使用 `AskUserQuestion` 或直接对话收集：

| # | Field / 字段 | Question / 问题 | Example / 示例 |
|---|--------------|----------------|---------------|
| 1 | Goal / 目标 | What do you want to improve? / 你想改进什么？ | "Increase test coverage to 90%" / "把测试覆盖率提升到90%" |
| 2 | Scope / 范围 | Which files can be modified? / 哪些文件可以修改？ | "src/**/*.ts" |
| 3 | Metric / 指标 | What number measures success? / 什么数字衡量成功？ | "coverage %" |
| 4 | Direction / 方向 | Higher or lower is better? / 越高越好还是越低越好？ | "higher" |
| 5 | Verify / 验证 | What command produces the metric? / 什么命令产生指标？ | "npm test -- --coverage" |
| 6 | Guard (optional) / Guard（可选） | What must ALWAYS pass? / 什么必须总是通过？ | "npm run build" |

**Validation gates / 验证门槛:**
- Metric MUST be mechanical (outputs a parseable number) / 指标必须是机械的（输出可解析的数字）
- Verify command MUST work on current codebase (dry-run it) / 验证命令必须在当前代码库工作（试运行）
- Scope MUST resolve to ≥1 file / 范围必须解析到≥1个文件

---

### Phase 1: The Loop / 循环阶段

```
LOOP (forever or N iterations) / 循环（无限或N次）:

  1. REVIEW / 回顾 (30s)
     - Read current state of in-scope files / 读取范围内文件的当前状态
     - Read last 10-20 entries from results log / 读取结果日志最后10-20条
     - Run: git log --oneline -20 / 运行git log
     - Run: git diff HEAD~1 (if last iteration was "keep") / 运行git diff
     - Identify: what worked, what failed, what's untried / 识别：什么成功、什么失败、什么未尝试

  2. IDEATE / 构思
     - Fix crashes/failures from previous iteration first / 首先修复上次迭代的崩溃/失败
     - Exploit successes — try variants of what worked / 利用成功 —尝试成功的变体
     - Explore new approaches / 探索新方法
     - Combine near-misses / 组合接近成功的尝试
     - Simplify — remove code while maintaining metric / 简化 —删除代码但保持指标
     - Radical experiments when incremental changes stall / 当增量变化停滞时尝试激进实验

  3. MODIFY / 修改
     - Make ONE focused change / 做一个专注的改动
     - Should be explainable in one sentence / 应该能用一句话解释
     - Multi-file is OK if single intent / 如果是单一意图，多文件也可以

  4. COMMIT / 提交
     - git add <specific-files> / 添加特定文件
     - git commit -m "experiment(<scope>): <description>" / 提交实验
     - NEVER use git add -A / 永远不要用 git add -A

  5. VERIFY / 验证
     - Run the verification command / 运行验证命令
     - Extract metric number / 提取指标数字
     - Timeout: 2x normal time / 超时：正常时间的2倍

  6. GUARD / 保护
     - Run guard command / 运行guard命令
     - If fails: revert and rework (max 2 attempts) / 如果失败：回滚并重做（最多2次）

  7. DECIDE / 决策
     - IMPROVED + guard passed → KEEP / 改进+guard通过→保留
     - IMPROVED + guard failed → REVERT + rework / 改进+guard失败→回滚+重做
     - SAME/WORSE → REVERT / 相同/变差→回滚
     - CRASHED → fix (max 3) or revert / 崩溃→修复（最多3次）或回滚

  8. LOG / 记录
     - Record to results log (TSV format) / 记录到结果日志（TSV格式）

  9. REPEAT / 重复
     - Unbounded: NEVER STOP / 无限模式：永不停止
     - Bounded: Stop after N iterations, print summary / 有限模式：N次后停止，打印摘要

```

---

## Critical Rules / 关键规则

1. **Loop until done** — Unbounded: loop until interrupted. Bounded: loop N times.
   **循环直到完成** —无限：循环直到中断。有限：循环N次。

2. **Read before write** — Always understand full context before modifying
   **先读后写** —修改前总是理解完整上下文

3. **One change per iteration** — Atomic changes. If it breaks, you know exactly why
   **每次迭代只改一处** —原子化改动。如果出错，你确切知道原因

4. **Mechanical verification only** — No subjective "looks good". Use metrics
   **只用机械验证** —不用主观判断"看起来不错"。使用指标

5. **Automatic rollback** — Failed changes revert instantly
   **自动回滚** —失败的改动立即回滚

6. **Simplicity wins** — Equal results + less code = KEEP
   **简洁优先** —同样结果+更少代码=保留

7. **Git is memory** — Every experiment committed. Use `git revert` (not reset)
   **Git是记忆** —每个实验都提交。用`git revert`（不是reset）

8. **When stuck, think harder** — Re-read, combine near-misses, try radical changes
   **卡住时深思** —重读、组合接近成功的尝试、尝试激进改动

---

## Domain Adaptations / 领域适配

| Domain / 领域 | Metric / 指标 | Scope / 范围 | Verify Command / 验证命令 | Guard |
|--------------|--------------|-------------|-------------------------|-------|
| Backend / 后端 | coverage % | `src/**/*.ts` | `npm test` | — |
| Frontend / 前端 | Lighthouse score | `src/components/**` | `npx lighthouse` | `npm test` |
| ML Training / ML训练 | val_loss / accuracy | `train.py` | `python train.py --eval` | — |
| Performance / 性能 | Benchmark time (ms) | Target files | `npm run bench` | `npm test` |
| Security / 安全 | Vulnerabilities | API/auth | Security scan | — |
| Documentation / 文档 | Validation pass rate | `docs/*.md` | Doc validator | — |

---

## Noise Handling / 噪声处理

For volatile metrics (benchmarks, ML training):
对于波动指标（基准测试、ML训练）：

```bash
# Multi-run verification (median of 3) / 多次验证（取中位数）
for i in 1 2 3; do npm run benchmark; done | sort -n | sed -n '2p'

# Minimum improvement threshold / 最小改进阈值
# Only keep if improvement > 2% / 只在改进>2%时保留
```

---

## Output Format / 输出格式

### Results Log (TSV) / 结果日志（TSV）

```
iteration	commit	metric	status	description
0	a1b2c3d	85.2	baseline	initial state / 初始状态
1	b2c3d4e	87.1	+1.9	keep	add tests for auth edge cases / 添加auth边缘情况测试
2	-	86.5	-0.6	discard	refactor test helpers (broke 2 tests) / 重构测试助手（破坏2个测试）
3	c3d4e5f	88.3	+1.2	keep	add error handling tests / 添加错误处理测试
```

### Progress Summary / 进度摘要

```
=== Autoresearch Progress / 进度 (Iteration 25) ===
Metric: 88.3% (started: 85.2%)
Keeps: 8 | Discards: 15 | Crashes: 2
Best: #23 — add error handling tests (+1.2%)
```

### Final Summary / 最终摘要

```
=== Autoresearch Complete / 完成 (50/50 iterations) ===
Baseline: 85.2% → Final: 92.1% (+6.9%)
Keeps: 18 | Discards: 28 | Crashes: 4
Best iteration: #47 — optimize query caching (+2.1%)
```

---

## Examples / 示例

### Example 1: Increase Test Coverage / 示例1：提升测试覆盖率

```
用户: 用autoresearch把测试覆盖率从72%提升到90%

我: 好的，让我设置autoresearch循环：Goal: Increase test coverage to 90%
Scope: src/**/*.ts (excluding src/**/*.test.ts)
Metric: coverage %
Verify: npm test -- --coverage 2>&1 | grep "All files"
Guard: npm run build

Baseline: 72%

开始迭代...

[Iteration 1] add tests for utils.ts → 74.1% (+2.1%) KEEP
[Iteration 2] refactor test helpers → 73.8% (-0.3%) DISCARD
[Iteration 3] add tests for api/auth.ts → 76.2% (+2.1%) KEEP
...
```

### Example 2: Optimize API Response Time / 示例2：优化API响应时间

```
用户: 优化API响应时间到100ms以下

我: 设置autoresearch：Goal: Reduce API response time to <100ms
Scope: src/api/**/*.ts
Metric: p95 latency (ms)
Verify: wrk -t2 -c10 -d10s http://localhost:3000/api
Guard: npm test

开始迭代...
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

## References / 参考资料

- `references/autonomous-loop-protocol.md` — Detailed loop protocol / 详细循环协议
- `references/core-principles.md` — 7 universal principles / 7条通用原则
- `references/results-logging.md` — TSV tracking format / TSV追踪格式

---

## Credits / 致谢

- [Andrej Karpathy](https://github.com/karpathy) — original autoresearch concept / 原始autoresearch概念
- [Udit Goenka](https://github.com/uditgoenka) — Claude Code adaptation / Claude Code适配