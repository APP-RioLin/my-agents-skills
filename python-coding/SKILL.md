---
name: python-coding
description: "Use this skill when writing or changing Python code: implement a feature, fix a bug, refactor a module, add type hints, modernize code, simplify control flow, or make an existing implementation more Pythonic by editing it. It covers authoring defaults, project conventions, tests, and Ruff/ty-based validation. It is for implementation work, including prompts like `make this more Pythonic`, not review-only requests such as `check if this is Pythonic`, `check PEP 8`, `check SOLID`, or `check DRY`."
---

# Python Coding

Use this skill when the task is to implement, refactor, debug, modernize, or extend Python code while keeping readability, maintainability, and change safety high.

This skill is for authoring code, not reviewing it. It complements `python-code-reviewer`, which is for finding risks in existing changes.

If the user only wants evaluation of existing code quality, style, naming, typing, or Pythonic-ness without asking for a patch, use `python-code-reviewer`.

## Core Standard

Good Python code changes:

- preserve existing behavior unless the task says to change it
- follow existing project conventions when they are clear and intentional; otherwise use standard Python guidance
- prefer idiomatic Python over patterns borrowed from other languages
- improve clarity before chasing abstraction
- reason from first principles before introducing patterns or layers
- favor the simplest design that fully satisfies the requirements
- reduce duplication that creates drift or bug risk
- keep functions, classes, and modules focused
- make data flow and side effects obvious
- keep tests and validation close to the risky logic

The goal is not to satisfy a checklist mechanically. The goal is to produce Python code that is easy to read, safe to change, and cheap to test.

## Style And Decision Order

Use this order when making choices:

1. existing project conventions and configured tools such as `pyproject.toml`, `ruff.toml`, `mypy.ini`, `pytest.ini`, or existing formatter settings, when they exist
2. surrounding code when it is consistent and intentional
3. PEP 8
4. Google Python Style Guide

If project-specific guidance is absent or ambiguous, default to PEP 8 and the Google Python Style Guide unless the user asks for a different standard.

Within that order, prefer the most idiomatic Python that keeps behavior obvious and the design minimal.

When the project supports them, prefer:

- `ruff check --fix` for linting and safe autofixes
- `ruff format` for formatting
- a project-native `ty` invocation such as `uv run ty check` or an activated-environment `ty check` to inspect typing issues
- `uvx ty check` only as a fallback when `ty` is the intended checker but is not installed locally
- `ty check --fix` only in authoring flows when safe autofixes are appropriate and the result will be reviewed

Read [references/python-guidelines.md](references/python-guidelines.md) when:

- project conventions are absent or unclear
- style or structure choices are part of the task
- a refactor is large enough to need explicit guardrails
- you need a concise reminder of Pythonic defaults for functions versus classes, stdlib versus dependencies, or abstraction thresholds
- you need a concise reminder of DRY, SOLID, clean-code, typing, naming, imports, docstrings, logging, error messages, exception handling, mutable global state, executable files and entrypoints, layout, or tool preferences

## Quick Workflow

1. Inspect any existing code and project conventions before editing; if none exist, use the defaults in this skill.
2. Understand the behavior, inputs, outputs, and failure modes that must stay correct.
3. Choose the smallest design that makes the code clearer, starting from first principles instead of inherited patterns.
4. Implement with explicit contracts, readable control flow, and limited blast radius.
5. After reviewing the proposed edits, apply them with `ruff check --fix` and `ruff format` when appropriate.
6. Run the project's configured type checker when one exists. If the project uses `ty`, prefer a project-native invocation such as `uv run ty check` or an activated-environment `ty check`, use `uvx ty check` only as a fallback when `ty` is the intended checker but is not installed locally, and apply `ty check --fix` only when safe autofixes are appropriate for that `ty`-based authoring flow; then review the result and rerun the check.
7. Run the relevant tests.
8. Summarize behavior changes, validation, and any residual risk.

## Expected Output

A successful use of this skill should normally produce:

- Python code or a patch that matches project conventions or the fallback PEP 8 plus Google-style defaults in this skill
- explicit contracts through clear names, imports, control flow, docstrings, and type hints where the code benefits from them
- executable entrypoints, file naming, and mutable-state boundaries that remain safe to import and easy to test
- a concise summary of behavior changes, validation run, and any residual typing, style, or runtime risk

## Authoring Priorities

Default order:

1. correctness
2. regression safety
3. clarity of contracts and control flow
4. maintainability
5. testability
6. performance
7. style

Style matters, but it is not the main goal. Prefer the change that makes behavior easier to reason about.

## Detailed Guidance

For detailed Python authoring rules, read [references/python-guidelines.md](references/python-guidelines.md).

Use that reference for concrete guidance on:

- functions versus classes
- standard library versus new dependencies
- imports, layout, naming, comments, docstrings, TODOs, logging, error messages, and executable-file conventions
- exception handling, runtime assertions, and executable entrypoints
- typing, typing imports, forward references, type aliases, and contract clarity
- DRY, SOLID, clean-code, and Pythonic defaults such as local helpers, comprehensions, lambdas, conditional expressions, iterators, and properties
- safety checks such as mutable defaults, mutable global state, truthiness, and hidden side effects

## Testing And Verification

When touching Python code:

- add or update tests for behavior changes
- prefer regression tests for bug fixes
- cover risky branches, not just the happy path
- follow the preferred verification command order in [references/python-guidelines.md](references/python-guidelines.md)
- do not claim style or type-checker compliance if the project tooling was not run

## Validation Prompts

Use prompts like these when checking whether the skill still triggers and guides well:

- should trigger: `Implement this feature in our Python API client and add the needed tests.`
- should trigger: `Make this Python function more Pythonic by simplifying the control flow and improving the implementation.`
- should trigger: `Fix the bug in this Python script, clean up the implementation, and run the relevant checks.`
- should not trigger: `Check if this Python module is Pythonic and point out the issues.` This should use `python-code-reviewer`.
- should not trigger: `Review this Python code for PEP 8, DRY, and SOLID concerns.` This should use `python-code-reviewer`.

## When Not To Use

- If the task is primarily to review a Python diff or PR, use `python-code-reviewer`.
- If the task is to assess existing code quality without asking for changes, use `python-code-reviewer`.
- If the task is primarily architecture strategy without concrete code changes, use a design-review workflow instead.
- If the task is not mainly Python, use a more relevant language skill.

## Completion Checklist

Before considering the task complete, confirm:

- any existing code and project conventions were checked first, or the defaults in this skill were used when none existed
- behavior changes are intentional
- duplication was reduced only where it lowered real maintenance risk
- abstractions have a concrete payoff
- names, control flow, and boundaries are easier to understand than before
- imports, type annotations, docstrings, exception handling, global state, and entrypoints match the intended project or fallback style
- tests or validation cover the risky paths
- `ruff` and `ty` were used when the project supports them
- the skill would still trigger correctly on realistic `make this more Pythonic` and `refactor this Python` requests
