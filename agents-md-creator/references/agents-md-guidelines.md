# AGENTS.md Guidelines

Use this file when creating or reviewing `AGENTS.md` files and you need portable authoring guidance plus runtime-specific discovery notes for Codex, Claude Code, and Gemini.

## Sources

- OpenAI Codex, AGENTS.md guide: https://developers.openai.com/codex/guides/agents-md
- Anthropic Claude Code, memory and project instructions: https://code.claude.com/docs/en/memory
- Google Gemini CLI, context files and `GEMINI.md`: https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/gemini-md.md
- Google Cloud Gemini Enterprise, prompt best practices: https://docs.cloud.google.com/gemini/enterprise/docs/agent-designer/create-agent#best-practice-prompt

The runtime-specific notes below are the most time-sensitive part of this reference. Re-check the linked runtime docs before changing discovery, precedence, or filename guidance.

## Portable AGENTS.md Pattern

For cross-agent portability, prefer this model:

- keep shared team instructions in `AGENTS.md`
- scope the file to the narrowest directory that actually needs the guidance
- use nested `AGENTS.md` files only when a subtree genuinely needs narrower rules
- keep runtime-specific glue thin so `AGENTS.md` stays the source of truth
- duplicate instructions only as a last resort

Useful content to include:

- the layout of the project or governed area
- commands to build, test, lint, or run checks
- style or review expectations that are specific to the repo
- constraints around security, secrets, deploys, or risky operations
- defaults that remove ambiguity during execution

Useful content to avoid:

- generic advice that belongs in the model’s default behavior already
- long background explanations with no operational consequence
- duplicated text across parent and nested files
- runtime-specific wrappers that drift away from the shared `AGENTS.md`

## Scope Rule

Choose the narrowest file that matches the operational boundary:

1. root `AGENTS.md` for repo-wide rules
2. nested `AGENTS.md` only when a subdirectory genuinely needs narrower instructions
3. user-level or runtime-level bridge files only when a platform cannot consume `AGENTS.md` directly

When creating a nested file, make sure it refines or overrides the parent rather than restating it.

## Cross-Platform Bridges

When different agents use different instruction filenames, prefer one of these approaches:

- direct support: the runtime reads `AGENTS.md` natively
- import bridge: a runtime-specific file imports `AGENTS.md` and adds only the small amount of platform-specific guidance
- filename configuration: the runtime is configured to also treat `AGENTS.md` as an instruction file

Use the thinnest bridge that works. The bridge should usually be shorter than the shared `AGENTS.md`.

## Runtime Notes

### Codex

The current OpenAI guide establishes this model:

- Codex reads instruction files before doing work.
- Codex builds the instruction chain once per run, which in the TUI usually means once per launched session.
- Global scope: in the Codex home directory (`~/.codex` unless `CODEX_HOME` is set), Codex reads `AGENTS.override.md` if it exists and is non-empty; otherwise it reads `AGENTS.md`. Only one home-scope file is used.
- Project scope: starting at the project root and walking down to the current directory, Codex checks each directory in this order: `AGENTS.override.md`, `AGENTS.md`, then any fallback names listed in `project_doc_fallback_filenames`. If Codex cannot find a project root, it only checks the current directory.
- Codex includes at most one file per directory.
- Merge order: Codex concatenates the selected files from root to current directory. Files closer to the current directory override earlier guidance because they appear later in the combined prompt.
- Empty files are ignored.
- Discovery stops when the combined size reaches `project_doc_max_bytes`, which defaults to 32 KiB.

Codex-specific notes:

- if `AGENTS.override.md` and `AGENTS.md` exist in the same directory, Codex uses the override and ignores the sibling `AGENTS.md`
- use `project_doc_fallback_filenames` when you need Codex to treat alternate names as instruction files
- if the team wants `AGENTS.md` to remain canonical, keep overrides and fallback names to the minimum needed for Codex behavior

### Claude Code

Claude Code does not read `AGENTS.md` directly as its primary memory file. The current Anthropic guidance is:

- Claude Code uses `CLAUDE.md` files for project, user, local, and managed instructions
- project instructions can live in `./CLAUDE.md` or `./.claude/CLAUDE.md`
- user instructions live in `~/.claude/CLAUDE.md`
- local per-project personal preferences can live in `CLAUDE.local.md`
- Claude Code can import other files into `CLAUDE.md` using `@path`

Portable pattern for Claude Code:

- keep shared instructions in `AGENTS.md`
- create a thin `CLAUDE.md` that imports `AGENTS.md`
- add only Claude-specific behavior below the import when needed

### Gemini

Gemini CLI uses `GEMINI.md` by default, but its configuration can treat `AGENTS.md` as a context filename.

The current Gemini guidance is:

- global context defaults live in `~/.gemini/GEMINI.md`
- workspace and parent-directory context files are loaded hierarchically
- additional just-in-time context files can load when tools access subdirectories
- the CLI supports `context.fileName` in `settings.json` so custom names, including `AGENTS.md`, can be recognized
- `/memory show` and `/memory reload` help inspect and refresh the active context

Portable pattern for Gemini:

- keep shared instructions in `AGENTS.md`
- configure Gemini to include `AGENTS.md` in `context.fileName`
- only fall back to `GEMINI.md` as a bridge when configuration is unavailable or not shared

Use the official operational checks when the environment allows it:

- `codex --ask-for-approval never "Summarize the current instructions."` to confirm the active instruction text
- `codex --cd path/to/subdir --ask-for-approval never "List the instruction sources you loaded."` to verify nested precedence
- `codex status` to confirm the workspace root when discovery seems wrong
- `/memory` in Claude Code to inspect loaded memory files
- `/memory show` and `/memory reload` in Gemini CLI to inspect and refresh context files
- inspect `~/.codex/log/codex-tui.log` or recent session logs when you need to audit which files loaded

Runtime-specific config and troubleshooting details:

- Codex: use `project_doc_fallback_filenames` when the repo already relies on alternate instruction filenames
- Codex: raise `project_doc_max_bytes` or split instructions across nested directories if files are truncated
- Codex: restart Codex after changing config or instruction files when the active chain appears stale
- Codex: check `CODEX_HOME` before debugging home-scope files, because a custom value changes the effective global directory
- Claude Code: keep `CLAUDE.md` concise, prefer imports for shared material, and avoid duplicating `AGENTS.md`
- Gemini: use `context.fileName` when you want `AGENTS.md` discovered alongside or instead of `GEMINI.md`

## First-Person House Rule

This skill enforces first-person narrative as a writing convention for instruction files.

Reasoning:

- it reads like direct operating guidance
- it makes ownership explicit
- it avoids the ambiguity of mixing policy voice, second-person voice, and detached prose

This is a local authoring convention, not a platform requirement. It is compatible with Codex, Claude Code, and Gemini because those guides focus on discovery and usefulness rather than narrative voice.

Preferred pattern:

- `I run targeted tests before I finish.`
- `I ask before making destructive changes.`
- `I use \`rg\` for search in this repo.`

## Gemini Prompt Best-Practice Notes

The Gemini guidance is useful for deciding what makes instructions actionable:

- provide enough context for the task
- say what good output looks like
- include boundaries and exceptions
- tell the agent when it should ask clarifying questions
- prefer direct, concrete wording over vague principles

For `AGENTS.md`, that usually means:

- include exact commands instead of generic references to testing
- say which risky actions require confirmation
- note missing information that should trigger a question
- keep defaults explicit so the agent does not have to guess

## Practical Authoring Pattern

A strong instruction file usually answers:

- Where am I operating?
- Which `AGENTS.md` file should exist here, if any?
- What commands do I run here?
- What tools do I prefer here?
- What should I avoid or ask about first?
- If this runtime does not read `AGENTS.md` natively, what is the thinnest bridge?
- What command proves the intended runtime loaded the shared instruction chain?
- What does done look like here?

If the file does not answer those questions, it is probably still too generic.
