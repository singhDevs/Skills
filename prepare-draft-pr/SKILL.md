---
name: prepare-draft-pr
description: Create or resume a GitHub issue-to-draft-PR workflow for all or selected uncommitted changes, with logical commits and final repository-required tests and lint.
---

# Prepare Draft PR

Complete the workflow in order. A request to run this workflow authorizes creating the issue, creating a branch when needed, committing the selected changes, pushing that branch, and opening a draft PR. Creating or editing this skill does not run the workflow. Honor narrower instructions and existing execution permissions.

## 1. Establish scope and base

- Read applicable `AGENTS.md` instructions. Inspect staged, unstaged, and untracked changes, the current branch, remotes, upstream, and any existing PR.
- Treat the developer's explicit commit instructions as authoritative over this skill's defaults and inferred scope: follow the specified files, hunks, exclusions, grouping, order, and messages exactly. Include all uncommitted changes only when no narrower scope is specified. Preserve excluded working-tree and staged changes. If an instruction cannot be followed safely or is ambiguous, ask a focused question before committing rather than substituting another interpretation.
- Inspect the selected diff and enough surrounding code to identify its purpose, affected modules, consumers, and test requirements. Account for untracked files explicitly; exclude ignored files and credentials.
- Establish the GitHub repository.
- Confirm one base branch with the developer: it is both the work branch's starting point and the PR's merge destination. If the developer already named the base branch, use exactly that branch throughout. Otherwise ask: "Which base branch should I create the work branch from and merge the changes into?" Block the workflow until the developer answers. Do not infer the base from the current branch, detached HEAD, an existing PR, or the remote default; elapsed time is not an answer. If the confirmed base cannot be resolved unambiguously, report the blocker and wait for clarification rather than substituting another branch.
- Read `write-pr-description` from the active agent's installed skills or the bundled sibling skills. Resolve it before creating external artifacts; if unavailable, report the missing dependency.
- For a confirmed resume, reconstruct the selected scope and completed steps from the conversation, issue, branch commits, and existing PR. Continue from the first incomplete step even when the working tree is clean; rerun final validation before publishing. Stop for no selected changes only when there is no confirmed workflow to resume. Ask one focused question if the scope, repository, or branch intent remains materially ambiguous.
- On every resume, repeat step 4's base/conflict check before final validation in step 5, even after a developer-committed merge or rebase with a clean working tree.
- For new in-scope uncommitted changes, including blocker fixes, make the commit work in step 4 incomplete again and must be committed before validation. Unrelated edits remain excluded and do not reopen commit work.

## 2. Create the issue

- First check whether the user supplied an issue or an earlier attempt already created one for this work. Reuse that confirmed issue. After an uncertain creation result, inspect GitHub before retrying. Create a new issue only when none exists for this workflow.
- Use `gh` against the established repository. Write a concise title describing the resulting change and a lucid body explaining the need and intended outcome, supported by the diff and conversation. Do not add anything to the body directly from the conversation.
- Infer a bucket from repository issue conventions and the actual change. Prefix a clear bucket as `Core: Add data layer`, `UI: Fix dialog lifecycle`, or `Module-A: Reject invalid input`. Omit the prefix when no bucket clearly fits; do not force unrelated changes into one.
- Keep the body understandable without reading the diff. Include only useful context and concrete completion criteria; omit file inventories and unsupported claims.
- For a new issue, use a temporary Markdown file with `gh issue create --repo <repository> --title <title> --body-file <file>`. Record the created or reused issue's number and URL.

## 3. Select the work branch

- Create the work branch from the base confirmed in step 1. Reuse an existing work branch only when the developer asks for it, preserving its name; an explicit request to create a new branch takes precedence. A checked-out default or integration branch does not count as a work branch.
- Name a new branch `<issue-number>-<issue-title-slug>`. Lowercase the full issue title, including any bucket, replace runs of spaces or unsupported characters with `-`, and trim leading/trailing dashes. Keep only ASCII letters, digits, and hyphens. Example: issue `123`, title `Core: Adds data layer` becomes `123-core-adds-data-layer`. Use this format without an additional prefix and validate with `git check-ref-format --branch`.
- Carry the selected uncommitted changes to the work branch without losing unrelated working-tree or index contents. If switching bases cannot preserve them safely, stop and explain the concrete conflict. Do not force checkout, discard changes, or overwrite a colliding branch.

## 4. Commit logical groups

- Before committing or proceeding to step 5, fetch the latest confirmed base branch from the verified repository remote. Verify that the fetched base is an ancestor of the current work branch's HEAD (`git merge-base --is-ancestor <fetched-base> HEAD`). Check for unmerged index entries or an unfinished merge, rebase, or cherry-pick. If freshness cannot be established, report the blocker and stop.
- If the work branch lacks base commits or has conflicts or unfinished operations, stop and ask the developer to update the current work branch with the latest confirmed base and resolve or complete the operation before resuming. Perform no pushes or PR creation/update; do not merge, rebase, or resolve conflicts automatically. Proceed only when the latest fetched base is included and no conflicts or unfinished operations remain.
- When grouping was not specified, plan cohesive commits by purpose, such as data contract and implementation, integration, and UI behavior. Keep closely coupled tests with the behavior they cover. Each commit should be reviewable and coherent; use one commit only when the changes form a single indivisible unit.
- Stage explicit paths or hunks for each group. Inspect the staged diff before committing so pre-existing staged changes cannot leak into the wrong commit or selected scope.
- When messages were not specified, write concise messages describing each group's resulting change, following repository conventions. Create new commits; preserve existing history.
- After committing, inspect status and the merge-base diff against the confirmed base branch. Confirm all selected changes are committed and unrelated local changes remain intact. Account for any earlier commits already on a reused branch because they will also appear in the PR. If unrelated committed work would widen the requested scope, ask how to handle it before publishing; preserve existing history.

## 5. Run final tests and lint

- Read the repository's testing and lint guidance for current commands, affected-module and consumer coverage, test routing, required configurations, and execution settings. Keep those details in the repository as their source of truth.
- Run final required tests and lint against committed HEAD, using the checks specified by the repository. When unrelated local edits remain, use an isolated checkout of HEAD so those edits cannot contaminate validation.
- If any test or lint check fails or cannot run, abort the workflow immediately: perform no pushes to any remote and no PR creation or update. Report the failed command, failing tests or lint findings, relevant error output, and any available log/report path. Preserve completed artifacts and local changes. Report the failure directly; do not automatically fix code, broaden commit scope, retry code changes, or ask to bypass the failure.
- Proceed only when every required check passed for the exact HEAD being published. Record that commit, commands, and results. Any later code change invalidates affected results and requires validation again before publishing.

## 6. Describe and open the draft PR

- Before pushing or creating a PR, check for an existing PR for this head. Select an existing draft for reuse only if its base matches the confirmed base branch; otherwise report the mismatch and wait for the developer's resolution before pushing or updating it. If a ready PR already exists, report it and stop without changing its state or creating another PR.
- Invoke `write-pr-description` using the final committed merge-base diff against the confirmed base branch, conversation context, and the issue created or explicitly selected for this work. Follow that skill's exact format, including its no-UI rule. Link this issue with `Closes: #<number>` when the PR resolves it.
- Save the exact description in a temporary Markdown file. Keep validation outcomes in the final report rather than adding sections that violate the description skill's format.
- Confirm the outgoing commits and PR diff match the intended scope. Push only the work branch, setting its upstream as needed; never force-push as part of this workflow.
- Update a reused draft's description using the saved file. If no PR exists, create with `gh pr create --repo <repository> --base <base-branch> --head <work-branch> --draft --title <concise-title> --body-file <file>`, using the confirmed base branch.
- Verify the returned PR's URL, head, draft state, and closing issue reference, and confirm its base equals the developer-confirmed base branch. On an uncertain push or create result, inspect remote state before retrying.

Finish with the issue URL, draft PR URL, branch, commit groups, and concise test/lint results. If stopped, state the blocker and completed artifacts. Do not merge, mark ready, or add workflow labels unless separately requested.
