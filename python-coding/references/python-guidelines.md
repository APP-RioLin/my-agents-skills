# Python Guidelines Reference

Use this file when project conventions are absent or unclear, when a task explicitly asks for PEP 8, Google-style Python, or more Pythonic code, or when a refactor needs more concrete design guardrails.

## Table Of Contents

- Sources
- Decision Order
- First Principles And Occam's Razor
- Pythonic Defaults
- Preferred Verification Commands
- Layout And Imports
- Executable Files
- Naming
- Comments And Docstrings
- Types And Contracts
- DRY, SOLID, And Clean Code
- Python Safety Checks
- Verification

## Sources

- PEP 8: https://peps.python.org/pep-0008/
- Google Python Style Guide: https://google.github.io/styleguide/pyguide.html
- Ruff formatter: https://docs.astral.sh/ruff/formatter/
- Ruff linter: https://docs.astral.sh/ruff/linter/
- ty type checking: https://docs.astral.sh/ty/type-checking/

Existing project conventions still override these sources when the project has established conventions or configured tooling.

## Decision Order

Use this order when choosing between alternatives:

1. project configuration and existing project conventions
2. nearby code that is consistent and intentional
3. PEP 8
4. Google Python Style Guide

If a project uses tools such as `ruff`, `black`, `isort`, `ty`, `mypy`, or `pyright`, let those tools drive the final shape of the code. Prefer `ruff` for formatting and linting and `ty` for type checking when they are available in the project.

Within that order, prefer the most idiomatic Python that keeps behavior clear and the design as simple as the problem allows.

## First Principles And Occam's Razor

- Start from the actual inputs, outputs, invariants, constraints, and failure modes before choosing abstractions.
- Prefer the simplest design that fully explains the problem and preserves the required behavior.
- Add layers, helpers, and indirection only when they remove concrete risk, reduce stable duplication, or make a boundary clearer.
- Prefer Python's built-in data structures, control flow, and standard-library tools before importing heavier patterns.
- Avoid transplanting object-heavy or framework-heavy designs from other languages unless the Python codebase genuinely benefits from them.

## Pythonic Defaults

- Prefer functions until state, lifecycle, or invariants clearly justify a class.
- Prefer the standard library over a new dependency unless the project already standardizes the dependency or it materially improves correctness or maintainability.
- Prefer `list`, `dict`, `set`, `tuple`, and `dataclass` before creating custom wrappers or inheritance hierarchies.
- Prefer explicit loops and conditionals when denser expressions would make control flow harder to follow.
- Prefer comprehensions and generator expressions for simple collection transforms and filters. If the expression has multiple `for` clauses, complex conditions, or becomes hard to scan, use an explicit loop instead.
- Prefer default iterators and operators for built-in containers and files, such as `for key in mapping`, `if item in seq`, `for line in file`, and `for key, value in mapping.items()`. Avoid extra calls like `.keys()` or `.readlines()` when direct iteration expresses the same intent.
- Lambdas are fine for short one-line expressions. Prefer generator expressions over `map()` or `filter()` with a `lambda`, and prefer helpers from `operator` for common operations such as `operator.mul`.
- Use conditional expressions only for simple cases where the true branch, condition, and else branch each stay readable on one line. Use a full `if` statement when the expression becomes long or visually hard to scan.
- Prefer local, concrete abstractions over reusable frameworks unless there is a demonstrated need for a broader boundary.
- Avoid nested functions or classes unless they need closure state or make a small local behavior materially clearer. Do not nest helpers only to hide them from module scope; prefer a module-level helper with a leading underscore instead.
- Prefer public attributes for trivial data access. Use `@property` only for cheap, unsurprising logic or a simple derived value; use getter or setter methods when access has meaningful behavior, invalidation, or cost.

## Preferred Verification Commands

Inside the project's Python environment, prefer:

1. `ruff check --diff`
2. `ruff format --diff`
3. review the proposed changes
4. `ruff check --fix`
5. `ruff format`
6. run the project's configured type checker when one exists
7. if the project uses `ty`, prefer a project-native invocation such as `uv run ty check` or an activated-environment `ty check`
8. if `ty` is the intended checker but is not installed locally, `uvx ty check` is an acceptable fallback
9. if the project uses `ty` and safe type-checker autofixes are appropriate for an authoring task, run `ty check --fix`
10. review those autofixes and rerun the `ty` check
11. the relevant test command for the project

If the project uses different required commands, follow the project.

## Layout And Imports

- Use 4 spaces per indentation level.
- Prefer spaces over tabs.
- If the project does not define a line length, keep code near 88 columns and wrap comments or docstrings more tightly.
- Prefer implicit line continuation inside parentheses, brackets, and braces.
- Keep imports at the top of the file unless a lazy import is clearly justified.
- Put any `from __future__` imports first, after the module docstring or leading comments and before other imports.
- Put separate module imports on separate lines rather than combining them on one line.
- Group imports as standard library, third-party, then local imports after any `from __future__` imports.
- Leave a blank line between import groups.
- Within each import group, sort imports lexicographically by the module's full package path, ignoring case.
- Prefer absolute imports with full package paths for new code when project conventions are absent.
- Use `import x` for packages and modules.
- Use `from x import y` when `y` is itself a module exposed by package `x`.
- Avoid relative imports in the Google-style fallback path.
- Avoid importing individual classes, functions, or constants by default. Narrow exceptions include typing-oriented imports from modules such as `typing`, `collections.abc`, and `typing_extensions`, plus explicit project conventions that already standardize another pattern.
- For symbols used only to support static analysis and type checking, import the symbols directly from `typing`, `collections.abc`, or `typing_extensions`. It is fine to import multiple such symbols on one line.
- Use `if TYPE_CHECKING:` blocks only in exceptional cases where typing-only imports must be avoided at runtime. Place the block immediately after normal imports, keep it limited to typing-only names, and avoid empty lines inside the block.
- Avoid wildcard imports.
- Prefer one statement per line when practical. Avoid compound statements that hide control flow.

## Executable Files

- Most `.py` files do not need a shebang.
- If a file is meant to be executed directly, put its program flow behind `main()` and start the main file with either `#!/usr/bin/env python3` or `#!/usr/bin/python3`, following project and deployment conventions.
- Python filenames should use a `.py` extension and must not contain dashes.
- If an executable must be exposed without the `.py` suffix, prefer a symlink or a thin wrapper rather than dropping the extension from the Python file.

## Naming

- Use `lower_with_under` for modules, packages, functions, methods, variables, and parameters.
- Use `CapWords` for classes and exceptions.
- Use `CAPS_WITH_UNDER` for constants.
- Prefer descriptive names over abbreviations unless the abbreviation is standard in the domain.
- Avoid names that only restate the type, such as `user_list` when the domain meaning is already clear.
- Avoid double-underscore privacy unless name mangling is truly needed; a single underscore is usually enough.

## Comments And Docstrings

- Write comments to explain intent, invariants, tradeoffs, or surprising behavior.
- Do not use comments to narrate obvious code.
- Keep comments and docstrings in sync with the code; stale comments are worse than none.
- Use `"""triple double quotes"""` for docstrings.
- Add docstrings to public modules.
- Add docstrings to public classes, functions, and methods. At minimum, public or non-obvious APIs should be documented clearly enough that a reader can understand how to use them without reading the full implementation.
- Public function and method docstrings should start with a summary line and use sections such as `Args:`, `Returns:`, `Yields:`, and `Raises:` when those sections materially clarify the contract.
- Class docstrings should describe what the instance represents and document public attributes in an `Attributes:` section when that information matters to callers.
- Exception class docstrings should describe what the exception represents, not narrate where it is raised.
- Methods explicitly decorated with `@override` may inherit base-class documentation unless the override materially refines the contract or adds side effects. In those cases, document the differences or use a minimal `"""See base class."""` docstring when appropriate.
- Non-public methods do not always need full docstrings, but add a short explanatory comment or docstring when their behavior is non-obvious.
- Keep TODO comments actionable and specific.
- Prefer the Google-style TODO form: `TODO: issue-or-context-link - short action`. Avoid owner-only TODO context when a tracked issue or concrete event can carry the follow-up.
- For logging APIs that accept a format string plus arguments, pass a literal pattern string and the data arguments separately rather than pre-rendering the message with f-strings or concatenation.
- Write user-facing and exception messages so they match the actual failure condition, keep interpolated values obvious and grep-friendly, and avoid claiming a more specific cause than the code has established.

## Types And Contracts

- Add type hints to public APIs and to internal helpers where the contract is not obvious.
- At minimum, annotate public APIs. Add annotations earlier for code that is stable, hard to understand, or prone to type-related bugs.
- Keep return types consistent.
- Use `from __future__ import annotations` when it materially simplifies forward references and the project/runtime supports it. Otherwise, use string forward references where needed.
- If a value may be `None`, annotate it explicitly as `X | None` or `Optional[X]`. Do not rely on implicit optional behavior from defaulting a parameter to `None`.
- Use `TypeAlias` for complex type aliases when the project/runtime supports it. Name public aliases with `CapWords`; if the alias is module-internal, prefix it with a single underscore.
- Prefer built-in generic types such as `list[int]`, `dict[str, int]`, and `tuple[str, ...]` in modern code when the project/runtime supports them.
- Use tuples for fixed-length or heterogeneous typed returns and lists for variable-length homogeneous collections.
- Prefer abstract container types such as `collections.abc.Sequence` and `Mapping` in public contracts when mutability or a concrete container type is not required by the behavior.
- Prefer annotated assignments over legacy `# type:` comments in new code.
- Use `TYPE_CHECKING` imports and forward references sparingly and only to avoid runtime-only dependency problems that cannot be cleaned up yet.
- Name `TypeVar` and `ParamSpec` values descriptively by default. Short names such as `_T` and `_P` are fine only for non-public unconstrained type parameters; constrained or externally visible type parameters should use descriptive names.
- Use `str` for text data and `bytes` for binary data. Do not introduce `typing.Text` in new code.
- Prefer explicit value objects, `TypedDict`, `Protocol`, or `dataclass` only when they clarify behavior or reduce coupling.
- Use `Protocol` or ABC-style abstractions only when multiple real implementations or testing seams justify them.

## DRY, SOLID, And Clean Code

- Deduplicate business rules, validation, normalization, and error handling that must stay aligned.
- Do not extract helpers for every repeated line; wait for stable repetition with clear meaning.
- Keep functions focused on one coherent job.
- Keep classes small enough that their purpose is obvious from the name.
- Prefer composition when behavior can vary independently.
- Inject boundary dependencies such as external clients, time, filesystem access, randomness, and environment lookups.
- Separate pure transformations from side effects when that improves testability and readability.
- Prefer explicit control flow over clever shortcuts.
- Prefer idiomatic Python over cross-language ceremony.

## Python Safety Checks

- Never use mutable objects as default argument values.
- Use context managers for resources with lifecycle requirements.
- Do not use `assert` for validating preconditions, external input, or other required application logic. Reserve it for internal invariants and test expectations.
- Catch the narrowest exception that lets the code recover correctly.
- Never use bare `except:`. Avoid catch-all `except Exception` handlers unless re-raising or isolating a failure boundary that records and suppresses the error intentionally.
- Keep `try` blocks narrow so the operation that may fail is obvious.
- Validate external input at system boundaries.
- Avoid mutable global state.
- If mutable global state is genuinely necessary, keep it internal with a leading underscore, expose it through narrow functions or class methods, and document why the design is necessary.
- Avoid hidden import-time work and hidden global state.
- Put executable program flow behind `main()` and `if __name__ == '__main__':` so the module remains importable.
- Use implicit truthiness when it matches the real intent, but use `is None` or `is not None` when `None` must be distinguished from other false values.
- Prefer `if seq:` and `if not seq:` over `if len(seq):` and `if not len(seq):` for strings, lists, tuples, and similar sequences.
- Use `if not flag:` rather than comparing booleans to `False`. If `False` must be distinguished from `None`, chain the check explicitly, such as `if not flag and flag is not None:`.
- When a value is known to be an integer and the code is specifically checking for zero, compare to `0` directly instead of relying on implicit false.
- Do not use patterns like `x = x or []` when `None` must be handled differently from other false values. Use an explicit `if x is None:` initialization instead.
- Log enough context to debug failures, but do not swallow tracebacks unless the contract requires it.

## Verification

- Update or add tests when behavior changes.
- Add regression coverage for bug fixes.
- Run the project's formatter, linter, type checker, and test suite when available.
- If a rule cannot be followed because of project constraints, keep the code internally consistent and document the reason when it matters.
