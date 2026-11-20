---
name: pr-review
description: Review code changes for concrete, actionable issues with emphasis on solution quality, repository fit, risk, and comment-resolution correctness.
---

# PR Review

Review the current code changes rigorously using the diff, repository context, and any additional context provided. Focus on issues that could justify another engineering iteration before the change is merged or pushed for review.

## Available context

Use any additional context provided with the review, such as the PR description, task/specification, acceptance criteria, or original review comments.

- Use task/PR context to judge whether the change satisfies the intended scope and behavior.
- Do not infer requirements that are not supported by the available context.
- If additional context is unavailable, review only what can be established confidently from the diff and repository.
- When original review comments are provided, also apply the comment-resolution review criteria below.

## Review the solution

- Establish what problem or behavior the change is intended to address before judging the implementation.
- Evaluate whether the chosen approach is appropriate. Suggest a different approach only when it is materially better and supported by repository or design evidence.
- Check that the solution is proportional to the task: neither overengineered nor too narrow to satisfy its actual scope.
- Evaluate architecture and repository patterns critically. Existing patterns are evidence, not automatically the right design.
- For fixes, verify that the change addresses the root cause rather than masking the observed symptom.
- Consider meaningful correctness, robustness, regression, code/design, and user/UX risks introduced by the change.

## Findings

- Inspect surrounding code, callers, consumers, contracts, tests, or similar implementations when needed to validate a concern.
- Report only concrete, actionable findings supported by code or repository evidence.
- Avoid stylistic preferences, speculative concerns, cosmetic suggestions, and unrelated refactors.
- Do not manufacture findings when the change is sound.
- Avoid reporting multiple symptoms of the same underlying issue as separate findings.

Prioritize findings as:

- **P0 — Critical:** Severe correctness, data-loss, security, or similarly critical issue that should block merge.
- **P1 — High:** Meaningful correctness, robustness, regression, or design issue that should normally be addressed before merge.
- **P2 — Moderate:** Real edge-case, maintainability, or design issue worth addressing, but not necessarily merge-blocking.

For each finding, include the relevant code location, the concrete problem, why it matters, the evidence supporting it, and a reasonable resolution direction when useful.

If no actionable findings are found, state that clearly.

## Comment-resolution review

When original PR review comment(s) are provided:

- Treat the original concern as an explicit review constraint, but do not assume the reviewer is automatically correct.
- Verify that the change addresses the underlying concern rather than only the commented symptom.
- Check whether the resolution introduces new assumptions, complexity, regressions, edge cases, or unrelated behavior changes.
- Consider whether the resolution affects or makes other related review comments obsolete.
- Review the resulting diff as if trying to anticipate the next legitimate reviewer comment before the change is pushed.
