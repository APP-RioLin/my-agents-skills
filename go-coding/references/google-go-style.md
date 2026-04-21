# Google Go Style Reference

Use this file when project conventions are absent or unclear, when a task explicitly asks for Google-style Go, or when package, API, naming, or concurrency decisions need tighter guardrails.

## Table Of Contents

- Sources
- Decision Order
- Style Priorities
- Naming
- Packages And Dependencies
- APIs, Errors, And Comments
- Context, Concurrency, And Interfaces
- Verification

## Sources

- Overview: https://google.github.io/styleguide/go/
- Guide: https://google.github.io/styleguide/go/guide
- Style Decisions: https://google.github.io/styleguide/go/decisions
- Best Practices: https://google.github.io/styleguide/go/best-practices
- Effective Go: https://go.dev/doc/effective_go

Existing project conventions still override these sources when the project has established patterns or configured tooling.

## Decision Order

Use this order when choosing between alternatives:

1. project configuration and existing project conventions
2. nearby package and module patterns that are consistent and intentional
3. `gofmt` and standard Go tooling
4. the Google Go style documents above

If the repo uses `gofumpt`, `golangci-lint`, `staticcheck`, code generation rules, or custom scripts, let those tools drive the final output.

## Style Priorities

From the canonical Google guide, prioritize:

1. clarity
2. simplicity
3. concision
4. maintainability
5. consistency

Use those priorities to break ties. Prefer the simplest construction that makes behavior and rationale obvious to a future reader.

## Naming

- Use `MixedCaps` or `mixedCaps` for identifiers; do not use snake_case in Go identifiers.
- Keep package names short, lowercase, and unbroken; avoid underscores except for specialized generated or test-package cases.
- Preserve common initialisms consistently, such as `ID`, `URL`, and `DB`.
- Do not prefix getters with `Get` unless "get" is part of the underlying domain concept or protocol.
- Keep receiver names short, type-related, and consistent across all methods on that type.
- Size local names to scope; short names are fine in tight scopes, while larger scopes need more descriptive names.
- Avoid repetitive names where the package, type, or method already provides the context.

## Packages And Dependencies

- Start with core language constructs and the standard library before adding dependencies.
- Keep package boundaries coherent; do not split packages too early or use interfaces to paper over poor package structure.
- Prefer concrete types by default, and let consumers define small interfaces around the methods they actually use.
- Export or return interfaces when the interface itself is the product or boundary, when encapsulation or runtime selection requires it, or when a well-justified package-boundary concern makes that tradeoff clearer.
- Avoid uninformative package names such as `util`, `common`, `helper`, or `model`.
- Do not use `import .`; rename awkward third-party imports explicitly when needed.
- Be careful copying structs from other packages, especially values that contain buffers, mutexes, or pointer-only method sets.

## APIs, Errors, And Comments

- Put `error` last in result lists and use `error` to signal ordinary failure.
- Return `nil` on success for functions that can fail.
- Treat non-error return values as unspecified on error unless the API documents otherwise.
- Do not use `panic` for normal error handling.
- Prefer clear, explicit control flow over compact cleverness that hides important details.
- Let code show `what`; use comments and docs to explain `why`, invariants, cleanup rules, and surprising behavior.
- Document concurrency guarantees only when they are non-obvious or part of the contract that callers need to rely on.
- Favor useful tests, examples, and failure messages when an API is subtle or easy to misuse.

## Context, Concurrency, And Interfaces

- Accept `context.Context` as the first parameter when a function or method needs it.
- Use `context.Context` itself in function signatures; do not replace it with a custom context type or a lookalike interface.
- In HTTP handlers, use `req.Context()` instead of adding a separate `ctx` parameter.
- In streaming RPC methods, use the context exposed by the stream instead of adding a separate `ctx` parameter.
- In tests, prefer `(testing.TB).Context()` as the initial context instead of creating a fresh base context.
- In entrypoints such as `main`, creating a base context is acceptable; avoid `context.Background()` in library code unless there is a documented reason.
- Do not add a context member to a struct type.
- Do not create custom context types.
- Prefer passing the caller's context instead of creating fresh `context.Background()` values in library code.
- Read-only operations are generally assumed safe for concurrent use; document behavior when mutation or synchronization is not obvious.
- Prefer tests to fail in the main `Test` function; do not call `t.Fatal` or `t.FailNow` from spawned goroutines.
- Use interfaces where they materially improve a caller boundary, not as default ceremony.

## Verification

Prefer this order unless the project defines something else:

1. `gofmt -w` on changed files or the project formatter
2. `go test ./...`
3. `go test -race ./...` when concurrency is relevant
4. `go vet ./...`
5. `staticcheck ./...` or `golangci-lint run` if the repo uses them

If the project includes generated Go code, rerun the project-native generators and reformat the generated output when the repo expects that workflow.
