# Repository instructions

## Source of truth

The skill definitions under `skills/` and their bundled assets define the
repository's behavior. Keep every skill portable, independently installable,
and free of undeclared dependencies on sibling skills, repository-root files,
specific models, or host-only tools.

The three phases remain manually invoked and must not chain automatically:

1. `t-brainstorm` explores and hands off a design without implementation; it
   may write only `docs/t-specs/<slug>/handoff.md`.
2. `t-spec` writes or updates `docs/t-specs/<slug>/` (`spec.md` and
   `tasks/Tn.md`) and marks it `Ready` only on the user's approval.
3. `t-build` executes a selected ready specification and does not publish
   without explicit authorization.

## README maintenance

Treat `README.md` as part of the public interface. Update it in the same change
whenever work changes any of the following:

- a skill's purpose, workflow, output, or boundary;
- installation or invocation commands;
- public file paths or the specification format;
- defaults, prerequisites, compatibility, or supported host behavior;
- delegation, verification, review, commit, or publishing guarantees; or
- the repository structure shown to users.

Do not leave README maintenance for a later task. Before completing a change,
compare the README against the modified skill sources and correct any stale,
missing, or overstated claim. Examples must use the real public repository,
`mustafayevt/t-skills`, and current `skills` CLI syntax. Present the default
interactive installation before specialized or non-interactive commands.

Keep the README concise, professional, and useful to a first-time user. It is
not a changelog or an internal progress log. Document released behavior only;
do not advertise planned work as available.

## Validation

For documentation or skill changes:

1. Inspect the complete diff, including pre-existing work in the tree.
2. Check skill frontmatter, names, relative paths, and bundled assets.
3. Verify documented commands against the current CLI or its authoritative
   documentation when command behavior changes.
4. Search for stale repository names, paths, examples, and contradictory
   workflow descriptions.
5. Confirm the README still describes all user-visible changes before marking
   the work complete.

## Git conventions

Use Conventional Commits v1.0.0. Never add AI/Codex attribution,
`Co-Authored-By` trailers, generated-by footers, or robot emoji to commits or
pull requests. Stage only files that belong to the intended change. Do not
push, publish, deploy, or open a pull request without explicit authorization.
