---
name: python-code-reviewer
description: Use this skill when reviewing existing Python code, a Python diff, or the Python portions of a PR or MR for implementation quality without asking for code changes. Use it for requests such as `review this Python code`, `review this Python diff`, `check if this is Pythonic`, `check naming`, `check comments and docstrings`, `check PEP 8`, `check SOLID`, `check DRY`, or `check clean code`. It is for review only, not for writing, refactoring, debugging, or implementing Python code.
---

# Python Code Reviewer

Use this skill when the task is to review existing Python code or a Python changeset for readability, idiomatic Python, naming, structure, typing clarity, and maintainability.

This skill is for review only. It reviews the quality of existing Python implementation details; it does not tell Codex to write, refactor, debug, or otherwise change Python code. For authoring or implementation work, use `python-coding`.

Use this skill when requests mention:

- `review this Python code`
- `review this Python diff`
- `review this module`
- `check if this is Pythonic`
- `review for idiomatic Python`
- `check naming`
- `check comments and docstrings`
- `check PEP 8`
- `check SOLID`
- `check DRY`
- `check clean code`

## Core Standard

A good Python code review:

- focuses on readability, maintainability, and idiomatic Python
- prioritizes material issues over cosmetic churn
- distinguishes clear issues from optional polish
- explains why a pattern is harder to read, reason about, or maintain
- prefers first-principles reasoning and the simplest design that fits the code
- gives actionable feedback that reduces complexity or improves clarity

The goal is not to enforce personal taste. The goal is to help Python code read naturally and stay cheap to change.

## Quick Workflow

1. Understand the code's intent and local project conventions.
2. Check naming, structure, control flow, abstractions, and typing.
3. Check whether the code uses idiomatic Python and standard-library patterns where they help.
4. Check comments, docstrings, and user-facing text for clarity, grammar, and spelling when they affect comprehension.
5. Return material issues first and keep minor polish as non-blocking suggestions.

## Tooling Checks

If you run project tooling during review, prefer read-only project-native commands so you can inspect issues without mutating code.

If the project uses `ty`, `ty check` is an appropriate read-only option.

Keep autofix commands such as `ty check --fix` for authoring work in `python-coding` unless the user explicitly changes the task from review to implementation.

## When Not To Use

- If the task is to write, refactor, debug, modernize, extend, or otherwise change Python code, use `python-coding`.
- If the task is mainly business correctness, regression risk, missing tests, rollout safety, or cross-team impact, use a broader review workflow.
- If the task is to prepare PR metadata or history, use a PR authoring workflow instead of this reviewer.
- If the task is mainly commit-message editing or prose editing outside code, use a narrower editing workflow.
- If the code is not primarily Python, use a more language-appropriate review workflow.

## Review Priorities

Default review order:

1. readability and maintainability
2. Pythonic and idiomatic code
3. naming and communication quality
4. simplicity of design and abstraction boundaries
5. typing and contract clarity
6. style

Style is last unless it materially affects readability or maintainability.

## Python Review Lens

Focus on:

- clarity of function, class, and module responsibilities
- idiomatic use of built-ins, standard library, and control flow
- names that are concise, appropriate, and meaningful
- comments and docstrings that clarify intent and stay in sync with behavior
- simple abstractions with obvious data flow and side effects
- typing and contracts that make behavior easier to understand

Do not treat Python review as a formatting exercise. Raise style or idiom comments when they improve clarity, change safety, or maintainability.

## Python Design And Readability

### PEP 8 And Local Style

Check whether the code is materially harder to read because it violates expected Python conventions.

If repo-local conventions are unclear, use the Google Python Style Guide as a secondary reference alongside PEP 8: https://google.github.io/styleguide/pyguide.html

Look for:

- inconsistent naming across the same module
- imports that obscure dependencies or create cycles
- imports that are not grouped clearly or place multiple unrelated imports on one line
- compound statements that cram multiple actions onto one line
- overlong or overly dense functions
- unreadable branching or nesting
- comments that explain trivia instead of intent
- line wrapping or indentation that makes control flow harder to scan

Prefer explicit code over hidden magic when style choices affect readability. Flag metaprogramming, decorator stacks, or dynamic import or attribute tricks when they make ordinary control flow materially harder to follow without a strong payoff.

Do not raise low-value formatting comments if the repo tooling will handle them automatically.

### Naming, Comments, And Language Quality

Check whether variable, function, class, parameter, and module names are concise, appropriate, and meaningful for their scope.

Look for:

- names that are vague, generic, too abbreviated, or misleading
- names that are longer than needed without adding meaning
- names that no longer match the behavior after a refactor
- inconsistent terms for the same concept
- naming that ignores high-signal Python conventions such as `snake_case` for functions and variables, `CapWords` for classes, and `Error` suffixes for exception classes
- parameter naming that ignores established conventions such as `self` and `cls` when those conventions matter
- ambiguous one-letter names such as `l`, `O`, or `I`, or other identifiers that are visually confusing
- non-public helpers that should be marked with a leading underscore for clarity
- placeholders like `data`, `obj`, `thing`, `temp`, or `val` where a domain name would be clearer
- class names that describe implementation mechanics instead of the role they play
- function names that hide side effects or misrepresent the return value

Also check comments and docstrings for coverage and accuracy:

- public, nontrivial, or non-obvious functions and methods that should have docstrings but do not
- classes missing docstrings that describe what the class or instance represents
- comments or docstrings that are stale and no longer match the code
- inline comments that narrate obvious code instead of explaining intent, invariants, or tradeoffs

Also check grammar and spelling where they materially affect clarity:

- identifiers with spelling errors that obscure intent
- comments and docstrings with grammar or spelling mistakes that create ambiguity
- user-facing strings, error messages, and log messages with wording mistakes that reduce clarity or professionalism

Do not flood the review with dictionary-level nitpicks. Comment only when wording makes the code harder to understand or noticeably less polished.

### Typing And Contract Clarity

Check whether annotations and function contracts make the code easier to understand instead of noisier or less accurate.

Look for:

- missing type hints where the contract is not obvious
- inconsistent return types that make call sites harder to reason about
- overly broad annotations such as `Any` where a narrower contract would clarify intent
- misleading annotations that do not match runtime behavior
- signatures that hide important optionality, mutability, or side effects

### Pythonic Code

Check whether the code uses idiomatic Python where doing so makes the behavior clearer, safer, or easier to maintain.

Prefer repo conventions first. If those are unclear, prefer standard-library and built-in Python patterns over cross-language ceremony.

Look for:

- object-heavy or Java-style structure where a function, `dataclass`, or simple module would be clearer
- manual indexing, counters, or temporary state where `enumerate`, tuple unpacking, `zip`, or direct iteration would simplify the code
- verbose dictionary or collection handling where built-ins or standard methods make intent clearer
- manual resource management where a context manager would make lifecycle handling safer
- boilerplate helpers or wrappers that duplicate obvious standard-library behavior
- comprehensions or generator expressions that would improve a simple transformation, or dense one-liners that should become an explicit loop
- custom container or inheritance layers where `list`, `dict`, `set`, `tuple`, or `dataclass` would be enough
- code that is clever rather than explicit, especially when it hides control flow or side effects

Do not ask for a code path to become more Pythonic unless the change would improve clarity, reduce complexity, or better match established project style.

### First Principles And Occam's Razor

Review design choices from the concrete behavior, constraints, and maintenance costs first.

Look for:

- abstractions added without a concrete duplicated need
- layers introduced for hypothetical flexibility rather than current requirements
- framework-shaped code that obscures a simple control flow
- helpers or indirection that explain less than direct code
- complexity arguments that are not grounded in actual data flow, invariants, or maintenance needs

Prefer comments that ask whether the simplest design that explains the current problem would be easier to read and maintain.

Do not push for simplification when the added structure is clearly justified by:

- explicit invariants or lifecycle constraints
- multiple real consumers or implementations
- meaningful boundaries around time, environment, filesystem, network, or external clients
- a clear contract that the simpler version would blur

### SOLID In Practice

Use SOLID as a design smell detector, not as a reason to demand abstract patterns everywhere.

Look for:

- classes doing too many unrelated things
- functions that mix orchestration, validation, I/O, and transformation without clear boundaries
- subclassing that weakens invariants or changes behavior unexpectedly
- interfaces or helpers that force callers to depend on unrelated behavior
- concrete dependencies that make substitution or unit-level reasoning harder than needed

Good review notes connect the design concern to a concrete readability or maintenance problem.

### DRY Without Over-Abstraction

Duplication is worth flagging when it creates drift, confusion, or unnecessary maintenance burden.

Look for:

- repeated validation, normalization, parsing, or error handling that should stay aligned
- repeated utility logic that already has the same meaning in multiple places
- parallel branches that differ only by incidental values

Do not push for abstraction when:

- the repetition is still small
- the code paths are likely to diverge soon
- the abstraction would hide intent more than it helps

### Clean Code

Look for:

- functions with more than one clear responsibility
- names that do not reflect the actual behavior
- hidden side effects
- misleading helper boundaries
- dead code or stale compatibility paths
- complex logic that would be clearer if split, renamed, or lightly documented

Prefer comments that explain why the current shape hurts readability or maintainability, not just that it feels unclean.

### Python-Specific Safety And Clarity Checks

Look for:

- mutable default arguments
- inconsistent return types
- ambiguous sentinel values
- unsafe truthiness checks where `None` and empty values mean different things
- direct dictionary or object access that assumes presence without a clear contract
- mutation of inputs that callers may reasonably expect to remain unchanged
- generator, iterator, or lazy-evaluation behavior that makes control flow harder to follow
- dataclass, `default_factory`, or descriptor misuse
- hidden import-time side effects
- broad exception handling that obscures intent or failure modes
- resource lifecycle code that should use a context manager

## Material Issues Versus Suggestions

Differentiate clearly:

- issue: materially hurts readability, maintainability, or idiomatic Python
- question: intent is unclear
- suggestion: optional polish

Keep optional suggestions separate from material issues.

## Review Output Structure

Preferred output order:

1. material issues
2. optional suggestions or open questions
3. short summary only when it adds value

If there are no material issues, say so explicitly and note whether only minor polish suggestions remain.

## Avoid Low-Signal Review Behavior

Avoid:

- nitpicking spelling when intent is already obvious
- asking for a refactor without explaining the concrete readability or maintenance value
- speculative style comments with no local benefit
- commenting on every line to appear thorough
- demanding abstraction or indirection without a clear payoff
- treating every PEP 8 or clean-code preference as blocking
- asking for clever code just because it looks more advanced

If a style or idiom concern is real, explain how it improves clarity, naming, simplicity, or maintainability.

## Validation For The Reviewer

Before finalizing a review, ask:

- did I follow local conventions before defaulting to generic Python advice
- are my comments about Python implementation quality rather than broader product or rollout concerns
- did I stay in review mode rather than drifting into authoring or implementation guidance
- is each material issue specific and actionable
- did I separate material issues from optional polish
- did I avoid low-signal nitpicks

Trigger prompts:

- should trigger: `Review this Python module for readability and maintainability.`
- should trigger: `Check if this code is Pythonic and call out naming or typing problems.`
- should trigger: `Review this Python diff for PEP 8, DRY, and clean-code issues.`
- should not trigger: `Refactor this Python module to simplify the control flow and add types.`
- should not trigger: `Fix the bug in this Python script and run the relevant checks.`

## Completion Checklist

Before considering the review complete, confirm:

- the code's intent and local conventions were understood
- material issues are about Python implementation quality, not broader rollout or product-risk review
- the review stayed focused on assessing existing code rather than proposing implementation work as the primary task
- naming conventions, docstring and comment quality, grammar and spelling, Pythonic code, simplicity, typing, and maintainability were checked where relevant
- comments are actionable and concise
- optional polish does not overwhelm material issues
- PEP 8, Pythonic code, naming, grammar and spelling, SOLID, DRY, first-principles, Occam's razor, typing, and clean-code concerns are framed as readability or maintainability findings rather than taste-only commentary
- the final review makes the Python code easier to read and maintain
