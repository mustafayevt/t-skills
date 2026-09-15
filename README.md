# t-skills

Three small, portable Agent Skills for a deliberate brainstorm → specification
→ implementation loop:

- `t-brainstorm` clarifies intent, alternatives, constraints, feasibility,
  edge cases, and success without writing implementation artifacts.
- `t-spec` turns the agreed decisions into a complete
  `docs/t-specs/<slug>/spec.md` with progress, stable tasks, contracts,
  acceptance, and verification.
- `t-build` implements an implementation-ready spec with scoped tasks, evidence gates,
  review, and explicit commits.

Every skill is explicitly user-invoked only. Each folder contains its own
`SKILL.md` and is independently installable; no skill requires another skill
to be installed. They describe portable behavior rather than assuming a
particular host's tools, models, routing scripts, worktree policy, or runtime.
Invoke a phase using the host's supported form, such as `/t-brainstorm`,
`$t-brainstorm`, `t-brainstorm`, or an explicit request; the exact syntax is
host-specific.
Manual invocation is an instruction-level convention; whether a host enforces
that convention is host-dependent.

During `t-build`, implementation and focused verification are delegated by
default to capable isolated workers when available. The orchestrator retains
specification, context, acceptance, and progress ownership; workers own scoped
edits, checks, self-review, and commits. Small related changes are batched and
dependent work remains sequential. If isolation is unavailable or denied,
`t-build` states the concrete direct-execution reason and the known or
inherited model selection; delegation and model selection remain separate.

## Install locally

Run the install from the target project, using the absolute path to this
checkout (or a path relative to that target project):

```sh
cd /path/to/target-project
npx skills add /absolute/path/to/t-skills --skill '*' -a codex -a claude-code -a opencode -a pi
```

To install one skill, replace `'*'` with its name, such as `t-build`. The
target list above is illustrative: choose the adapters supported by the
installed CLI and the host environments you use. This repository is also
published at `mustafayevt/t-skills`, so a remote install can use the published
repository URL:

```sh
npx skills@latest add mustafayevt/t-skills --skill '*' -a codex -a claude-code -a opencode -a pi
```

## Shared specification format

`t-spec` owns the complete local template at
`skills/t-spec/assets/spec-template.md`. The format keeps `Progress` and a
stable `Tasks` checklist at the top, followed by goal/context, binding
requirements versus implementation suggestions, non-goals, architecture and
decisions, contracts, edge cases, acceptance criteria, and verification.
`Task details` stays at the bottom and defines each stable task (`T1`, `T2`,
and so on) with scope, dependencies, acceptance, and verification. A spec may
omit sections that genuinely do not apply, but it must expose material
unknowns instead of inventing decisions. `t-build` contains its own concise
reader instructions for this same format, so installing it alone remains
functional.

## Provenance

T-Skills is original to this package and follows the open Agent Skills format;
no external skill text or code is copied here.
