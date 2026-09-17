# t-skills

Portable Agent Skills for turning an idea into an implementation through three
deliberate, independently controlled phases:

```text
t-brainstorm  →  t-spec  →  t-build
     decide        document      implement
```

`t-skills` gives coding agents a disciplined workflow for exploring a change,
writing an implementation-ready specification, and executing it with explicit
progress, verification, review, and commits. Each phase is manually invoked,
works on its own, and leaves a clear handoff for the next phase.

## Skills

| Skill | Purpose | Output |
| --- | --- | --- |
| [`t-brainstorm`](skills/t-brainstorm/SKILL.md) | Investigates the repository, clarifies intent, explores meaningful alternatives, and resolves product and architecture decisions. | A concise, standalone decision handoff. |
| [`t-spec`](skills/t-spec/SKILL.md) | Converts agreed decisions and repository evidence into a complete implementation specification. | `docs/t-specs/<slug>/spec.md` with stable tasks, contracts, acceptance criteria, and verification. |
| [`t-build`](skills/t-build/SKILL.md) | Executes an implementation-ready specification with scoped work, evidence gates, review, and Conventional Commits. | Verified implementation, accurate spec progress, and task-owned commits. |

## Why this workflow

Coding agents are most reliable when decisions, requirements, and execution do
not collapse into one opaque step. `t-skills` keeps those responsibilities
separate while preserving the information needed to move between them.

- **Deliberate phase boundaries.** No skill silently starts another phase.
- **Durable shared state.** The specification records progress, decisions,
  contracts, tasks, acceptance criteria, and verification evidence.
- **Portable behavior.** The skills describe outcomes and responsibilities
  without requiring a specific model, orchestration runtime, or tool API.
- **Independent installation.** Every skill is self-contained and can be
  installed or used separately.
- **Controlled execution.** `t-build` preserves existing work, scopes changes,
  verifies outcomes, reviews the final result, and does not publish without
  explicit authorization.
- **Delegation with ownership.** When capable isolated workers are available,
  `t-build` delegates implementation while the orchestrator retains full-spec,
  acceptance, and progress ownership.

## Installation

### Interactive installation (recommended)

Run the standard command and choose the skills and supported coding agents you
want during the interactive setup:

```sh
npx skills@latest add mustafayevt/t-skills
```

Installation is project-local by default. Add `--global` (or `-g`) to make the
selected skills available across projects:

```sh
npx skills@latest add mustafayevt/t-skills --global
```

### Explicit installation

Install one skill:

```sh
npx skills@latest add mustafayevt/t-skills --skill t-build
```

Install every skill for Codex, Claude Code, OpenCode, and Pi:

```sh
npx skills@latest add mustafayevt/t-skills --skill '*' -a codex -a claude-code -a opencode -a pi
```

Install every skill for every agent supported by the installed CLI, without
prompts:

```sh
npx skills@latest add mustafayevt/t-skills --all
```

### Install from a local checkout

Use a local path when developing or testing changes to the skills:

```sh
git clone https://github.com/mustafayevt/t-skills.git
cd /path/to/target-project
npx skills add /absolute/path/to/t-skills
```

The available agent adapters and exact installation destinations are managed
by the [`skills` CLI](https://github.com/vercel-labs/skills).

## Usage

All three skills are user-invoked only. Invocation syntax depends on the host;
use the form it supports, such as `/t-brainstorm`, `$t-brainstorm`,
`t-brainstorm`, or an explicit natural-language request to use the skill.

### 1. Explore the change

Invoke `t-brainstorm` with the outcome you want:

```text
Use t-brainstorm to design offline synchronization for this application.
```

The skill inspects relevant context before asking questions, follows the
consequences of important decisions, and finishes with a standalone handoff.
It does not edit application files or create a specification.

### 2. Write the specification

Invoke `t-spec` with the brainstorming handoff when it is not already present
in the conversation:

```text
Use t-spec with this handoff to create the offline-sync specification.
```

The result is stored at `docs/t-specs/<slug>/spec.md`. A ready specification
contains stable task identifiers, binding requirements, architecture and
decision context, contracts, edge cases, acceptance criteria, verification,
and task-level execution details.

### 3. Build the specification

Invoke `t-build` and identify the specification to execute:

```text
Use t-build for docs/t-specs/offline-sync/spec.md.
```

`t-build` validates readiness, captures the starting Git state, executes tasks
in dependency order, verifies each outcome, performs a final review, and keeps
the specification's progress accurate. It creates scoped Conventional Commits
but does not push, publish, deploy, or open a pull request unless the user
separately authorizes that action.

## Specification contract

The specification is the durable interface between planning and execution. Its
top-level progress block records:

- the current state: `Draft`, `Ready`, `Building`, `Blocked`, or `Complete`;
- the active task and concise resume notes;
- the base commit captured when implementation begins; and
- a stable checklist whose task IDs match detailed definitions later in the
  document.

The complete template lives at
[`skills/t-spec/assets/spec-template.md`](skills/t-spec/assets/spec-template.md).
`t-spec` owns the authoring contract, while `t-build` carries enough matching
reader guidance to function when installed alone.

## Architecture and boundaries

The repository intentionally contains Markdown instructions and a specification
template rather than a custom orchestration runtime. The workflow does not
require sibling skills, fixed model names, persisted worker logs, automatic
phase chaining, worktrees, or a particular repository layout beyond the
documented specification path.

Host capabilities still matter. Manual invocation is an instruction-level
contract, and delegation, model selection, permissions, and available tools
depend on the active coding-agent environment. Each skill checks what is
actually available instead of claiming unsupported behavior.

## Repository structure

```text
AGENTS.md
CLAUDE.md
README.md
skills/
├── t-brainstorm/
│   └── SKILL.md
├── t-spec/
│   ├── SKILL.md
│   └── assets/
│       └── spec-template.md
└── t-build/
    └── SKILL.md
```

## Development

Keep each skill portable, self-contained, and explicit about its boundaries.
When behavior, commands, paths, defaults, compatibility, or public guarantees
change, update this README in the same change so the public documentation stays
aligned with the skill sources.

Contributions should use
[Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/).

## License

[MIT](LICENSE)
