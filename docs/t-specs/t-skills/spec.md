# T-Skills v1

> Historical record of the initial package build. The current behavior of
> each skill is defined by `skills/*/SKILL.md`, which has since been revised.

## Progress

- **State:** Complete
- **Active task:** None
- **Resume notes:** Package committed in `65ed52a` and review fixes in `4423142`; final docs handoff records cached CLI installs, validator-equivalent checks, and scoped independent review. No unresolved material concerns.
- **Base commit:** Not captured — new repository with no initial commit at build start.

## Tasks

- [x] T1 — Author the portable three-skill package.
- [x] T2 — Verify installation and specification compatibility.
- [x] T3 — Review behavior and complete the local handoff.

## Goal and context

Provide a lightweight, manually invoked workflow for discussing a feature, writing a detailed specification, and implementing it. Preserve thoughtful design and verification while reducing duplicated context, agent overhead, and ceremony. This is a published package, not a measured claim of savings over another workflow.

## Requirements

- Ship `t-brainstorm`, `t-spec`, and `t-build` in standard `skills/<name>/SKILL.md` directories with name and description frontmatter.
- Every user-facing phase requires explicit invocation. Completion may suggest another phase but must not start it.
- Brainstorming explores the goal, existing code, options, decisions, constraints, and acceptance without implementation or spec writes. Separate suggestions from agreed decisions.
- Spec writing produces a comprehensive standalone `docs/t-specs/<slug>/spec.md`. Preserve relevant agreed behavior and technical decisions without a length target. Distinguish requirements from implementation suggestions. Surface material missing decisions.
- Put progress and a stable task checklist at the top of the specification; task definitions and dependencies belong in the same document. No `status.md` or separate implementation plan.
- Build understands this format, reads the whole spec, reconciles recorded progress with Git/code, and updates task management without silently changing requirements.
- Delegate implementation and focused verification to an implementation-capable isolated worker by default after checking availability. Batch small related tasks so spawning is not per checkbox; do not evade the default by calling an entire task tiny. Fresh workers handle distinct assignments, the same worker handles fixes, and dependent tasks are delegated sequentially after prerequisites complete. Direct execution is limited to incidental progress bookkeeping, an explicit user request, or isolation that is unavailable or denied after checking it.
- The frontier orchestrator owns full-spec understanding, assignments, context questions, acceptance, and progress; workers own application edits, checks, self-review, and commits. Delegation and model selection are separate: prefer a selectable smaller capable or mid-tier model, use the available/default model when per-worker selection is unavailable and disclose that fact, and escalate to a frontier capability for difficult reasoning only after distinguishing missing context from insufficient capability. Each assignment has one concise execution statement naming delegation or the concrete fallback reason and known or inherited model selection; reported delegation requires an actual invocation. Keep a strong fresh final reviewer.
- Worker prompts are ephemeral and contain only task-relevant requirements, global constraints, interfaces, dependencies, boundaries, acceptance, and verification. Workers can inspect relevant files and request context; they do not spawn agents.
- Select model capability explicitly where supported; escalate difficult reasoning, honor user preferences and available models, and disclose unavailable selection rather than pretending a model was used.
- Each verified implementation outcome gets a Conventional Commit. Stage only owned changes. No AI attribution or Co-Authored-By trailers. The orchestrator owns progress updates and includes them in an explicitly scoped commit or final documentation commit without per-task bookkeeping commits.
- Use focused verification, author diff review, and a cheap orchestration gate per assignment. Broad relevant verification and full acceptance checks happen at the end. One independent final review is required for nontrivial work when available. Early review is justified for risky foundations.
- Do not repeat successful unchanged checks. Fix meaningful findings together and verify affected code. Never declare blocked mandatory checks or known correctness defects complete. Disclose lack of independent-review capability and distinguish a qualified self-review handoff.
- Installation uses the existing `skills` CLI and supports choosing individual skills and multiple supported harnesses. Document both local installation and remote installation from the published `mustafayevt/t-skills` repository.
- Each installed skill folder is self-contained. No dependencies on sibling installation paths or the repository root. Shared schema meaning must remain consistent without requiring another skill to be installed.

## Non-goals

No custom CLI, orchestration runtime, model router, persisted worker prompts/reports, event logs, blanket repeated questions, worktree requirements, automatic phase chaining, forced TDD ceremony, fixed task counts, or mandatory reviewer after every task. No debug/archive command in v1. No publication automation, remote repository creation, global installation, deployment, or unrelated workspace changes in this task.

## Architecture and decisions

The package is Markdown instructions and a spec template. Use original text and acknowledge inspiration; preserve notices if third-party text is actually incorporated. Ship an MIT license. Keep additional assets conditional on a concrete benefit. A template belongs inside `t-spec`; `t-build` carries the minimum matching reader/update contract so it can be installed independently.

## Contracts

Spec task IDs are stable `T1`, `T2`, etc. A task has scope, dependencies, acceptance, and verification. Top-level progress distinguishes draft, ready, building, blocked, and complete; tracks active work and short resume notes. Capture the original build baseline once and preserve it on resume. Account for preexisting changes rather than attributing the entire dirty worktree to this build.

Worker results contain status, commit SHA/subject, changed files, verification commands/outcomes, and concerns. Request missing context or report blockers instead of guessing. Workers exclude the progress document unless its ownership is explicitly delegated.

## Edge cases

- Multiple candidate specs: resolve the intended target rather than guessing.
- Material ambiguity: surface it before dependent implementation; routine decisions remain autonomous.
- Spec revisions: reopen invalidated completion claims and affected dependencies.
- Interrupted work: concise resume notes and Git/code reconciliation prevent rerunning completed work.
- Dirty trees or pre-staged files: preserve unrelated changes and prevent accidental inclusion in commits.
- No Git/commit capability: report the limitation and do not invent commits or initialize repositories without authority.
- No isolation/model selection/reviewer capability: use the stated fallback and report limits accurately.
- Missing required checks: leave completion blocked; optional limitations remain explicit.
- A single installed skill: all local references must still resolve.
- Manual invocation enforcement and model APIs vary by harness; explain these portability limits without claiming universal enforcement.

## Acceptance criteria

- Three skills are discovered by the skills CLI with their intended names.
- Local project installations for Codex, Claude Code, OpenCode, and Pi retain all selected skill files and the spec template. Installation is tested in disposable directories, not user configurations.
- Each skill passes frontmatter validation and its relative references remain valid when installed alone.
- Spec authoring and build reading agree on progress, task IDs, dependencies, and completion rules.
- README provides usable installation/invocation instructions and accurately distinguishes local installation from the published remote repository.
- Review finds no unresolved material inconsistency with the requirements above.
- Git commits follow Conventional Commits, contain no attribution trailers, and the final working tree is clean.

## Verification

- `quick_validate.py` was attempted for each skill but could not import PyYAML in this environment. Its frontmatter constraints were checked with an equivalent dependency-free parser plus Ruby YAML parsing; all three skills passed names, descriptions, allowed keys, and TODO/placeholder checks.
- The cached `skills` CLI 1.5.19 was used in disposable projects: all three skills were installed for Codex, Claude Code, OpenCode, and Pi, and `t-spec` was installed alone in a separate project. Installed templates were compared with their sources.
- The package was independently reviewed. Five findings were grouped into `4423142` and the reviewer confirmed that all five were resolved, with exact three-file scope and clean commit metadata.
- The final repository contains Conventional Commits `65ed52a`, `4423142`, and this documentation commit, with no attribution trailers; the final working tree is clean.
- No real cross-harness model or cost benchmark was run, and no universal host enforcement of manual invocation is claimed. The missing PyYAML dependency is the only unavailable validator check.

## Task details

### T1 — Author the portable three-skill package

- Scope: skills, spec template, README, license, minimal repository configuration.
- Dependencies: agreed workflow.
- Acceptance: instruction content and package structure satisfy the requirements above.
- Verification: frontmatter validator, references, author self-review, Conventional Commit.

### T2 — Verify installation and specification compatibility

- Scope: disposable local install tests and source/template compatibility checks; documentation corrections if needed.
- Dependencies: T1.
- Acceptance: discovery and selected/all-skill installation succeed for documented targets; standalone references work.
- Verification: CLI outputs and installed-file comparison.

### T3 — Review behavior and complete the local handoff

- Scope: independent final review, grouped corrections, final progress and verification summary.
- Dependencies: T1, T2.
- Acceptance: meaningful findings resolved, completion accurately documented, clean committed repository.
- Verification: independent review, affected checks after fixes, final Git inspection.
