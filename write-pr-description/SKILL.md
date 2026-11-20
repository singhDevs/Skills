---
name: write-pr-description
description: "Draft or rewrite pull request descriptions from PR or branch diffs and conversation context, using concise change summaries, expected UI behavior for QA, and linked issues. Use when writing or improving a PR body, including while preparing a PR."
---

# Write PR Description

Generate a concise, evidence-based PR description in the format below. Drafting the description does not itself authorize creating a PR or updating a published PR body; perform those actions only when separately requested.

## Establish the changes

- Read applicable repository instructions and use the repository and PR specified by the user.
- For a supplied PR, inspect its current diff against its actual base and the changed files at its current head. Do not substitute unrelated local changes for the PR's contents.
- Otherwise, inspect committed changes on the current branch against the established base using the merge-base diff, such as `git diff <base>...HEAD`. Establish the base from an existing PR, the user's target, or repository guidance; do not assume a branch name.
- Include uncommitted changes only when requested. Inspect the relevant staged, unstaged, or untracked files when they are part of that requested scope.
- Use available read-only Git or PR tools; no particular hosting integration is required. Inspect surrounding code, callers, and relevant tests when needed to understand behavior and conditions.
- Use the conversation to understand intent, but describe the final implemented result. Resolve stale plans or an outdated existing description against the actual code. Do not recount abandoned approaches or intermediate fixes.
- Ask one focused question if the change scope, base, or a material behavior cannot be established. Request the missing diff or context if it is inaccessible. Do not invent behavior or turn missing evidence into a claim of no UI changes.

## Required output

Return the Markdown description directly, without an introduction, surrounding code fence, or additional sections. Preserve these headings, field labels, and section order, subject to the no-UI rule below:

```markdown
## What this PR does
- <Change and a brief explanation of its purpose or effect>
- <Another distinct change, if needed>

## Expected UI Behavior
- **Screen/Component:** <Affected surface>
- **Expected changes:** <Observable behavior QA should expect>
- **Conditions (if any):** <Relevant prerequisites, or None>

## Linked Issue
Closes: #<ISSUE_NUMBER>
```

### What this PR does

- Use short, concrete bullets that explain what changed and briefly why it matters or what it affects. Prefer one sentence per bullet; include only as many bullets as distinct meaningful changes require.
- Group related edits by their resulting behavior. Include technical details when they explain a meaningful change, especially for internal work.
- Avoid file inventories, symbol-by-symbol summaries, vague claims such as "improves UX," repeated points, and implementation history.

### Expected UI Behavior

- Write for QA in terms of what users will see or experience: visible content, interactions, navigation, loading, empty or error states, and accessibility behavior as applicable. Include user-visible behavioral changes even when the visual layout is unchanged.
- State the changed outcome concretely. A brief before/after distinction is useful when it clarifies the change. Do not replace expected behavior with implementation details or a generic instruction to "verify it works."
- Name the affected screen or component using its user-facing name when available. Repeat all three field bullets for each separate screen or component, with a blank line between groups; keep shared behavior together when it applies to the same surfaces.
- Under **Conditions (if any):**, include prerequisites or limits supported by the code, such as platform, permissions, feature flag, account state, or triggering action. Write `None.` when no special condition applies.
- Omit the entire **Expected UI Behavior** section when the changes have no user-visible effect. Do not leave empty fields or add an N/A section.
- Describe expected behavior without claiming it was tested or verified unless there is actual evidence. Do not invent runtime results.

### Linked Issue

- Use `Closes: #<number>` for an issue confirmed by the user or PR context as being resolved. Do not treat an arbitrary issue reference in code, a commit, or a conversation as a confirmed closing relationship.
- If multiple issues are confirmed as resolved, use one `Closes:` line per issue. Preserve a qualified issue reference when needed to identify another repository.
- If no linked issue is known, retain the exact line `Closes: #<ISSUE_NUMBER>`. Do not ask solely to replace this placeholder.

## Final check

Confirm that every described change and condition is supported by the final code, each summary bullet briefly explains its purpose or effect, QA can identify the affected surface and observable result, and the issue reference is confirmed or left as the exact placeholder. Remove unused bullets and template placeholders other than the allowed issue placeholder.
