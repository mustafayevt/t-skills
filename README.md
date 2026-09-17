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
| [`t-brainstorm`](skills/t-brainstorm/SKILL.md) | Explores the codebase, sizes the work, asks one question at a time, compares 2–3 approaches, and presents the design section by section for approval. | A design handoff: in chat for small work, `docs/t-specs/<slug>/handoff.md` for full work. |
| [`t-spec`](skills/t-spec/SKILL.md) | Converts the agreed design into a specification detailed enough that an implementer needs no design judgment. | `docs/t-specs/<slug>/`: `spec.md` (progress, linked task table, design) and `tasks/Tn.md` (one self-contained worker brief per task), marked `Ready` only after you approve it. |
| [`t-build`](skills/t-build/SKILL.md) | Dispatches one implementer worker per task in dependency order, has a fresh reviewer check each task against its task file, runs one final review of the whole diff, and commits. | Verified implementation, accurate spec progress, and Conventional Commits. |

## Why this workflow

Coding agents are most reliable when decisions, requirements, and execution do
not collapse into one opaque step. `t-skills` keeps those responsibilities
separate while preserving the information needed to move between them.

- **Deliberate phase boundaries.** No skill silently starts another phase.
- **Durable shared state.** `spec.md` carries progress and the design;
  each task is its own file that only one worker reads. The build updates
  progress but never edits the design.
- **You approve the spec.** `t-spec` stops in `Draft`; it becomes `Ready`
  only when you say so.
- **Portable behavior.** The skills describe outcomes and responsibilities
  without requiring a specific model, orchestration runtime, or tool API.
- **Independent installation.** Every skill is self-contained and can be
  installed or used separately.
- **Controlled execution.** `t-build` works on a branch, preserves existing
  work, scopes changes, verifies outcomes, reviews the final result, and
  does not merge or publish without explicit authorization.
- **Cheap workers, scoped reviews.** `t-build` gives each task to a fresh
  implementer worker that reads only its task file, on an explicitly chosen
  small model when the host allows it. A fresh reviewer checks each task's
  commit against its task file and reports `must-fix` findings and notes;
  fixes go back to the same implementer for at most two rounds. One final
  reviewer on the strongest model checks the whole diff against the design.
  Reviewers compute their own diffs, so large changes never pass through
  the orchestrator's context.

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

The skill reads the relevant code before asking anything the code can
answer, states whether the work is small, full, or too big for one spec,
asks one question per message, proposes 2–3 approaches before recommending
one, and presents the design in sections for approval. It ends with a
handoff: in chat for small work, or written to
`docs/t-specs/<slug>/handoff.md` for full work. It does not edit application
files or create a specification.

### 2. Write the specification

Invoke `t-spec` with the slug. It reads `docs/t-specs/<slug>/handoff.md`
when present, or a handoff supplied in the conversation:

```text
Use t-spec to create the offline-sync specification.
```

The result is a directory:

```text
docs/t-specs/<slug>/
├── spec.md      progress (state, base commit, active, resume notes),
│                task table linking to each task file, rulings, then the
│                design: goal, requirements, non-goals, decisions,
│                contracts, edge cases, acceptance criteria, verification
└── tasks/
    ├── T1.md    context, copied constraints, dependencies, files,
    └── T2.md    interfaces, ordered steps with code, named tests,
                 acceptance, verification
```

`spec.md` says what and why; each task file says how, with code wherever
the exact shape matters, and stands alone so a worker never needs the rest.
The orchestrator reads `spec.md` and only the scheduling headers of task
files, so its context does not grow with the size of the implementation
detail. Placeholders such as `TBD` are treated as failures.

`t-spec` stops in `Draft` and asks you to read the spec and task files. It
marks the spec `Ready` only on your explicit approval. A worked
example lives at
[`skills/t-spec/assets/example/`](skills/t-spec/assets/example/).

### 3. Build the specification

Invoke `t-build` and identify the specification to execute:

```text
Use t-build for offline-sync.
```

`t-build` validates readiness, moves to a `t/<slug>` branch when you are on
the default branch (or stays on your current feature branch), captures the
starting Git state, and reports once what the host supports (workers, model
selection).
It then dispatches a fresh implementer worker per task file, one task at
a time in dependency order, sets the worker's model explicitly when the
host allows it, gates each result against the task's files and evidence,
dispatches a fresh reviewer per task, records progress in the spec's task
table, and finishes with one review of the whole diff against the design.
A check counts only when its command and output appear in the worker's
report. Each worker commits its own task on the build branch. It creates
scoped Conventional Commits but does not merge, push, publish, deploy, or
open a pull request; at the end it offers merge, pull request, or
leave-as-is as your choice.

## Specification contract

The specification directory is the durable interface between planning and
execution.

- `spec.md` opens with `Progress` (state, base commit, active tasks,
  resume notes), a `Tasks` table with stable IDs linked to their files,
  dependencies, status (`pending`, `active`, `done`, `blocked`,
  `reopened`), and commit SHAs, and `Rulings` made during the build. The
  design sections follow. `t-spec` writes all of it; the build updates only
  the first three sections.
- States: `Draft` (being written or awaiting your approval), `Ready`
  (approved; the build runs and resumes in this state), `Complete`.
  Execution progress lives in the task table.
- `tasks/Tn.md` is one self-contained brief per task with context, copied
  constraints, dependencies, files, interfaces consumed and produced,
  ordered steps, named tests, acceptance, and verification. Dependencies
  control ordering; tasks are built one at a time.

Templates live in [`skills/t-spec/assets/`](skills/t-spec/assets/) with a
filled example under `example/`. `t-spec` owns the authoring contract,
while `t-build` carries enough matching reader guidance to function when
installed alone.

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
│       ├── spec-template.md
│       ├── task-template.md
│       └── example/
│           ├── spec.md
│           └── tasks/
│               ├── T1.md
│               └── T2.md
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
