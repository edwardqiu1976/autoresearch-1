---
name: autoresearch
description: Autonomous Goal-directed Iteration for OpenClaw/OpenCode. Apply Karpathy's autoresearch principles to ANY task. Loops autonomously — modify, verify, keep/discard, repeat.
version: 1.0.0-openclaw
---

# AutoResearch for OpenClaw — Autonomous Goal-directed Iteration

Inspired by [Karpathy's autoresearch](https://github.com/karpathy/autoresearch) and [uditgoenka/autoresearch](https://github.com/uditgoenka/autoresearch). Applies constraint-driven autonomous iteration to ANY work — not just ML research.

**Core idea:** You are an autonomous agent. Modify → Verify → Keep/Discard → Repeat.

## Activation Triggers

- User says "autoresearch" or "自动研究"
- User says "work autonomously", "iterate until done", "keep improving"
- User says "自动迭代", "自主优化", "循环改进"
- User provides a goal with a measurable metric and wants autonomous improvement

## Quick Start

```
用户: 用autoresearch优化这个项目的测试覆盖率
我: 好的，让我先分析项目结构，确定优化目标和范围...
```

## Core Workflow

### Phase 0: Setup (Interactive)

**CRITICAL: Before starting ANY loop, collect required context.**

Use `AskUserQuestion` or direct conversation to collect:

| # | Field | Question | Example |
|---|-------|----------|---------|
| 1 | Goal | What do you want to improve? | "Increase test coverage to 90%" |
| 2 | Scope | Which files can be modified? | "src/**/*.ts" |
| 3 | Metric | What number measures success? | "coverage %" |
| 4 | Direction | Higher or lower is better? | "higher" |
| 5 | Verify | What command produces the metric? | "npm test -- --coverage" |
| 6 | Guard (optional) | What must ALWAYS pass? | "npm run build" |

**Validation gates:**
- Metric MUST be mechanical (outputs a parseable number)
- Verify command MUST work on current codebase (dry-run it)
- Scope MUST resolve to ≥1 file

### Phase 1: The Loop

```
LOOP (forever or N iterations):

1. REVIEW (30s)
   - Read current state of in-scope files
   - Read last 10-20 entries from results log
   - Run: git log --oneline -20
   - Run: git diff HEAD~1 (if last iteration was "keep")
   - Identify: what worked, what failed, what's untried

2. IDEATE
   - Fix crashes/failures from previous iteration first
   - Exploit successes — try variants of what worked
   - Explore new approaches
   - Combine near-misses
   - Simplify — remove code while maintaining metric
   - Radical experiments when incremental changes stall

3. MODIFY (One Atomic Change)
   - Make ONE focused change
   - Should be explainable in one sentence
   - Multi-file is OK if single intent

4. COMMIT (Before Verification)
   - git add <specific-files>
   - git commit -m "experiment(<scope>): <description>"
   - NEVER use git add -A

5. VERIFY
   - Run the verification command
   - Extract metric number
   - Timeout: 2x normal time

6. GUARD (if defined)
   - Run guard command
   - If fails: revert and rework (max 2 attempts)

7. DECIDE
   - IMPROVED + guard passed → KEEP
   - IMPROVED + guard failed → REVERT + rework (max 2)
   - SAME/WORSE → REVERT
   - CRASHED → fix (max 3) or revert

8. LOG
   - Record to results log (TSV format)

9. REPEAT
   - Unbounded: NEVER STOP
   - Bounded: Stop after N iterations, print summary

```

## Critical Rules

1. **Loop until done** — Unbounded: loop until interrupted. Bounded: loop N times.
2. **Read before write** — Always understand full context before modifying
3. **One change per iteration** — Atomic changes. If it breaks, you know exactly why
4. **Mechanical verification only** — No subjective "looks good". Use metrics
5. **Automatic rollback** — Failed changes revert instantly
6. **Simplicity wins** — Equal results + less code = KEEP
7. **Git is memory** — Every experiment committed. Use `git revert` (not reset)
8. **When stuck, think harder** — Re-read, combine near-misses, try radical changes

## Domain Adaptations

| Domain | Metric | Scope | Verify Command | Guard |
|--------|--------|-------|----------------|-------|
| Backend code | Tests pass + coverage % | `src/**/*.ts` | `npm test` | — |
| Frontend UI | Lighthouse score | `src/components/**` | `npx lighthouse` | `npm test` |
| ML training | val_loss / accuracy | `train.py` | `python train.py --eval` | — |
| Performance | Benchmark time (ms) | Target files | `npm run bench` | `npm test` |
| Security | Vulnerabilities found | API/auth | Security scan | — |
| Documentation | Validation pass rate | `docs/*.md` | Doc validator | — |

## Noise Handling

For volatile metrics (benchmarks, ML training):

```bash
# Multi-run verification (median of 3)
for i in 1 2 3; do npm run benchmark; done | sort -n | sed -n '2p'

# Minimum improvement threshold
# Only keep if improvement > 2%
```

## Output Format

### Results Log (TSV)

```
iteration	commit	metric	status	description
0	a1b2c3d	85.2	baseline	initial state
1	b2c3d4e	87.1	+1.9	keep	add tests for auth edge cases
2	-	86.5	-0.6	discard	refactor test helpers (broke 2 tests)
3	c3d4e5f	88.3	+1.2	keep	add error handling tests
```

### Progress Summary (every 10 iterations)

```
=== Autoresearch Progress (Iteration 25) ===
Metric: 88.3% (started: 85.2%)
Keeps: 8 | Discards: 15 | Crashes: 2
Best: #23 — add error handling tests (+1.2%)
```

### Final Summary

```
=== Autoresearch Complete (50/50 iterations) ===
Baseline: 85.2% → Final: 92.1% (+6.9%)
Keeps: 18 | Discards: 28 | Crashes: 4
Best iteration: #47 — optimize query caching (+2.1%)
```

## Comparison with Other Tools

| Feature | This Skill | karpathy/autoresearch | uditgoenka/autoresearch |
|---------|-----------|----------------------|------------------------|
| Platform | OpenClaw/OpenCode | Standalone | Claude Code |
| Domain | Any | LLM training only | Any |
| Git memory | ✅ | ✅ | ✅ |
| Guard mechanism | ✅ | ❌ | ✅ |
| Noise handling | ✅ | ❌ | ✅ |
| Sub-commands | ❌ | ❌ | ✅ (10 commands) |

## Examples

### Example 1: Increase Test Coverage

```
用户: 用autoresearch把测试覆盖率从72%提升到90%

我: 好的，让我设置autoresearch循环：

Goal: Increase test coverage to 90%
Scope: src/**/*.ts (excluding src/**/*.test.ts)
Metric: coverage %
Direction: higher is better
Verify: npm test -- --coverage 2>&1 | grep "All files" | awk '{print $4}'
Guard: npm run build

Baseline: 72%

开始迭代...

[Iteration 1] add tests for utils.ts → 74.1% (+2.1%) KEEP
[Iteration 2] refactor test helpers → 73.8% (-0.3%) DISCARD
[Iteration 3] add tests for api/auth.ts → 76.2% (+2.1%) KEEP
...
```

### Example 2: Optimize API Response Time

```
用户: 优化API响应时间到100ms以下

我: 设置autoresearch：

Goal: Reduce API response time to <100ms
Scope: src/api/**/*.ts
Metric: p95 latency (ms)
Direction: lower is better
Verify: wrk -t2 -c10 -d10s http://localhost:3000/api | grep 'Latency'
Guard: npm test

开始迭代...
```

## Integration with OpenClaw

This skill integrates with OpenClaw's features:

- **Sessions**: Can run in background sessions
- **Approvals**: Guard commands respect exec approvals
- **Memory**: Results logged to OpenClaw memory system
- **Skills**: Can be combined with other skills

## References

- `references/autonomous-loop-protocol.md` — Detailed loop protocol
- `references/core-principles.md` — 7 universal principles
- `references/results-logging.md` — TSV tracking format

## Credits

- [Andrej Karpathy](https://github.com/karpathy) — original autoresearch concept
- [Udit Goenka](https://github.com/uditgoenka) — Claude Code adaptation
