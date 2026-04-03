# Results Logging — TSV Tracking Format

Every iteration is logged in TSV (Tab-Separated Values) format for easy parsing and analysis.

## Log File Location

```
.autoresearch/results.tsv
```

## Format

```tsv
iteration	commit	metric	delta	status	description
0	a1b2c3d	85.20	0.00	baseline	initial state
1	b2c3d4e	87.10	+1.90	keep	add tests for auth edge cases
2	-	86.50	-0.60	discard	refactor test helpers (broke 2 tests)
3	c3d4e5f	88.30	+1.20	keep	add error handling tests
4	-	0.00	-	crash	double batch size (OOM)
5	-	-	-	no-op	attempted to modify read-only config
```

## Columns

| Column | Type | Description |
|--------|------|-------------|
| iteration | number | 0-indexed iteration number |
| commit | string | Git commit hash (or "-" if not kept) |
| metric | number | Metric value after verification |
| delta | number | Change from previous iteration (or "-" for non-keep) |
| status | enum | `baseline`, `keep`, `discard`, `crash`, `no-op`, `hook-blocked` |
| description | string | One-sentence description of what was tried |

## Status Values

| Status | Meaning |
|--------|---------|
| `baseline` | Initial state before any iterations |
| `keep` | Change improved metric, committed |
| `discard` | Change didn't improve, reverted |
| `crash` | Change caused error, reverted |
| `no-op` | No actual change produced |
| `hook-blocked` | Git hook rejected the commit |

## Reading the Log

```bash
# Count keeps vs discards
grep -c "^.*\tkeep" .autoresearch/results.tsv
grep -c "^.*\tdiscard" .autoresearch/results.tsv

# Find best iteration
sort -t$'\t' -k3 -n -r .autoresearch/results.tsv | head -1

# Extract all kept commits
awk -F'\t' '$5 == "keep" {print $2}' .autoresearch/results.tsv

# Calculate average improvement per keep
awk -F'\t' '$5 == "keep" {sum+=$4; count++} END {print sum/count}' .autoresearch/results.tsv
```

## Progress Summary Format

Printed every N iterations (default: 10):

```
=== Autoresearch Progress (Iteration 25) ===
Metric: 88.30% (started: 85.20%)
Keeps: 8 | Discards: 15 | Crashes: 2
Best: #3 — add error handling tests (+1.20%)
Trend: Improving (last 5: +0.8, +1.2, +0.3, -0.1, +0.5)
```

## Final Summary Format

```
=== Autoresearch Complete (50/50 iterations) ===
Baseline: 85.20% → Final: 92.10% (+6.90%)
Keeps: 18 | Discards: 28 | Crashes: 4
Best iteration: #47 — optimize query caching (+2.10%)
Top 5 improvements:
  #47: optimize query caching (+2.10%)
  #23: add error handling tests (+1.20%)
  #15: implement connection pooling (+0.95%)
  #8: cache config reads (+0.82%)
  #31: batch database writes (+0.71%)
```

## Integration with Git History

The results log complements git history:

- **Git log**: Shows WHAT was changed (code diff)
- **Results log**: Shows WHY it was changed (metric impact)

Together, they form the complete memory:

```bash
# Find the commit for best iteration
BEST_ITER=$(awk -F'\t' '$3 == max {print $2}' max=$(awk -F'\t' 'NR>0 && $5=="keep" {print $3}' .autoresearch/results.tsv | sort -n | tail -1) .autoresearch/results.tsv)
git show $BEST_ITER
```

## Log Rotation

Keep logs manageable:

```bash
# Archive old logs
mv .autoresearch/results.tsv .autoresearch/results-$(date +%Y%m%d).tsv

# Start fresh log
echo "iteration\tcommit\tmetric\tdelta\tstatus\tdescription" > .autoresearch/results.tsv
```