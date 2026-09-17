---
name: t-build
description: Use only when the user explicitly invokes t-build to implement an implementation-ready spec, coordinating scoped outcome-sized tasks, verification, review, and commits without publishing or silently advancing phases.
---

# t-build

This skill is user-invoked only. Do not start it automatically after a spec or
invoke another phase when it finishes. It is standalone and must work without
any other skill installed. Host syntax, tools, delegation support, and model
choices vary; use only capabilities actually available and never hardcode a
tool name, model name, or command into the portable workflow.

## Start safely

1. Before reading a selected specification completely or mutating progress or
   code, determine the selection. An explicit path or slug supplied in the
   t-build invocation is an override; validate that it resolves to a spec
   under `docs/t-specs/<slug>/spec.md`. Otherwise enumerate
   `docs/t-specs/*/spec.md`, showing each slug plus its `Progress` State and
   Active task. If none exist, report that and stop. If multiple exist, ask the
   user to select one; if exactly one exists, still present it and ask for
   confirmation. Use the host's ordinary interactive question mechanism, not a
   hardcoded UI tool, and never guess a selection.
2. After selection, read the selected specification completely, the repository's
   applicable instructions, relevant code, and current Git state. It must be
   implementation-ready: `Ready` means material decisions are resolved;
   `Building` means resume the recorded work; `Draft` needs specification work.
   For `Blocked`, recheck whether the recorded blocker is now resolved and
   continue only if it is; otherwise report it and stop. For `Complete`,
   validate the claims and reopen invalidated work instead of duplicating it.
   On resume, compare the spec's claims with current code and Git history
   before changing anything. Preserve decision status from the specification:
   agreed decisions and binding agent-chosen design choices are binding;
   flexible implementation defaults may change when contracts and acceptance
   remain satisfied; observed facts should be verified when they may have
   drifted. Check any high-impact unverified assumption before dependent work.
3. Capture the starting Git commit once in the spec's Progress section when the
   build begins. Preserve that baseline and all pre-existing dirty changes for
   review; do not reset, stash, discard, or overwrite work outside the agreed
   scope. If Git cannot record a baseline, disclose the blocker rather than
   fabricating one.

The reader contract is stable: `Progress` contains `State`, `Active task`,
`Resume notes`, and `Base commit`; the top `Tasks` checklist uses stable `T1`,
`T2`, and so on; every checklist item has exactly one matching `### Tn — ...`
section at the bottom with scope, dependencies, acceptance, and verification.
Dependencies control ordering; task IDs do not imply parallelism.
If an unresolved high-impact fact or choice could invalidate the design, the
specification is not implementation-ready even if its recorded state says
`Ready`; report the inconsistency and stop. A substantive product, contract,
architecture, or safety choice requires a user decision or discretion the user
explicitly delegated. Choose routine reversible implementation details
autonomously and record material choices in the task or resume notes.

Progress transitions are explicit: before work starts, set `State: Building`
and the current `Active task`; update that task and the notes as work advances;
when a blocker is resolved, clear or replace the stale blocker note. If a spec
change invalidates evidence, reopen the affected completed tasks and every
dependent completed task whose evidence relied on it. When all acceptance
criteria and required checks are verified, clear `Active task`, set
`State: Complete`, and put concise final evidence and limitations in `Resume
notes`.

## Task execution

After checking the host's delegation capabilities, delegate implementation and
focused verification to an implementation-capable isolated worker by default.
The frontier orchestrator owns full-spec understanding, assignments, context
questions, acceptance, and progress; the worker owns application edits, checks,
self-review, and the task commit. Batch tiny related changes into one
assignment so spawning is not per checkbox; do not classify an entire task as
"tiny" to evade this default. Start sequentially, delegate a dependent task
only after its prerequisites complete, use a fresh worker for each distinct
assignment, and reuse that worker for fixes. There is no independent-tasks-only
restriction when the dependency order is clear.

Before each assignment, make one concise execution statement naming delegation
or the concrete reason for a direct fallback and the known or inherited model
selection. Direct implementation is limited to incidental progress bookkeeping,
an explicit user request, or capable isolation being unavailable or denied
after it was checked. Do not bypass permissions. Delegation and model selection
are separate: if per-worker selection is unavailable, still delegate with the
available/default model and disclose that fact; never pretend a cheaper model
was selected. Prefer a smaller capable or mid-tier implementation model when
selectable, and escalate to a frontier capability for difficult reasoning only
after distinguishing missing context from insufficient capability. Keep a
strong fresh reviewer for the final review. Do not persist worker logs or
reports, or ask blanket repeated questions. A delegation report requires an
actual delegation invocation.

For every delegated task, provide an ephemeral, scoped brief containing the
goal, copied relevant requirements and global constraints, contracts,
dependencies, owned paths, acceptance, verification, and commit/report rules.
Workers may inspect relevant code but must not spawn more workers. They should
report needs-context or blocked rather than guess over material unknowns.

The implementer must:

- make changes only within the task scope and preserve unrelated user work;
- run focused, proportional checks: logic changes need relevant tests or
  checks, regression fixes need the original symptom covered, and UI changes
  need the applicable build plus visual or interaction verification;
- self-review the actual diff;
- inspect both the staged and unstaged diff before staging; stage explicit
  task-owned paths only, never a broad catch-all. If unrelated changes are
  already staged, use a task-scoped commit method that preserves them, or stop
  and report that ownership cannot be separated. Do not casually unstage a
  user's changes;
- create a Conventional Commit using `<type>(<optional scope>): <description>`
  with a type such as `feat`, `fix`, `refactor`, `test`, `docs`, `chore`,
  `build`, `ci`, `perf`, or `style`; include no AI attribution,
  `Co-Authored-By` trailer, generated-by footer, or robot emoji; and
- report `Status: done`, `Status: needs-context`, or `Status: blocked`, along
  with the commit SHA when done, subject, files, commands and outcomes, and
  concerns. A needs-context or blocked result must not be presented as done.

Workers must exclude the specification from staging unless the task explicitly
delegates that exact progress edit. The orchestrator alone updates spec
progress. Pending progress can be included in the next owned implementation
commit or one final documentation commit; do not create a bookkeeping commit
for every checkbox.

## Gates and review

After each task, cheaply validate the actual commit range and files against the
task scope, acceptance evidence, and reported concerns. Do not rerun unchanged
passing checks or reread everything by ritual. Review risky foundational work
early when dependent work would otherwise multiply a mistake.

At the end, run the broad relevant checks, verify every acceptance criterion,
and perform one fresh independent review for non-trivial work against the
selected specification, implementation changes, and relevant code. The reviewer should
report concrete defects or unmet requirements, not taste. Group fixes, rerun
affected checks, and repeat only as needed. If no independent reviewer is
available, say so plainly, perform a separate self-review, and provide a
qualified handoff; never claim independent review.

If a mandatory check is unavailable, the work is blocked until the user
accepts that limitation; never mark the task or overall spec complete. Clearly
separate an unavailable mandatory check from an optional check that was not
run.

Update checkboxes only for verified outcomes. On interruption or blockage,
write short resume notes and leave the state accurate. Mark the overall work
`Complete` only after the final gate; distinguish unavailable mandatory checks
from optional limitations.

## Boundaries and finish

Do not push, open a pull request, publish, deploy, archive, or install
globally unless the user separately requests and authorizes that action. Do
not initialize or replace a user's project repository without authority. Do
not use broad staging or destructive Git commands.

Finish with the verified implementation status, commits and files, checks and
outcomes, review evidence, unresolved concerns, and accurate spec progress.
Stop there. Any suggested follow-up (including another skill) must be an
explicit user choice, not an automatic invocation.
