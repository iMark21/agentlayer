# AI-Ready Bootstrap Manual

## Purpose

This tool exists for a common problem:

- many repos still have no AI-Ready layer at all
- some repos already have partial files such as `AGENTS.md`, `CLAUDE.md`, or Copilot instructions
- teams want one canonical system, but different developers use different AI runtimes

The operating model is:

1. install a canonical `.ai/` layer
2. choose one, two, or several runtimes
3. generate only the adapters required for those runtimes
4. keep Git governance and repository conventions explicit
5. if the repo already has AI files, audit first and standardize instead of blindly overwriting

## Core Model

### Canonical Layer

`.ai/` is the source of truth.

It contains:

- context
- architecture notes
- dependency notes
- feature inventory
- repository workflow
- recent changes and gotchas
- decision framework
- rules by language, UI, feature, testing, analytics, and data
- agent playbooks
- deterministic skills

### Runtime Adapters

Adapters are intentionally thin.

| Runtime | Adapter | Purpose |
| --- | --- | --- |
| `codex` | `AGENTS.md` | Codex entry point |
| `claude` | `CLAUDE.md` | Claude Code entry point |
| `copilot` | `.github/copilot-instructions.md` plus `.github/instructions/` | GitHub Copilot entry point |
| `cursor` | `.cursor/rules/ai-ready.mdc` | Cursor entry point |
| `generic` | `AI-READY.md` | Universal adapter for any AI |

This avoids the usual failure mode where every runtime has a different truth.

## Supported Project Types

Current automatic detection:

- `android`
- `ios`
- `web`
- `backend`
- `generic`

### iOS Support

iOS is not a fallback mode.

The bootstrap treats it as a first-class project type and detects:

- `.xcodeproj`
- `.xcworkspace`
- `Package.swift`
- `Podfile`

The generated iOS guidance is Swift and SwiftUI oriented and explicitly covers:

- screen or flow-level state ownership
- structured concurrency and `async/await`
- `@MainActor` expectations for UI-facing state owners
- SwiftUI/UIKit boundaries
- preview/sample-data expectations

If detection is not what you want, force it:

```bash
bin/ai-ready install /path/to/repo \
  --project-type ios \
  --runtimes codex,claude,generic
```

## Runtime Selection

You can choose one runtime, several, or `all`.

### Single Runtime

```bash
--runtimes codex
--runtimes claude
--runtimes copilot
--runtimes cursor
--runtimes generic
```

### Multi Runtime

```bash
--runtimes codex,claude
--runtimes codex,claude,generic
--runtimes codex,claude,copilot,generic
```

### All

```bash
--runtimes all
```

This expands to:

- `codex`
- `claude`
- `copilot`
- `cursor`
- `generic`

### Universal Generic Mode

`generic` installs `AI-READY.md`.

Use it when:

- the chosen AI has no repo-native adapter format
- the team wants one common handoff file across tools
- the repo should remain usable even if the runtime choice changes later

This is the safest default when you need the setup to survive tool churn.

## Commands

### 1. Audit

Read-only mode.

Use it when:

- the repo has no AI setup and you want to inspect first
- the repo already has AI files and you need a gap analysis
- you want a report for another developer before changing anything

Example:

```bash
bin/ai-ready audit /path/to/repo
```

Optional report file:

```bash
bin/ai-ready audit /path/to/repo \
  --report-path /tmp/ai-ready-audit.md
```

### 2. Install

Use it when the repo does not yet have a canonical AI layer.

Example:

```bash
bin/ai-ready install /path/to/repo \
  --runtimes codex,claude,generic \
  --project-type android
```

What it does:

- detects or accepts the project type
- creates `.ai/`
- writes the canonical docs and playbooks
- writes only the runtime adapters you selected
- optionally installs Git hooks
- optionally applies local git user name and email

### 3. Standardize

Use it when the repo already has AI-related files and you want to normalize it.

Example:

```bash
bin/ai-ready standardize /path/to/repo \
  --runtimes codex,claude,copilot,generic \
  --yes
```

What it does:

- audits what is already there
- backs up known AI files under `.ai/archive/standardize-*`
- rewrites the canonical `.ai/` layer
- rewrites the selected runtime adapters
- removes unselected adapters from the active root
- preserves a backup before overwrite

## Generated Files In The Target Repo

### Canonical

```text
.ai/
|-- README.md
|-- context.md
|-- context/
|   |-- architecture.md
|   |-- dependencies.md
|   |-- features.md
|   |-- repository.md
|   `-- recent-changes.md
|-- decision-framework.md
|-- rules/
|-- agents/
|-- skills/
|-- features/
`-- archive/
```

### Optional Runtime Adapters

- `AGENTS.md`
- `CLAUDE.md`
- `.github/copilot-instructions.md`
- `.github/instructions/`
- `.cursor/rules/ai-ready.mdc`
- `AI-READY.md`

## Operating Modes For Teams

### Mode A: Read First, Then Apply

Use this when a teammate wants to review the plan before any files are generated.

1. run `audit`
2. share the report plus this manual
3. choose one or more runtimes
4. run `install` or `standardize`

### Mode B: Execute Directly

Use this when the repo clearly needs an AI-Ready layer now.

1. choose runtimes
2. run `install` or `standardize`
3. commit the generated layer
4. let the teammate refine the placeholders with real repo knowledge

## Android Example

```bash
bin/ai-ready install ~/Developer/android-repo \
  --runtimes codex,claude,generic \
  --project-type android \
  --git-name "Michel Marques" \
  --git-email "marques.jm@icloud.com" \
  --apply-git-config
```

## iOS Example

```bash
bin/ai-ready install ~/Developer/ios-repo \
  --runtimes codex,claude,generic \
  --project-type ios \
  --git-name "Michel Marques" \
  --git-email "marques.jm@icloud.com" \
  --apply-git-config
```

## Generic Any-AI Example

```bash
bin/ai-ready install ~/Developer/tech-repo \
  --runtimes generic \
  --project-type generic
```

That gives the repository:

- a canonical `.ai/` layer
- `AI-READY.md` as a universal adapter
- documented Git workflow
- a handoff path that does not depend on a single AI vendor

## What To Ask The AI After Install

If the repository still has placeholder text under `.ai/context/`, that is expected. The next step is to make the AI read the repo and replace placeholders with real project knowledge.

### Codex Prompt

```text
Read AGENTS.md and the canonical .ai layer. Audit the repository, map the real architecture and dependencies, replace the placeholder AI context with repo-specific information, and only then propose the smallest safe implementation plan.
```

### Claude Code Prompt

```text
Read CLAUDE.md and the canonical .ai layer. Summarize the real module layout, identify missing context, update the AI-Ready docs so they match the repository, and then suggest the next safe changes.
```

### Generic AI Prompt

```text
Read AI-READY.md and .ai/README.md. Use the repository itself to infer the true architecture, dependencies, and workflows, update the placeholder AI context files, and propose a minimal-risk plan before making code changes.
```

These prompts are important when the repository had no AI system before bootstrap. The tool creates the frame; the AI still needs to ground that frame in the real codebase.

## Git Governance

This tool mirrors the same discipline used in `ai-workspace`.

### Installed Rules

- block direct commits on `main`, `develop`, and `master`
- encourage short-lived `feature/*`, `fix/*`, `chore/*`, `docs/*`, and `refactor/*` branches
- use commit format `[branch_name] type: "title"`
- do not add AI co-author trailers

### Hook Installation

If the target repo is a git repo and you do not pass `--no-git-hook`, the tool:

- creates `.githooks/pre-commit`
- sets `core.hooksPath=.githooks`

### Git Identity

The tool records detected `user.name` and `user.email` in `.ai/context/repository.md`.

If you want the tool to apply them locally:

```bash
bin/ai-ready install /path/to/repo \
  --runtimes generic \
  --git-name "Michel Marques" \
  --git-email "marques.jm@icloud.com" \
  --apply-git-config
```

## CI

GitHub Actions validates:

- shell syntax for `bin/ai-ready`
- Android fresh-install smoke tests
- iOS fresh-install smoke tests
- standardize-mode smoke tests including `AI-READY.md`

## Practical Recommendation

If you do not know yet which AI will own the repo, install at least:

```bash
--runtimes generic
```

If the team already knows it will use Codex or Claude, prefer:

```bash
--runtimes codex,claude,generic
```

That gives you:

- first-class adapters for the chosen tools
- a universal fallback adapter for any other AI
- a single `.ai/` canon that survives runtime changes
