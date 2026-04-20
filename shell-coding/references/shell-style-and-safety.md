# Shell Style And Safety

Use this file when authoring or hardening shell scripts and you need concrete rules for shebangs, quoting, functions, arrays, traps, or control-flow style.

## Sources

- Google Shell Style Guide: https://google.github.io/styleguide/shellguide.html
- Missing Semester, Course Overview + Introduction to the Shell: https://missing.csail.mit.edu/2026/course-shell/
- Missing Semester, Command-line Environment: https://missing.csail.mit.edu/2026/command-line-environment/

Local script, command, repo, and deployment constraints override these sources when the surrounding shell context already has an intentional target or toolchain.

The Google guide is Bash-oriented. Apply its Bash-specific rules directly when the target is Bash, and adapt them carefully when the surrounding shell context intentionally targets another shell.

## Language Choice

- Use shell mainly for glue code, wrappers, task runners, and simple orchestration.
- If a script is growing large, stateful, or parsing-heavy, move it to a more structured language early.
- If the runtime expects Bash, it is fine to use Bash features intentionally.
- If portability matters, stay within the shell features the repo or runtime actually supports.

## Shebang And Strictness

- Use an explicit shebang that matches the intended shell.
- For Bash executables, prefer `#!/bin/bash` with minimal flags and set options in the script body rather than in the shebang.
- For Bash scripts, prefer enabling strict mode early with `set -euo pipefail` when compatible with the script's control flow.
- Keep shell options set in the script body rather than relying on invocation flags alone.

## Files And Security

- For Google-style Bash executables, use a `.sh` extension or no extension according to how the file is deployed.
- For Google-style Bash libraries, use a `.sh` extension and do not mark the file executable.
- Do not use SUID or SGID shell scripts.
- If elevated access is needed, prefer an explicit `sudo` boundary or another non-SUID mechanism.

## Comments

- Start maintained script files with a brief top-level comment describing what the file does.
- Add function header comments when a function is not both obvious and short.
- In libraries, give every function a header comment regardless of length.
- Follow the Google function comment shape when it matters: description, globals, arguments, outputs, and returns.
- Use implementation comments only for tricky, non-obvious, or important behavior.
- Format temporary follow-up notes as searchable TODOs, for example `TODO(name): ...`.

## Formatting

- Prefer 2-space indentation for new shell code unless the repo uses a different established style.
- Do not use tabs except where shell syntax explicitly requires them, such as tab-stripping here-docs.
- Keep lines at 80 columns when practical. Break long strings with here-docs or embedded newlines instead of letting lines sprawl.
- Keep lines readable and split long pipelines one stage per line.
- When splitting a long pipeline or logical compound, use a trailing `\` and indent the continued line by 2 spaces with the operator at the start of the continuation.
- Keep `; then` and `; do` on the same line as `if`, `while`, `for`, or `until`.
- Indent `case` arms by 2 spaces and keep the arm body visually separate from `;;`.
- Avoid `;&` and `;;&` in `case` statements.
- Use blank lines to separate logical blocks.
- Keep comments focused on intent, invariants, or non-obvious behavior.

## Quoting And Expansion

- Quote variable and command expansions by default.
- Use `"${var}"` for ordinary named scalar values and `"${array[@]}"` for Bash arrays.
- Prefer unbraced forms like `"$1"` and `"$@"` for single-character positional or special parameters unless braces are required for clarity, such as `${10}`.
- Use arrays to hold argument lists in Bash instead of building command strings.
- Use `"$@"` when forwarding arguments in Bash. Treat `$*` as a special case for deliberate join-style output such as certain messages or logs.
- When iterating over argv for clarity, prefer `for arg in "$@"; do`.
- Do not use raw command substitution to build argv or arrays. Command substitution yields a single string that will then be split and glob-expanded.
- In Bash, prefer builtins such as parameter expansion, `[[ ... =~ ... ]]`, and shell arithmetic when they express the operation clearly and avoid unnecessary external processes.
- In Bash regex matches, do not quote the RHS of `[[ string =~ regex ]]` when you intend regex semantics. Use `BASH_REMATCH` for captured groups when extraction is needed.
- Use `$(...)` instead of backticks for command substitution.
- Use `read -r` to avoid mangling backslashes.
- Avoid `eval` unless there is no safer design and the input is fully controlled.
- Use an explicit path such as `./*` when wildcard expansion could otherwise turn filenames beginning with `-` into option-like arguments.

## Tests, Conditionals, And Functions

- In Bash, prefer `[[ ... ]]` for conditionals.
- Prefer `==` over `=` inside `[[ ... ]]`, and use `-z` or `-n` for empty-string checks.
- Use `(( ... ))` or `$(( ... ))` for arithmetic instead of `let`, `expr`, or `$[ ... ]`.
- Prefer `(( ... ))` for numeric comparisons instead of relying on `[[ ... ]]` with `<` or `>`.
- Use functions for reusable behavior and keep them short.
- Use lower_snake_case for functions and ordinary variables. Use `pkg::func` only when a library-style namespace is helpful and consistent with the repo.
- Declare functions in the `foo()` form with no space before `()`. The `function` keyword is optional, but use it consistently if the surrounding shell context already does.
- Keep braces on the same line as the function name.
- Use `local` inside Bash functions to limit scope.
- Separate declaration from assignment when a `local` value comes from command substitution and the exit status matters.
- Keep top-level flow small; a `main "$@"` pattern is usually clearer than large top-level bodies.
- In non-trivial Bash scripts, put functions together near the top, keep executable code out from between them, and end the file with `main "$@"`.
- Capitalize exported or readonly constants with underscores and place them near the top of the file.
- Keep shell source filenames lowercase with underscores when needed.
- Prefer functions over aliases for any reusable behavior that takes arguments.

## Error Handling And Cleanup

- Send diagnostics to `stderr`.
- Return non-zero on failure and fail loudly rather than masking partial success.
- Check command return values directly with `if ! cmd; then` or an immediate status check.
- Capture `PIPESTATUS` immediately if you need per-segment pipeline failures; any later command will overwrite it.
- Use `trap` for cleanup of temp files, temp directories, locks, or child processes.
- Be explicit when non-zero exit codes are expected as part of normal control flow.

## Common Hazards

- Backticks instead of `$(...)`.
- Unquoted expansions causing word splitting or globbing.
- Storing argument lists in strings instead of arrays.
- Building arrays or argv with command substitution such as `declare -a files=($(cmd))` or `mybinary $(get_arguments)`.
- Using `for var in $(cmd)` for line-oriented data.
- Piping into `while read` when parent-shell state must be preserved.
- Using `local var="$(cmd)"` and then checking `$?`, which reports `local` rather than the command substitution.
- Forgetting that `(( expr ))` can exit non-zero when the expression evaluates to zero under `set -e`.
- Parsing human-oriented output when a machine-oriented command or option exists.
- Letting implicit globals leak between functions.
- Using aliases in scripts instead of functions.
- Using shell for problems that need structured data handling.
