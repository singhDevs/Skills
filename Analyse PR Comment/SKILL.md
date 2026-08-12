---
name: analyse-pr-comment
description: Analyze a PR review comment and help the developer converge on a high-confidence resolution direction before implementation. Use when a review comment needs repository-aware investigation, design reasoning, or resolution planning.
---

# PR Comment Resolution

Help the developer reach a high-confidence resolution direction for the provided PR review comment.

Do not modify code unless explicitly asked. The goal is not to satisfy the reviewer blindly or produce the fastest patch; it is to understand the concern, uncover constraints early, compare viable directions when needed, and converge on a robust repository-aligned resolution.

## 1. Build resolution context

- Read the comment literally and separate explicit asks from inferred concerns.
- Locate the commented code and understand what it currently does in the surrounding flow.
- Identify the underlying concern and why it matters.
- Inspect relevant callers, consumers, contracts, tests, similar implementations, or module boundaries when needed.
- Use repository patterns as evidence, not unquestionable rules.
- Preserve ambiguity and clearly separate observed facts, inferred reviewer intent, and unknowns.

## 2. Discover constraints before designing

Before recommending a solution, actively look for information that could invalidate the obvious fix:

- hidden assumptions or contracts,
- edge cases and negative flows,
- surrounding behavior that must remain unchanged,
- repository or architectural constraints,
- state, lifecycle, concurrency, API, or ownership implications where relevant,
- signs that the comment exposes a deeper abstraction problem rather than a local defect.

The goal is to surface discoveries now that would otherwise appear only after implementation begins.

## 3. Converge on a resolution direction

Adapt the depth of analysis to the comment.

For straightforward comments with one clearly supported resolution, state that direction directly and explain why deeper alternative exploration is unnecessary.

For design-sensitive or uncertain comments:

- Generate only a small number of materially different, credible approaches.
- Do not create cosmetic variants just to provide options.
- Challenge each serious approach before recommending one.
- For each candidate, test its assumptions, edge cases, failure modes, complexity, repository fit, regression risk, and whether it solves the root cause or only the symptom.
- Make the approaches compete against each other: explain where one handles a constraint more naturally, where another weakens, and under what conditions each would still be appropriate.
- Recommend a direction only after this adversarial comparison.
- If repository evidence does not support a confident recommendation, say so and identify what information is missing.

Prefer the simplest design that robustly satisfies the actual concern. Do not overengineer for hypothetical future needs.

## 4. Define the resolution contract

Before implementation, state what would make the comment genuinely resolved:

- expected behavior,
- important invariants and edge/negative cases,
- behavior that must remain unchanged,
- likely affected code or abstractions,
- focused tests, checks, or proof needed.

This should give the developer a clear checkpoint for implementation and later review.

## Output

Keep the response structured and practical. Use enough detail to support an engineering decision without turning the brief into an implementation plan.

### Comment understanding
Explain the current behavior, the reviewer concern, and the likely intent.

### Relevant context
Summarize the repository evidence that materially shaped the analysis.

### Constraints and risks
List the assumptions, edge cases, contracts, or deeper design concerns that matter.

### Resolution analysis
For a straightforward case, give the recommended direction and why it is sufficient.

For a design-sensitive case, compare the serious approaches after challenging them, then recommend the strongest surviving direction.

### Resolution contract
State expected behavior, invariants, what must remain unchanged, and the validation/proof required.

### Confidence
State High, Medium, or Low confidence and the main reason. Call out any missing context that could materially change the conclusion.

## Guardrails

- Do not assume the reviewer is automatically correct.
- Do not jump from the comment directly to implementation.
- Do not invent repository conventions or unsupported requirements.
- Do not prefer a minimal patch when a different abstraction solves the concern more naturally.
- Do not generate multiple approaches unless real design alternatives exist.
- Do not hide uncertainty behind a confident recommendation.
- Do not modify files unless the developer explicitly asks.
