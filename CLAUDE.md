# Repository instructions

This repository publishes three portable Agent Skills: `t-brainstorm`,
`t-spec`, and `t-build`. Read and follow `AGENTS.md` as the canonical repository
policy before changing a skill or its documentation.

## Keep the public README current

`README.md` is part of the product, not optional follow-up documentation.
Update it in the same change whenever a modification affects a skill's purpose,
workflow, output, boundaries, installation, invocation, public paths,
specification contract, defaults, compatibility, delegation behavior,
verification, commits, or publishing behavior.

Before finishing any skill change:

1. Compare the complete skill diff with the README.
2. Add or revise every user-visible statement affected by the change.
3. Verify commands and paths instead of copying stale examples.
4. Search for contradictions and obsolete repository names.
5. Keep the prose concise, professional, and accurate for a first-time user.

Installation examples must use the public repository
`mustafayevt/t-skills`. Lead with the default interactive command:

```sh
npx skills@latest add mustafayevt/t-skills
```

Show explicit skill, agent, global, or non-interactive flags only as optional
alternatives. Document current behavior only; never present planned behavior
as released.

## Architecture constraints

- Keep every skill self-contained and independently installable.
- Do not require sibling skills, specific models, or host-specific tools.
- Keep all phases manually invoked; never chain them automatically.
- Preserve the specification contract between `t-spec` and `t-build`.
- Preserve unrelated user work and do not publish without explicit authority.

Use Conventional Commits v1.0.0. Do not add AI attribution, co-author trailers,
generated-by footers, or robot emoji to commits or pull requests.
