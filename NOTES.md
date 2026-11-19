# Notes: Batch finding fixes

## Gotchas

- **Batch by change, not by label.** Two findings with the same decision (`fix`) can need different changes. Only identical fix keys share a batch by default.
- **Batch size.** GitHub's agentic autofix takes up to 25 alerts per pull request. That is a ceiling, not a target. Smaller batches are easier to review and revert.
- **Automated fixes need review.** GitHub's own guidance says fix suggestions may be non-deterministic, change semantics, fix only part of the problem, add new vulnerabilities, or name dependencies that do not exist. Tests and human review are the control.
- **Grouped dependency updates.** Dependabot and Renovate can group security updates per ecosystem. If those tools are already set up, use them rather than editing lockfiles in the agent.
- **Secrets are not fixed by deletion.** The value stays in history and in any copy. Rotation is the fix.
- **Plan approval is a hard gate.** This follows the rule that agents get plan approval before code changes.

## Areas for improvement

- Add the verification step (rescan and confirm) and the close-and-monitor step as separate skills.
- Add a pull-request template file for the trace table.
- Add per-ecosystem upgrade notes (npm, pip, Maven, Go) for transitive overrides.

## Sources

- [GitHub: Agentic autofix general availability](https://github.blog/changelog/2026-10-15-agentic-autofix-for-code-scanning-alerts-general-availability/): up to 25 alerts in one pull request; validation by rerunning CodeQL.
- [GitHub: Participating in a security campaign](https://docs.github.com/en/code-security/tutorials/manage-security-alerts/best-practices-for-participating-in-a-security-campaign): group similar alerts; verify CI after fixes.
- [GitHub: Responsible use of security AI features](https://docs.github.com/en/code-security/responsible-use/security-and-quality-ai-features): limits of automated fixes.
- [GitHub: Dependabot grouped security updates](https://docs.github.com/en/code-security/how-tos/secure-your-supply-chain/secure-your-dependencies/configure-security-updates).
- [Renovate: vulnerabilityAlerts](https://docs.renovatebot.com/configuration-options/#vulnerabilityalerts).
