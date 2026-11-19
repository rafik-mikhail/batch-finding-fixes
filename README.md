# Batch Finding Fixes

An agent skill that batches approved fix decisions by fix key, writes a plan for each batch, and after approval implements each batch as one branch and one draft pull request with a trace from every finding to its change. It runs the repository's existing checks and lists findings to rescan, but never marks a finding fixed.

## Use the skill

Use [`SKILL.md`](SKILL.md) in your agent’s skill directory under `batch-finding-fixes/`. Provide approved decision records from [Decide Finding Action](https://github.com/rafik-mikhail/decide-finding-action) and authorized write access to the affected repositories. The skill stops after writing each plan until you approve it. `fix-first` batches stay separate, and a failing test sends their findings back to validation.

## Use as agent instructions

Copy the [Batch Finding Fixes instruction gist](https://gist.github.com/rafik-mikhail/c7bd8fe26fe85e7f3b16f2f50e223b08) into your agent’s instruction file. The gist contains the same guidance with `applyTo: "**"` front matter for tools that support it. If your agent accepts plain Markdown instructions, copy the body without the front matter.

## Where it fits

This skill is step 6 of a six-step workflow that takes a received scan report to fix pull requests. Steps 1–3 work on the whole report, steps 4–5 work on one finding at a time, and step 6 works on one batch at a time.

| Step | Skill | Works on |
|---|---|---|
| 1 | [Normalize Scan Report](https://github.com/rafik-mikhail/normalize-scan-report) | whole report |
| 2 | [Deduplicate Scan Findings](https://github.com/rafik-mikhail/deduplicate-scan-findings) | whole report |
| 3 | [Prioritize Scan Findings](https://github.com/rafik-mikhail/prioritize-scan-findings) | whole report: sets the order and route; again for confirmed findings |
| 4 | [Validate Scan Finding](https://github.com/rafik-mikhail/validate-scan-finding) | one finding at a time (`validate` route and rule samples) |
| 5 | [Decide Finding Action](https://github.com/rafik-mikhail/decide-finding-action) | one finding at a time; one rule per rule sample |
| 6 | **Batch Finding Fixes** | one batch at a time |

Step 3 sends each finding down one route:

| Route | When | Path |
|---|---|---|
| `fix-first` | Dependency or base-image upgrade, patch or minor within the major version, tests in CI | Decide (`not-validated`, `fix`) → Batch. A failing test sends the findings to Validate. |
| `sample-rule` | Many findings from one rule, none `immediate` or `out-of-cycle` | Validate about 5 → Decide. If refuted for one shared reason, propose a rule change and hold the rest until rescan. |
| `validate` | Everything else | Validate → Decide → Batch |
| `defer-track` | SSVC `defer` | No review now; reassess on a KEV listing, a new exploit, an exposure change, or a new fix. |

Findings that share a fix are linked in step 2 only when the fix is known mechanically (dependency, image, IaC module, secret). Shared code causes are grouped in step 5 through the fix key, after validation. [Group Findings by Root Cause](https://github.com/rafik-mikhail/group-findings-by-root-cause) is retired.

Verification by rescan and closing with regression monitoring are not covered yet.

## How to test

Follow the [evaluation guide](TESTING.md).

## Notes

[NOTES.md](NOTES.md) covers gotchas, areas for improvement, and the sources behind the guidance.
