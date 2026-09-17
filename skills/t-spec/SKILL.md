---
name: t-spec
description: Use only when the user explicitly invokes t-spec to write or update an implementation specification under docs/t-specs/<slug>/ as a design document with progress plus one self-contained brief per task, and to mark it Ready only after the user approves it.
---

# t-spec

User-invoked only. Do not run it as an automatic follow-up to brainstorming,
and do not invoke a build phase when it finishes. It is standalone: use the
bundled templates in `assets/` and the worked example in `assets/example/`,
but do not depend on another skill, a repository-root file, a particular
tool, or a particular model.

## Purpose

Produce a specification a build phase can execute without rereading the
brainstorm and without making design decisions. Assume each implementer
sees only its own task file, has zero context for the codebase, and will do
exactly what is written.

## Output layout

```text
docs/t-specs/<slug>/
├── spec.md      progress, task table, and the design: why and what
└── tasks/
    ├── T1.md    self-contained worker brief
    └── T2.md
```

Write only inside `docs/t-specs/<slug>/`. `handoff.md` may already exist
there from brainstorming; read it, do not modify it.

## Inputs

1. Read the request, repository instructions, relevant code and tests, and
   any existing files at the slug.
2. Use `docs/t-specs/<slug>/handoff.md` or a handoff in the conversation as
   input. Preserve its distinctions between agreed decisions, agent-chosen
   defaults, observed facts, unverified assumptions, and unresolved
   blockers.
3. In a fresh conversation, do not pretend prior decisions exist. If a
   missing decision is needed, ask; otherwise continue from the request and
   repository evidence.
4. If no slug was supplied, use the handoff's slug or state a short stable
   slug derived from the feature.

Capture every agreed decision. There is no length limit; do not drop
requirements for brevity.

## spec.md

Sections in this order. Omit a design section only when it has no
content; a section with one item is one line, do not pad.

1. `Progress`
   ```markdown
   - **State:** Draft | Ready | Complete
   - **Base commit:** Not captured
   - **Active:** None
   - **Resume notes:** [short: current evidence, next step, or blocker]
   ```
2. `Tasks` — the only progress record. Every row links to its task file so
   a reviewer can open one task from the spec:
   ```markdown
   | ID | Title | Depends on | Status | Commit |
   |----|-------|------------|--------|--------|
   | [T1](tasks/T1.md) | [title] | — | pending | |
   | [T2](tasks/T2.md) | [title] | T1 | pending | |
   ```
   Task status values: `pending`, `active`, `done`, `blocked`, `reopened`.
   IDs are stable across edits.
3. `Rulings` — decisions the build makes that the design did not cover.
   Write `None yet.`
4. `Goal and context`
5. `Requirements` — `Binding requirements` (including project-wide
   constraints with exact values) and `Implementation suggestions`.
6. `Non-goals`
7. `Architecture and decisions` — components, data flow, boundaries. Label
   agreed decisions, agent-chosen defaults (binding design choice or
   flexible detail), observed facts with file references, unverified
   assumptions, unresolved blockers. Omit empty labels.
8. `Contracts` — externally visible behavior, data shapes, interfaces,
   formats, error semantics.
9. `Edge cases` — each one should map to a named test in some task.
10. `Acceptance criteria` — observable conditions for the whole feature.
11. `Verification` — the broad checks run at the end of the build.

Sections 1–3 are the build's to update; sections 4–11 are the design and
contain no task steps or code. The build never edits the design.

State meanings: `Draft` is being written or awaiting the user's approval
(resume notes say which); `Ready` is user-approved and is the state the
build runs in; `Complete` is set by the build. Execution progress lives in
the task table, not in the state.

## tasks/Tn.md

A task is the smallest unit with its own test cycle and its own commit.
Fold setup, configuration, and documentation into the task whose
deliverable needs them. Split only where one task could be accepted while
its neighbor is rejected. Order tasks so each depends only on earlier
ones; they are built one at a time.

Every task file stands alone. Sections in this order:

```markdown
# Tn — [title]

## Context
[One paragraph: what this task delivers and where it fits.]

## Constraints and contracts
[Copied verbatim from spec.md: only the requirements, contracts, and edge
cases this task must honor. Duplication is intended; the worker does not
read spec.md.]

## Dependencies
[Task IDs with the exact names and signatures they produce, or None.]

## Files
- Create: `exact/path`
- Modify: `exact/path:lines`
- Test: `exact/test/path`

## Interfaces
- Consumes: [exact names and signatures]
- Produces: [exact names, parameters, return types later tasks rely on]

## Steps
1. [Concrete action; include code when the exact shape matters]

## Tests
- `test_name` — [behavior asserted]; run: `[command]`

## Acceptance
- [Observable task outcome]

## Verification
- `[command]` → [expected result]
```

Steps describe what to do and how, in execution order. Show code for any
step where the exact shape matters: signatures, data structures, tricky
logic. Non-code tasks list the equivalent check under `Tests`.

No placeholders. These are specification failures: `TBD`, `TODO`,
"similar to T2" (repeat it), "write tests for the above" without cases,
"add validation" or "handle edge cases" without saying which, references to
names not defined in any task.

## Updating an existing spec

Preserve `Progress` and the task table. When a decision changes:

1. Update the design sections of `spec.md`.
2. Regenerate every task file the change affects.
3. Set those tasks, and any `done` task that depended on them, to
   `reopened` with a one-line reason in resume notes.
4. Set `State` back to `Draft` and run the review gate again.

Do not reset unaffected tasks.

## Completeness pass

Before stopping, check once across all files and fix inline:

1. **Coverage:** every requirement, decision, contract, and edge case maps
   to at least one task; every acceptance criterion is verified somewhere.
2. **Placeholders:** search all files for the patterns above.
3. **Consistency:** names, signatures, and paths a task consumes match what
   its dependency produces; copied constraints match `spec.md`; the task
   table matches the task files one to one and every row links to its
   file.
4. **Completeness:** an unresolved high-impact fact or choice keeps the
   state at `Draft`; say what is missing. Do not invent an answer. Routine
   reversible defaults and non-blocking unknowns do not block.

## Review gate

`Ready` is set by the user's approval, never by this skill on its own.

1. When the pass is clean, keep `State: Draft` and write
   `Awaiting user review` in resume notes.
2. Report the directory, the task table with links, and any agent-chosen
   defaults worth a second look. Ask the user to review `spec.md` and the
   task files and reply with changes or approval.
3. Apply requested changes, rerun the completeness pass, and ask again.
4. On explicit approval, set `State: Ready`, clear the resume note, and
   suggest that the user invoke `t-build`. Do not invoke it. A request for
   speed does not skip this gate; it only shortens the dialogue before it.
