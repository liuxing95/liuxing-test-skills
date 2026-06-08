# skill-publish-test

AI skills plugin sourced from GitLab and mirrored to GitHub through a reviewed
build-artifact sync flow.

## Structure

- `.claude-plugin/` contains Claude-compatible plugin and marketplace metadata.
- `skills/` contains public skill folders. Each skill should live in its own
  folder, for example `skills/skill-1/skill.md`.
- `scripts/` contains version validation, artifact build, GitHub branch sync,
  and pull request automation.
- `dist/github-sync/` is generated output only. It is the only content pushed to
  the GitHub mirror branch.

## Version Policy

The root `package.json` is the only package version authority. Changesets should
only update this package version, and these plugin metadata files must always
match it:

- `.claude-plugin/plugin.json`
- `.claude-plugin/marketplace.json`

Use the version assertion script before build, publish, or mirror sync.
For normal releases, commit a changeset with the feature change. GitLab CI
consumes pending changesets after a merge commit lands on protected `release`,
opens a `changeset-release/release` merge request back to `release`, and mirrors
the built artifact to GitHub after that release merge request lands.

## Local Commands

```sh
pnpm install
pnpm run validate
pnpm run build
pnpm run prepare:github-sync
```

`pnpm run prepare:github-sync` writes `dist/github-sync/` and validates that the
artifact only contains public allowlisted files.

## GitHub Mirror

GitLab remains the source of truth. When a merge commit lands on the protected
`release` branch, GitLab CI builds `dist/github-sync/`, pushes a timestamped
`mirror/<timestamp>` branch to `liuxing95/liuxing-test-skills`, and creates or
updates a GitHub pull request into `main`.

See `docs/github-sync.md` for setup and recovery details.
