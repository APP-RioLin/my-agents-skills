---
name: commit-message-authoring
description: Draft, rewrite, standardize, or review Git commit messages. Use when Codex needs to write a message from a diff, rewrite a commit as a Conventional Commit, tighten a title, fix commit wording, or suggest a squash message.
---

# Commit Message Authoring

Use this skill when the task is to write, rewrite, shorten, standardize, or review a Git commit message.

## Core Standard

Good commit messages:

- make the first line easy to scan in `git log --oneline`
- prefer one short descriptive subject over a long exhaustive one
- use the body only when it adds context the diff will not show
- describe the change in imperative mood
- follow explicit user instruction first, then enforced repo rules and visible conventions; if none are visible, default to Conventional Commits

This skill draws from:

- https://cbea.ms/git-commit/
- https://www.conventionalcommits.org/en/v1.0.0/

## Default Format

Default to:

`<type>[optional scope]: <description>`

Examples:

- `fix(auth): handle expired refresh tokens`
- `docs: clarify local setup steps`
- `refactor(cache): split key parsing from fetch path`

Rules:

- keep the entire first line concise; target about 50 characters, stretch only when clarity would suffer
- use one main intent per commit message
- prefer a specific verb and concrete object
- do not end the first line with a period
- if using a body, leave one blank line after the first line
- wrap body prose at roughly 72 characters
- use the body for why, constraints, side effects, or follow-up context
- use footers only when needed, such as `BREAKING CHANGE:` or issue refs

## Choosing A Style

Use this order:

1. explicit user instruction
2. enforced commit tooling or templates
3. visible repo history
4. Conventional Commits
5. plain cbea.ms-style imperative subject without a type prefix

If explicit user instruction conflicts with enforced tooling, say so briefly and satisfy the tooling.
If the user explicitly asks for a Conventional Commit or a plain subject line, honor that request unless enforced tooling blocks it.
If the repo clearly uses Conventional Commits, match it when the user has not asked for something else.
If the repo does not use type prefixes, do not force them unless the user asks.
If no convention is visible, use Conventional Commits by default.

## Choosing The Type

Prefer the narrowest accurate type:

- `feat`: new user-facing or API-facing behavior
- `fix`: bug fix
- `refactor`: internal restructure without intended behavior change
- `docs`: documentation only
- `test`: tests only
- `perf`: performance improvement
- `build`, `ci`, `chore`: tooling, infra, or maintenance work
- `revert`: revert an earlier change

Add a scope only when it makes the message clearer:

- good: `fix(parser): reject empty arrays`
- skip it when it adds noise

## Description Rules

After the prefix:

- use imperative mood: `add`, `fix`, `remove`, `clarify`
- prefer short, concrete wording over implementation detail
- avoid vague subjects like `updates`, `misc fixes`, `cleanup`
- keep the dominant change in focus; move secondary detail to the body or drop it

Capitalization note:

- cbea.ms recommends capitalizing the subject line
- Conventional Commits examples often use lowercase after the prefix
- when combining the two, prefer visible repo history first; if no convention is visible, default to lowercase after the prefix so the line reads naturally, for example `fix: handle empty config values`

## When To Add A Body

Add a body only when the first line is not enough.
Typical reasons:

- the reason for the change is not obvious from the diff
- the change has important side effects or tradeoffs
- a bug fix needs problem context
- a breaking change needs migration detail

Keep bodies short. Prefer 1 to 3 compact paragraphs or a brief bullet list.

Example:

```text
fix(sync): ignore stale revision tokens

Avoid applying tokens generated before the last successful refresh.
This prevents older background jobs from overwriting newer state.
```

## Breaking Changes And Footers

Mark breaking changes explicitly:

- `feat(api)!: remove legacy webhook secret`
- `BREAKING CHANGE: legacy webhook secret is no longer read`

Use other footers only when they add real value:

- `Refs: #123`
- `Reviewed-by: Name`

## Workflow

1. Inspect the diff, staged files, user summary, and existing commit message if one was provided.
2. Determine whether the task is authoring, rewriting, or review.
3. Identify the single dominant intent.
4. Check style inputs in order: explicit user instruction, enforced tooling, visible repo history, then skill defaults.
5. If the task is authoring or rewriting, draft one concise first line.
6. If the task is review, state concise findings first and then provide one improved rewrite when changes are needed.
7. Add body or footers only if they carry information the subject cannot.
8. If the change mixes unrelated intents, say the commit should be split or choose the dominant intent explicitly.

## Review Mode

When the user asks to review a commit message:

- assess the message against visible repo conventions or the default rules in this skill
- lead with the concrete problems, not background theory
- if the message is already good enough, say so plainly
- if the message needs work, provide one revised message after the findings
- if the user asked only for a rewrite, skip the critique unless ambiguity or a convention conflict matters

## Output Defaults

By default:

- return one best commit message, not a menu
- return just the first line unless a body is warranted
- for review requests, return findings first and a rewrite only when it improves the result
- if the user asked for copy-ready text, provide the full message in a fenced block
- if confidence is low because the change set is too broad, say so briefly and explain the ambiguity

## Completion Checklist

Before finishing, verify:

- the subject is short and descriptive
- the verb is imperative
- the main line has no trailing period
- the body, if present, explains why or impact instead of narrating the diff
- the type and scope, if used, are accurate
- the result matches visible repo conventions or the chosen default

## Validation

Test prompts:

- `write a commit message for this diff` -> should trigger
- `rewrite this commit as a conventional commit` -> should trigger
- `review this commit message: chore: stuff` -> should trigger
- `rewrite this commit without a type prefix` -> should trigger and honor the user override unless tooling blocks it
- `squash these three commits and give me one message` -> should trigger
- `write a commit message with a body for this breaking API change` -> should trigger
- `explain what this commit does` -> should NOT trigger; this is read-only understanding, not commit-message authoring
