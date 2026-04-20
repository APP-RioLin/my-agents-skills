---
name: shell-coding
description: Use this skill when writing, refactoring, debugging, or hardening shell code, Bash scripts, wrapper scripts, or durable command pipelines. It covers how to choose when shell is appropriate, keep scripts safe and readable, follow surrounding script, command, and repo conventions first, apply the Google Shell Style Guide when the target is Bash or the surrounding shell context already follows Google-style conventions, and use shell builtins plus standard CLI tools effectively for orchestration and data wrangling.
---

# Shell Coding

Use this skill when the task is to implement, refactor, debug, modernize, or extend shell code while keeping safety, readability, and operational reliability high.

This skill is for authoring shell code: scripts, wrappers, and maintained pipelines. It is not for broad system administration, one-off command suggestions, or interactive shell customization.

Use this skill when requests mention:

- `write a bash script`
- `write a shell script`
- `refactor this bash`
- `debug this shell script`
- `make this script safer`
- `clean up this pipeline`
- `use awk or sed in this script`
- `wrap this command in a reusable script`
- `make this shell automation portable`

Only trigger this skill when the request materially involves authoring or changing shell code. If the user only needs an ad hoc command, shell setup advice, or terminal troubleshooting, this skill should usually not load.

## Core Standard

Good shell code changes:

- preserve intended behavior unless the task says to change it
- follow surrounding script, command, or repo conventions and runtime constraints first
- use shell mainly for orchestration, wrappers, and command composition
- prefer the simplest construction that satisfies the real requirements
- prefer shell builtins for simple expansion, matching, and arithmetic in Bash before reaching for external text-processing commands
- keep control flow obvious and state small
- make failure modes explicit
- avoid quoting and word-splitting hazards
- lean on standard command-line tools instead of rebuilding them badly in shell

The goal is not to show off shell cleverness. The goal is to produce shell code that is easy to read, safe to run, and cheap to maintain.

Apply these reasoning defaults throughout:

- Occam's razor: choose the simplest design that keeps correctness, safety, and maintainability intact.
- First-principles reasoning: start from the task's inputs, outputs, side effects, and shell semantics instead of copying familiar snippets by habit.
- If the solution becomes too complex to explain clearly in shell terms, stop and move to a more structured language instead of forcing shell to carry it.

## Language And Decision Order

Use this order when making choices:

1. surrounding script, command, or repo conventions, configured tooling, and deployment constraints
2. the target shell and portability boundary the surrounding shell context already expects
3. surrounding code when it is consistent and intentional
4. Google Shell Style Guide when the target is Bash or the surrounding shell context already follows Google-style conventions
5. Missing Semester and Learn X in Y Minutes references for syntax, tools, and pipeline patterns

If the shell target is ambiguous, inspect the surrounding script, command, or repo context first. Do not assume Bash-specific features are acceptable unless the shebang, tooling, or existing code makes that clear.

When the target is Bash, apply the Google guide as the default Bash style contract: `#!/bin/bash` for executables, `set` options in the script body, `[[ ... ]]` for conditionals, arrays and `"$@"` for argument lists, `foo()` function declarations, `local` in functions, lower_snake_case names, capitalized exported or readonly constants, grouped functions, and `main "$@"` for non-trivial scripts.

If the surrounding shell context intentionally targets a narrower portability boundary such as POSIX `sh`, keep the same safety goals but adapt the implementation instead of forcing Bash-only constructs.

When the script is growing past simple orchestration, pause and reassess whether it should stay shell. If the task needs complex state, richer data structures, heavy parsing, or long-lived maintainability, switch to a more structured language early.

Treat rising solution complexity itself as a signal. If correctness depends on intricate branching, fragile quoting, layered subshell behavior, or dense text munging that is hard to reason about locally, shell is usually no longer the right tool.

Read [references/shell-style-and-safety.md](references/shell-style-and-safety.md) when:

- shebang, comments, naming, quoting, arrays, traps, functions, or error handling are part of the task
- you need concrete shell-style rules
- you are hardening an existing script against common Bash footguns

Read [references/shell-tools-and-pipelines.md](references/shell-tools-and-pipelines.md) when:

- the task involves pipelines, text processing, `find`, `grep`, `sed`, `awk`, `sort`, `uniq`, `paste`, or remote shell commands
- you are deciding whether to solve a problem with coreutils or with shell control flow
- you need quick reminders for command substitution, subshells, and pipeline composition

## Workflow

1. Inspect the surrounding script, command, or repo context for shebangs, shell target, naming and comment patterns, linting, test commands, and portability expectations.
2. Understand the inputs, outputs, side effects, and failure conditions that must stay correct.
3. Decide whether shell is still the right tool by reasoning from requirements first, not from habit.
4. Choose the simplest approach that meets those requirements without hiding failure or portability risks.
5. Keep logic in small functions with a narrow main flow. For non-trivial Bash scripts, prefer a bottom-most `main` function invoked as `main "$@"`.
6. Use Bash builtins for simple expansion, matching, and arithmetic, and use standard tools for searching, filtering, and reshaping text when that is clearer than shell control flow.
7. Run `bash -n` for Bash syntax checks and `shellcheck` when the project supports them.
8. Run the relevant smoke tests or task-specific commands.
9. Summarize behavior changes, validation, and any residual risk.

## Expected Output

A successful use of this skill should normally produce:

- shell code or a patch that matches the surrounding shell context's intended target and style boundary
- explicit handling of quoting, error paths, cleanup, and argument passing where relevant
- a concise summary of behavior changes, validation run, and residual portability or operational risk

If shell is no longer the right tool, the result should say so plainly and recommend the more appropriate language or structure with a short rationale.

## Authoring Priorities

Default order:

1. correctness
2. safety and failure behavior
3. portability within the intended shell/runtime
4. clarity of data flow and control flow
5. maintainability
6. style

Style matters, but in shell it exists mainly to reduce ambiguity and operational mistakes.

## Safety Defaults

Default to:

- an explicit shebang that matches the intended shell
- `set -euo pipefail` in Bash scripts when compatible with the task and surrounding shell conventions
- file naming, extension, and executable-vs-library choices that match the surrounding shell context
- a top-level file comment for maintained scripts plus function header comments when the function is non-obvious, reused, or part of a library
- double-quoting expansions unless unquoted splitting or globbing is intentionally required
- arrays for argument lists in Bash
- `"$@"` when forwarding argv in Bash unless joined string behavior is explicitly desired
- `read -r` when reading lines
- functions plus `local` variables inside Bash functions
- separate `local` declaration from command-substitution assignment when exit status matters
- error messages to `stderr`
- `trap` for cleanup when the script creates temporary files, directories, or child processes

Be careful with:

- `eval`
- unquoted `$*`, `$@`, command substitutions, and variable expansions
- quoting the RHS of `[[ string =~ regex ]]` in Bash when regex matching is intended
- `local var="$(cmd)"` when the command's exit status matters
- parsing `ls` output
- pipelines that hide failure or spawn subshells that lose state
- implicit globals and reused temporary variable names
- scripts that silently continue after partial failure

## Design Heuristics

Prefer:

- small wrapper scripts that mostly compose existing commands
- one clear responsibility per function
- a `main` function that owns argument parsing and top-level flow
- helper functions for repeated logging, validation, or cleanup behavior
- names that make shell role obvious: lower_snake_case for functions and ordinary variables, capitalized names for exported or readonly constants
- simple data flow through stdin/stdout where that keeps the code obvious

Avoid:

- deeply nested shell logic
- ad hoc string manipulation when `awk`, `sed`, `cut`, or `grep` expresses it better
- large global state surfaces
- hidden control flow in aliases when a function or script would be clearer
- shell rewrites of tasks that need richer parsing or structured data handling

## Text Processing And Pipelines

When working with streams and files:

- prefer `grep`, `sed`, `awk`, `sort`, `uniq`, `cut`, `paste`, `tr`, `xargs`, and `find` over manual parsing loops
- keep each pipeline stage focused on one transformation
- break long pipelines one stage per line when they no longer fit cleanly
- validate pipeline behavior on representative sample data
- use remote commands explicitly, for example `ssh host 'cmd | cmd'`, when the pipeline boundary matters

Use shell loops for orchestration and branching. Use text tools for text.

## Testing And Verification

When touching shell code:

- run syntax checks for the intended shell
- run `shellcheck` when available
- exercise success and failure paths
- verify comment, naming, and file-structure expectations when the surrounding shell context follows Google-style Bash
- verify cleanup behavior if the script creates files, directories, locks, or background processes
- do not claim portability beyond the shell/runtime you actually validated

## Validation Prompts

Use prompts like these to regression-test whether this skill still triggers and guides correctly:

- Should trigger: `Refactor this Bash backup script to stop building command strings, use arrays for arguments, and make failures explicit.`
- Edge case: `Harden this POSIX sh wrapper without introducing Bash-only features, and explain any portability limits you keep.`
- Should not trigger: `Give me a one-liner to find TODO comments in this repo.`

## When Not To Use

- If the task is primarily interactive shell setup, aliases, prompts, or dotfile ergonomics, treat it as shell environment configuration rather than shell-code authoring.
- If the task is primarily code review rather than implementation, do not use this authoring skill; review the shell changes directly instead.
- If the task needs complex parsing, large state, or multi-step business logic, move to a more structured language instead of forcing shell to carry it.

## Completion Checklist

Before considering the task complete, confirm:

- the intended shell and portability target were identified
- shell is still an appropriate tool for the task
- file, function, and TODO comments were added where the surrounding shell style requires them
- quoting and expansion behavior are intentional
- naming and top-level script structure match the intended shell style
- repeated behavior was factored without obscuring the main flow
- command-substitution exit status was not masked by `local`
- cleanup and error paths are explicit
- new shell files follow the intended extension, executability, and security expectations
- syntax checks, linting, and smoke tests were run when available
- the result is easier to operate and reason about than before
