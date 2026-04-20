# Shell Tools And Pipelines

Use this file when the task is mostly about composing commands, reshaping text streams, or deciding whether coreutils can solve the problem more clearly than custom shell logic.

## Sources

- Google Shell Style Guide: https://google.github.io/styleguide/shellguide.html
- Learn Bash in Y Minutes: https://learnxinyminutes.com/bash/
- Missing Semester, Course Overview + Introduction to the Shell: https://missing.csail.mit.edu/2026/course-shell/
- Missing Semester, Command-line Environment: https://missing.csail.mit.edu/2026/command-line-environment/
- Missing Semester, Data Wrangling: https://missing.csail.mit.edu/2020/data-wrangling/

## Core Heuristic

Use shell to compose tools. Use the tools to do the actual text processing.

When Bash builtins express the operation clearly, prefer them over spawning extra processes. Reach for external tools when the work is genuinely text- or record-oriented and the tool makes the transformation easier to read.

When the task is about:

- finding files, start with `find`
- filtering lines, start with `grep`
- simple substitutions, start with `sed`
- field extraction, aggregation, or record-oriented transforms, start with `awk`
- sorting or counting, use `sort`, `uniq`, `wc`, and `paste`

Avoid replacing those tools with long shell loops unless the logic genuinely needs branching or orchestration.

## Pipelines

- Keep one transformation per stage.
- Keep a short pipeline on one line when it fits cleanly.
- Break long pipelines across lines once readability drops.
- When splitting a long pipeline, put one stage per line and keep the operator at the start of the continuation line.
- Validate the intermediate shape when a pipeline becomes non-trivial.
- If later logic depends on which stage failed, copy `PIPESTATUS` immediately after the pipeline before running any other command.
- Remember that a pipeline stage may run in a subshell, which matters if you expect variable updates to survive.

## Reading Command Output

- Prefer `while read -r ...; do ...; done < <(cmd)` or `readarray -t` over `cmd | while read ...` when state must survive after the loop.
- Avoid `for item in $(cmd)` for line-oriented or whitespace-containing output.
- Do not build argv or arrays from raw command substitution such as `mybinary $(cmd)` or `files=($(cmd))`. Use `readarray -t`, process substitution, or explicit parsing instead.
- Keep the loop in shell only when you genuinely need branching or orchestration around each item.

## Command Substitution And Subshells

- Use command substitution when you truly need a command's stdout as a value.
- Use `$(...)` instead of backticks.
- Use subshells like `(cd dir; cmd)` when directory changes or environment changes should not leak to the parent shell.
- Prefer explicit paths and explicit working-directory changes over fragile chained state.

## Data Wrangling Patterns

- Use Bash parameter expansion, `[[ ... =~ ... ]]`, and shell arithmetic for simple in-shell transformations when that is clearer than shelling out.
- Use `awk` when the problem is column- or record-shaped.
- Use `sed` for targeted substitutions, not for building large ad hoc parsers.
- Use `sort | uniq -c` for frequency-style summaries.
- Use `paste -sd,` or similar tools when joining lines into a single delimited value is clearer than writing shell loops.
- Use shell arithmetic for arithmetic. Avoid `expr`, `let`, and deprecated `$[ ... ]` forms.
- When globbing files for another command, prefer an explicit path such as `./*` if a bare `*` could be misread as an option.

## Remote Commands And Process Control

- Use `ssh host 'cmd'` when the command should run remotely.
- Be explicit about which side of a pipeline runs remotely and which runs locally.
- Use `trap` for cleanup around background jobs or temporary resources.
- Use `nohup`, `disown`, or a terminal multiplexer intentionally when long-running commands must survive terminal shutdown.

## Choosing A Better Tool

Move beyond shell when:

- parsing becomes deeply nested or stateful
- correctness depends on structured data models
- the script needs rich error recovery
- the code is growing into an application rather than a wrapper
