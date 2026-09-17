---
name: t-build
description: Use only when the user explicitly invokes t-build to implement a ready specification under docs/t-specs/<slug>/; dispatches one implementer worker per task file, verifies each outcome, runs one final independent review, and commits without publishing or advancing phases.
---

# t-build

User-invoked only. Do not start it after a spec is written, and do not invoke
another phase when it finishes. Standalone: works without any other skill
installed. Host syntax, tools, delegation, and model names vary; use what is
actually available and never hardcode a tool, model, or command.

## Spec layout

```text
docs/t-specs/<slug>/
├── spec.md      Progress, Tasks table (linked to task files), Rulings,
│                then the design: requirements, decisions, contracts,
│                edge cases, acceptance criteria, verification
└── tasks/Tn.md  one self-contained worker brief per task
```

Workers read only their task file. You read `spec.md` in full; from task
files read only `Context`, `Dependencies`, `Files`, and `Interfaces` for
scheduling and conflict checks, and open a task file in full only when
gating it or handling a `blocked` report. `Steps` and code are worker
material; keeping them out of your context is the point of the split.
The build edits only `Progress`, `Tasks`, and `Rulings` in `spec.md`; the
design sections and task files are inputs.

## Roles

- **Orchestrator** (you): selects the spec, understands the design,
  dispatches workers, gates each result, keeps `Progress` and `Tasks`
  accurate, runs the final review. You do not write application code except where
  **Direct work** allows.
- **Implementer worker**: a fresh isolated worker per assignment. It
  implements exactly what its task file says, runs the listed checks,
  commits when told to, and reports. It makes no design decisions and
  spawns no workers.
- **Task reviewer**: a fresh worker after each task, sized to the diff,
  checking the task's commit against its task file. Reports `must-fix`
  findings and `notes`; does not edit code.
- **Final reviewer**: one fresh worker at the end, on the strongest
  available model, reviewing the whole diff against the design.

## Step 1 — Select the spec

1. If the invocation names a slug or path, resolve it to
   `docs/t-specs/<slug>/spec.md` and validate that it exists.
2. Otherwise list every `docs/t-specs/*/spec.md` with slug, `State`, and
   `Active`. None → report and stop. One → present it and ask to confirm.
   Several → ask the user to pick. Use the host's normal question
   mechanism; never guess.
3. Read `spec.md` in full, the scheduling headers of every `tasks/Tn.md`, repository instructions, relevant code, and current Git
   state. Do not mutate anything yet.

## Step 2 — Check readiness

State handling:

- `Draft` → not approved by the user. Report that and stop; never mark it
  `Ready` yourself.
- `Ready` with `Base commit: Not captured` → fresh build.
- `Ready` with a base commit → resume. Compare the task table with code
  and Git history before changing anything; trust commits over notes. For
  any task row `blocked`, recheck the recorded blocker in resume notes;
  resolved → continue, otherwise report and stop.
- `Complete` → validate the claims; reopen invalidated work, do not repeat
  completed work.

Reader contract (report the gap and stop if unmet):

- `Progress` has `State`, `Base commit`, `Active`, `Resume notes`; a
  `Rulings` section exists; the `Tasks` table has `ID`, `Title`,
  `Depends on`, `Status`, `Commit`.
- Every table row links to exactly one `tasks/<ID>.md`, and every task
  file has a row.
- Each task file has Context, Constraints and contracts, Dependencies,
  Files, Interfaces, Steps, Tests, Acceptance, Verification.
- `Depends on` controls order; tasks run one at a time.

Decision authority:

- Agreed decisions and binding agent-chosen choices in the design are
  binding.
- Flexible implementation defaults may change when contracts and
  acceptance still hold; record the change under `Rulings`.
- Verify observed facts that may have drifted; check high-impact
  unverified assumptions before dependent work.
- An unresolved high-impact fact or choice makes the spec not ready even
  if it says `Ready`. Report the inconsistency and stop.
- Substantive product, contract, architecture, or safety choices need the
  user. Routine reversible details are yours; record material ones.

Before dispatching, scan the task headers once for conflicts: two tasks
that modify the same file, an interface consumed that no task produces, a
dependency on a task that does not exist. Rule on each with the design as
the authority, record it under `Rulings`, and continue. Stop only if every
path forward is a guess.

## Step 3 — Start the build

1. Work on a branch, never directly on the default branch. On a fresh
   build: if the current branch is the default branch, create and switch
   to `t/<slug>` and say so; if the user is already on another branch,
   use it. On resume, stay on the branch recorded in `Resume notes`. If
   `t/<slug>` exists but is not checked out, ask before switching. Record
   the branch name in `Resume notes`.
2. Record the current commit as `Base commit` once, the first time the
   build starts. If Git cannot provide one, report the blocker; never
   fabricate it.
3. Note any pre-existing dirty or staged changes. They are the user's;
   preserve them and keep them out of task commits. Never reset, stash,
   discard, or overwrite work outside the agreed scope.
4. Check the host once for worker dispatch and per-worker model
   selection. State the result in one line, for example
   `Workers: yes, model selection: yes → implementers on <tier>, reviewer
   on <tier>.` If model selection is unavailable, say so
   here and do not repeat it.

## Step 4 — Execute tasks

### Dispatch

Delegate every task to an implementer worker by default. Do not evade this
by calling a task "tiny". Several small, same-shaped tasks may share one
worker; list all their task files in that dispatch.

Model choice, when the host allows it:

- **Always set the worker's model explicitly.** An omitted model inherits
  the orchestrator's model, usually the most capable and most expensive.
  Omitting it is a defect.
- Task files with complete steps and code → cheapest capable tier.
- Multi-file integration, or steps that describe without showing → mid
  tier.
- Escalate one tier only after a worker reports `blocked` for capability
  rather than context.
- Task reviewer → mid tier; cheapest tier for a small mechanical diff.
- Final reviewer → most capable available.

Ordering:

- One task at a time, in dependency order. Never run two implementers at
  once; they conflict on the working tree and the index.
- A task starts only when every dependency is `done` and gated.
- One fresh worker per assignment; reuse that worker for its own fixes.
- Note the current HEAD before dispatching; the task reviewer needs it as
  the start of the task's commit range.
- Set the dispatched task to `active` and name it under `Active`.

### Dispatch message

The worker gets its task file path and nothing else from the spec. Add
only:

- interfaces from earlier tasks whose final names differ from what the
  task file says;
- rulings that affect this task;
- the worker rules and report format below.

Do not paste session history, other tasks, or `spec.md`. Tell the worker:
read the task file first; implement exactly what it says; do not redesign;
if it is wrong or incomplete, report `needs-context` or `blocked` instead of
guessing; do not spawn workers; do not touch `docs/t-specs/`.

### Worker rules

The worker must:

- change only the paths under its task's `Files` and leave unrelated work
  untouched;
- follow `Steps` in order and write every test listed under `Tests`;
- run the focused test while iterating and the task's `Verification`
  commands once before finishing;
- read its own diff once: complete against the task file, no leftover
  debug output, names match `Interfaces`;
- when committing: inspect staged and unstaged changes, stage only its
  `Files` by name, never a catch-all; if unrelated changes are already
  staged, use a path-scoped commit method or report `blocked`;
- write a Conventional Commit `<type>(<scope>): <description>` with a type
  such as `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `build`,
  `ci`, `perf`, `style`, and no AI attribution, `Co-Authored-By` trailer,
  generated-by footer, or robot emoji;
- report `Status: done | needs-context | blocked`, then commit SHA and
  subject, files changed, commands run with their output, and concerns.
  A check counts only if its command and output are in the report; a
  claim without output is not evidence.

### Commit ownership

- The worker commits its own task.
- Pending `spec.md` progress edits ride in one final `docs` commit, or in
  a `docs` commit at a natural checkpoint; no bookkeeping commit per task.

### Gate

After each implementer report:

1. `done` → open the task file; confirm the commit touches only its
   `Files`, every listed test exists, and each verification command's
   output is in the report. Do not rerun passing checks by ritual. Then
   dispatch the task review below.
2. `needs-context` → supply it and resume the same worker.
3. `blocked` → decide: missing context (resume with context), capability
   (fresh worker one tier up), task too large (split; record under
   `Rulings`), task file wrong (rule on it, record, redispatch). Never
   retry unchanged.
4. Concerns about correctness or scope → resolve before the review.

### Task review

One fresh reviewer per task, after the gate passes. Give it: the task
file path, the task's commit range (the SHA before the implementer
started and its commit), and the instruction to compute the diff itself.
Do not paste the diff. The reviewer reads the task file and the diff,
may read surrounding code, and reports:

- `must-fix`: a requirement, contract, or edge case in the task file not
  met; a listed test missing or asserting nothing; a defect in the change;
  a change outside `Files`.
- `note`: anything else worth knowing (naming, structure, a risk in
  unchanged code). Notes never block.

Handling:

1. No `must-fix` → record the commit SHA, set the row to `done`, append
   notes under `Rulings` as `Tn note: …`, dispatch the next task.
2. `must-fix` → resume the same implementer with the findings verbatim.
   It fixes, reruns the covering tests, commits, and reports with output.
   Resume the same reviewer: "recheck these findings only, and flag new
   breakage in the fix." Repeat at most twice.
3. Still open after two rounds → you decide: rule on the finding under
   `Rulings` with the task file as the authority, or dispatch a fresh
   implementer one tier up with the task file and the open findings.
4. A finding that contradicts the task file is yours to rule on, not the
   reviewer's or the implementer's; record the ruling.

Keep `Active` and `Resume notes` current. If a ruling invalidates a `done`
task, set it and every dependent `done` task to `reopened` with the reason.

### Direct work

Implement directly only when: the host has no worker capability or denied
it (after checking); the user explicitly asked; or the change is progress
bookkeeping in `spec.md`. Apply the worker rules to yourself. Never bypass
permissions.

## Step 5 — Final gate and review

1. Run the spec's `Verification` and confirm every acceptance criterion.
2. Dispatch the final reviewer with the `spec.md` path, `Base commit`, and
   the instruction to compute the diff to HEAD itself, plus the `Tn note`
   entries from `Rulings` to triage. It checks the whole change against
   the design: acceptance criteria, contracts, edge cases, and integration
   between tasks. It reports `must-fix` and `note` as above, not taste.
3. Group `must-fix` findings into one fix pass (resume the implementer
   whose task is affected, or a fresh worker), rerun the affected checks,
   and resume the reviewer to recheck only the fix. Repeat only if the fix
   introduced a new defect.
4. If no worker can act as an independent reviewer, say so, do a separate
   self-review of the full diff, and label the handoff as self-reviewed.
   Never claim an independent review that did not happen.
5. A mandatory check that cannot run blocks completion until the user
   accepts the limitation. Keep unavailable mandatory checks separate from
   optional checks you chose not to run.

When everything passes: set `Active: None`, `State: Complete`, put final
evidence and known limitations in `Resume notes`, and commit `spec.md`.
On interruption or a blocker, leave `State: Ready`, mark the affected task
row `blocked` or `active`, and write what happened in `Resume notes`.

## Boundaries

- Do not merge the build branch, push, open a pull request, publish,
  deploy, archive, or install globally unless the user separately
  authorizes it.
- Do not initialize or replace a repository without authority.
- Do not use broad staging or destructive Git commands.
- Do not edit the design sections of `spec.md` or any task file during
  the build; a needed change is a ruling or a request to the user.
- Do not persist worker reports as files unless the host's dispatch
  mechanism requires it; then keep them out of commits.

## Finish

Report: state, branch, commits and files, checks run with outcomes,
review evidence (independent or self), unresolved concerns, and the final
task table. Offer the next step as a choice for the user: merge locally,
push and open a pull request, or leave the branch as is. Stop. Any
follow-up, including another skill, is the user's call.
