---
name: go-coding
description: "Use this skill when writing or changing Go code: implement a feature, fix a bug, refactor a package, add or improve tests, simplify an API, or make existing Go code more idiomatic. It covers authoring defaults, project-convention discovery, Google Go style references, formatting, and validation with standard Go tooling. It is for implementation work, including prompts like `make this Go code idiomatic`, not review-only requests that only ask for findings without code changes."
---

# Go Coding

Use this skill when the task is to implement, refactor, debug, modernize, or extend Go code while keeping readability, correctness, and change safety high.

This skill is for authoring Go code, not review-only findings. If the user only wants a critique of existing Go code or a PR without asking for a patch, review the code directly instead of loading this authoring skill.

## Core Standard

Good Go code changes:

- preserve intended behavior unless the task says to change it
- follow existing module, package, and tool conventions first
- prefer idiomatic Go over patterns imported from other languages
- keep package boundaries, APIs, and data flow easy to understand
- choose the least mechanism that fully solves the problem
- keep errors explicit and actionable
- make context use, concurrency behavior, and cleanup requirements obvious
- add or update tests near risky behavior
- minimize dependencies and hidden magic

The goal is not to maximize abstraction. The goal is to produce Go code that is clear, simple, maintainable, and easy to validate.

## Style And Decision Order

Use this order when making choices:

1. existing project conventions and configured tools such as `go.mod`, `go.work`, `Makefile`, `Taskfile.yml`, `golangci.yml`, `staticcheck.conf`, or repository scripts
2. surrounding package and module patterns when they are consistent and intentional
3. standard Go defaults enforced by `gofmt` and the Go toolchain
4. Google Go Style Guide, Style Decisions, and Best Practices

If the project already uses stricter tools such as `gofumpt`, `golangci-lint`, or `staticcheck`, let those tools drive the final shape of the code.

Read [references/google-go-style.md](references/google-go-style.md) when:

- project conventions are absent or unclear
- the task explicitly asks for Google-style Go or more idiomatic Go
- naming, API, context, interface, or package-boundary decisions matter
- the refactor is large enough to need explicit guardrails

## Quick Workflow

1. Inspect the existing module, package layout, tests, and repo tooling before editing.
2. Understand the required behavior, inputs, outputs, invariants, and failure modes.
3. Choose the simplest design that keeps the package/API surface coherent.
4. Implement with clear names, focused functions and types, explicit error handling, and minimal surprise.
5. Format with the project formatter; `gofmt` is the minimum default.
6. Run the relevant validation commands for the repo.
7. Update tests, examples, docs, or comments when contracts or rationale need to stay clear.
8. Summarize behavior changes, validation, and any residual risk.

## Expected Output

A successful use of this skill should normally produce:

- Go code or a patch that matches the repo's conventions or the fallback defaults in this skill
- package and API changes that remain easy to read and reason about
- explicit handling of errors, cleanup, context flow, and concurrency semantics where relevant
- a concise summary of behavior changes, validation run, and any residual risk

## Authoring Priorities

Default order:

1. correctness
2. regression safety
3. clarity and simplicity
4. maintainability
5. testability
6. performance
7. style

Style matters, but readable behavior and safe evolution matter more.

## Detailed Guidance

For concrete guidance on naming, errors, packages, contexts, interfaces, documentation, and verification, read [references/google-go-style.md](references/google-go-style.md).

Use that reference when you need reminders about:

- package and identifier naming
- receiver names, initialisms, getters, and repetitive naming
- error handling, `panic`, context placement, and cleanup
- interface placement, dependency choices, package size, and concurrency contracts
- Go doc comments, useful failures, and verification commands

## Testing And Verification

When touching Go code:

- add or update tests for behavior changes
- prefer regression tests for bug fixes
- run the project's formatter; `gofmt` is the minimum default
- run `go test ./...` unless the repo defines a narrower or different command
- run `go test -race ./...` when concurrency changes are involved or the project already uses race checks
- run `go vet ./...` when applicable
- run `staticcheck ./...` or `golangci-lint run` when the project is configured for them
- rerun generators when the project uses generated Go artifacts
- do not claim tool compliance if the tool was not run

## Validation Prompts

Use prompts like these when checking whether the skill still triggers and guides well:

- should trigger: `Implement this feature in our Go service, follow the repo's style, and add the needed tests.`
- should trigger: `Make this Go package more idiomatic and simplify the API without changing behavior.`
- should trigger: `Fix the bug in this Go handler, clean up the implementation, and run the relevant checks.`
- should not trigger: `Review this Go package for style and concurrency issues.` This is review-only.
- should not trigger: `Explain how Go contexts work.` This is explanatory, not a code-authoring task.

## When Not To Use

- If the task is primarily review-only and does not ask for code changes, do not use this authoring skill.
- If the task is primarily architecture strategy without concrete Go edits, use a design workflow instead.
- If the task is not mainly Go, use a more relevant language or domain skill.

## Completion Checklist

Before considering the task complete, confirm:

- existing module, package, and tool conventions were checked first, or the fallback defaults in this skill were used when none existed
- behavior changes are intentional
- package and API boundaries still make sense
- errors, cleanup, context flow, and concurrency expectations are explicit where needed
- tests or validation cover the risky paths
- formatter, tests, and relevant Go tooling were run when available
- the result is more idiomatic and easier to maintain than before
