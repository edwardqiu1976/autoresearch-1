# Core Principles — 7 Universal Rules from Autoresearch

These principles generalize Karpathy's autoresearch to ANY domain.

## Principle 1: Loop Until Done

**Unbounded mode:** Loop forever until manually interrupted.
**Bounded mode:** Loop exactly N times, then summarize and stop.

- NEVER ask "should I continue?" — in unbounded mode, YES. ALWAYS.
- In bounded mode, continue until N is reached or goal is achieved.
- Progress is measured by the metric, not by human approval.

## Principle 2: Read Before Write

**Always understand full context before modifying.**

- Read ALL in-scope files at the start of each iteration
- Read git history to see what was tried before
- Read results log to see patterns
- Never assume — always verify current state

## Principle 3: One Change Per Iteration

**Atomic changes. If it breaks, you know exactly why.**

- The change should be explainable in ONE sentence
- If you need "and" to describe it, it's two changes — split them
- Multi-file changes are OK if they serve a single purpose
- Example: "add caching" (touches 3 files) = ONE change
- Example: "add caching AND refactor auth" = TWO changes

## Principle 4: Mechanical Verification Only

**No subjective "looks good" or "seems better". Use metrics.**

- The metric MUST output a parseable number
- The metric MUST be reproducible (same input → same output)
- Examples: test coverage %, bundle size bytes, latency ms
- Anti-examples: "code is cleaner", "feels faster"

## Principle 5: Automatic Rollback

**Failed changes revert instantly. No debates. No hesitation.**

- Use `git revert HEAD --no-edit` (preserves history)
- Fallback: `git reset --hard HEAD~1` (if revert conflicts)
- Never ask "should I revert?" — if metric didn't improve, REVERT
- Failed experiments are valuable — they teach what doesn't work

## Principle 6: Simplicity Wins

**Equal results + less code = KEEP. Tiny improvement + ugly complexity = DISCARD.**

- Code quality is part of the fitness function
- Simpler solutions are more likely to work in edge cases
- Complex solutions are harder to debug and maintain
- When stuck, try REMOVING code instead of adding

## Principle 7: Git Is Memory

**Every experiment committed. Git history IS the learning mechanism.**

- Commit BEFORE verification (enables clean rollback)
- Use `experiment:` prefix in commit messages
- Read `git log --oneline -20` every iteration
- Read `git diff HEAD~1` to understand what worked
- Reverted experiments remain in history — learn from failures

---

## How These Principles Interact

```
Principle 3 (atomicity) + Principle 5 (rollback) = 
  → If change breaks, you know EXACTLY what caused it

Principle 2 (read first) + Principle 7 (git memory) =
  → You learn from past experiments, don't repeat failures

Principle 4 (mechanical) + Principle 6 (simplicity) =
  → Code quality is objectively measured, not subjective

Principle 1 (loop) + All others =
  → Autonomous improvement that compounds over iterations
```

## Anti-Patterns (What NOT to Do)

| Anti-Pattern | Why It's Wrong |
|--------------|----------------|
| Making multiple unrelated changes | Can't attribute improvement |
| Subjective verification | No reproducibility |
| Asking "should I continue?" | Breaks autonomy |
| Ignoring git history | Repeats past failures |
| Adding complexity for tiny gains | Maintenance burden |
| Not reverting failures | Pollutes codebase |

---

These principles are universal. Apply them to ANY domain: code, content, ML, DevOps, marketing, or anything with a measurable outcome.