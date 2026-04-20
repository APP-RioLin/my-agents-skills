---
name: agents-md-creator
description: Use this skill when creating, updating, reviewing, or consolidating an `AGENTS.md` file as a shared instruction document for coding agents, including requests to add nested `AGENTS.md` instructions, set global agent instructions, or configure `AGENTS.md` as an instruction filename. It covers how to scope `AGENTS.md` to the right directory, write clear first-person agent instructions, keep `AGENTS.md` as the main source of truth, adapt that guidance for runtimes such as Codex, Claude Code, and Gemini when needed, and capture setup and validation commands without bloating the file.
---

# Agents MD Creator

Use this skill when the task is to create, improve, review, or reorganize an `AGENTS.md` file.

This skill specializes in `AGENTS.md`, not general skill creation. Use `skill-creator` for broader `SKILL.md` work.

## Core Standard

Good `AGENTS.md` files:

- are written in first person
- describe how I work, not how "you" should behave
- are scoped to the correct directory boundary
- keep `AGENTS.md` as the shared source of truth unless the user explicitly wants another primary filename
- give concrete commands, paths, and validation steps
- focus on repo-specific expectations before generic advice
- stay easy to scan during execution

The goal is not to write a policy essay. The goal is to give the agent a compact operating agreement it can actually follow.

## First-Person Narrative Is Required

Write `AGENTS.md` in first person unless the user explicitly asks for another style.

Prefer:

- `I run targeted tests before I finish a change.`
- `I use \`rg\` for text search unless the repo requires another tool.`
- `I ask before making destructive changes such as dropping data or force-resetting git state.`

Avoid:

- `You should run tests before finishing.`
- `The agent must use rg.`
- `Developers should ask before destructive changes.`

First person makes ownership and behavior clearer, especially when the file is read as direct operating guidance by an agent runtime.

## Scope And Placement

Choose the narrowest file that matches the instructions:

1. a user-level `AGENTS.md` when the request is for reusable personal defaults and the target runtime can consume that file directly or through a documented bridge
2. a root `AGENTS.md` for repo-wide guidance
3. a nested `AGENTS.md` only when a subdirectory genuinely needs narrower instructions
4. a user-level or runtime-level bridge only when a specific tool needs extra configuration to consume the shared `AGENTS.md`

Before writing, identify:

- which directory the file governs
- whether the request is for user-level defaults or project-level guidance
- whether broader instructions already exist
- whether the new file should narrow or extend nearby guidance
- whether the target runtime needs a bridge, import, or filename configuration so it can consume `AGENTS.md`

If the request is ambiguous, inspect the repo structure first and then ask only if the scope still matters materially.

Read [references/agents-md-guidelines.md](references/agents-md-guidelines.md) when:

- you need the official AGENTS.md discovery and layering model
- you need help choosing user-level versus root versus nested instructions
- you need help adapting `AGENTS.md` to a runtime that prefers another filename or discovery rule
- you are deciding what kind of context and constraints belong in the file
- you need fallback filename, size-limit, or troubleshooting details
- you want prompt-authoring reminders derived from Gemini guidance

## Workflow

1. Inspect the repo layout, existing `AGENTS.md` files, and any runtime-specific bridge files or config when discovery behavior matters.
2. Identify the governed scope, audience, and main workflows.
3. Collect repo-specific commands, constraints, and validation steps.
4. Draft concise first-person guidance in `AGENTS.md` with explicit defaults.
5. Keep commands concrete and paths exact.
6. If a target runtime does not read `AGENTS.md` directly, add the thinnest possible bridge so `AGENTS.md` remains the shared source.
7. Review for overlap, contradiction, and unnecessary generic advice.
8. Verify the file is easy to scan, materially useful during execution, and actually loads in the intended scope.

## Expected Output

A successful use of this skill should normally produce:

- the correctly placed `AGENTS.md` for the target user, repo, or nested scope
- first-person instructions with concrete commands, paths, defaults, and escalation points
- guidance that complements broader instruction files instead of duplicating them
- when needed, a minimal runtime-specific bridge or config note so Codex, Claude Code, or Gemini can consume the same `AGENTS.md`
- a concise note on how the result was validated, including any command used to confirm the intended runtime loaded the shared instructions

## What To Capture

Capture the instructions that actually change agent behavior, such as:

- setup or bootstrap commands
- test, lint, and validation commands
- preferred tools and search commands
- coding and review expectations specific to the repo
- deployment, data, or security boundaries
- directory-specific differences for nested files
- escalation rules for destructive or risky operations
- the small amount of runtime-specific glue needed when a tool does not read `AGENTS.md` directly

Do not fill the file with generic engineering advice that would apply everywhere.

## Structure Defaults

Prefer a compact structure like:

1. purpose or scope
2. how I work in this area
3. commands I run
4. repo-specific constraints or boundaries
5. completion or validation checklist

Use headings when they improve scanning. Skip them when the file is short enough without them.

If a runtime-specific bridge exists, keep it thin and make it obvious that `AGENTS.md` is the main source of truth.

## Prompt-Authoring Rules For AGENTS.md

When deciding what to include, prefer guidance that:

- provides enough context for the task
- clearly states boundaries and defaults
- explains expected outputs or validation
- says when I should ask clarifying questions

Prefer direct operational language over vague principles.

Good:

- `I run \`pytest tests/unit\` after touching service logic.`
- `I ask before rotating credentials, deleting resources, or rewriting commit history.`
- `If the request is missing an environment or tenant, I ask for it before running deploy commands.`

Weak:

- `Be careful with deployments.`
- `Follow best practices.`
- `Try not to break anything.`

## Choose Defaults, Not Menus

Do not present multiple equal options without guidance.

Prefer:

- one default search tool
- one default test command per area
- one recommended edit path
- short exception notes only when they matter

If the repo has multiple workflows, explain which one I use first and when I switch.

## Nested Files

Use nested instruction files only when a subdirectory genuinely needs different instructions.

Default choice:

- use nested `AGENTS.md` when the subdirectory needs narrower guidance than the parent
- keep the nested file additive and specific rather than restating the parent
- reach for runtime-specific override files only when a platform requires them and the shared `AGENTS.md` plus a bridge is not enough

Good reasons:

- different test commands
- different toolchains
- stricter deploy or security rules
- a separate service or package with its own workflow

Bad reasons:

- cosmetic wording changes
- repeating the same instructions everywhere
- splitting files just to mirror the directory tree

Nested files should add or refine guidance, not duplicate the parent file verbatim.

## Cross-Platform Pattern

Keep shared team instructions in `AGENTS.md` and use the thinnest bridge or filename configuration that lets other runtimes consume that same file.

Read [references/agents-md-guidelines.md](references/agents-md-guidelines.md) when you need runtime-specific discovery, precedence, bridge, or filename details for Codex, Claude Code, or Gemini.

## Validation

Before finishing:

- verify the file is written in first person
- verify the scope matches the target directory
- verify commands are real, concrete, and repo-specific
- verify there is no contradiction with nearby `AGENTS.md` files
- verify any runtime-specific bridge still keeps `AGENTS.md` as the shared source of truth
- verify the file helps with real tasks rather than repeating generic norms
- run a runtime-appropriate command to confirm the intended instruction chain loads when the environment allows it

Useful validation prompts:

- `Create a user-level AGENTS.md with my default working agreements for tests and risky changes.`
- `Create an AGENTS.md for this repo that captures how I run tests and handle risky changes.`
- `Update this nested AGENTS.md for the frontend package without duplicating the root file.`
- `Review this AGENTS.md and tighten it so it reads like direct operating guidance.`
- `Make this AGENTS.md the shared source for Codex, Claude Code, and Gemini without duplicating the instructions.`
- `Configure this repo so \`AGENTS.md\` is treated as an instruction filename without duplicating the shared instructions.`
- wrong-skill check: `Create a reusable SKILL.md for Python coding.`

Useful verification commands when the runtime is available:

- `codex --ask-for-approval never "Summarize the current instructions."`
- `codex --cd path/to/subdir --ask-for-approval never "List the instruction sources you loaded."`
- `codex status`
- `/memory` in Claude Code
- `/memory show` or `/memory reload` in Gemini CLI

## Completion Checklist

Before considering the task complete, confirm:

- the right `AGENTS.md` scope was chosen
- the file is written in first person
- defaults are explicit
- risky operations and escalation points are clear
- commands and paths are concrete
- nested guidance adds signal instead of duplication
- any runtime-specific bridge is thinner than the shared `AGENTS.md` itself
- the result is concise enough to be useful during execution
