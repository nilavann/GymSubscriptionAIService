# Contributing

How changes get into this repository. Automation (git hooks, formatting, CI) is added in a later
pull request; until then these rules are followed by hand.

## Branches

- `main` is production. `dev` is integration. Nothing is committed to either directly.
- Create a branch from the latest `dev`, named by type and topic:
  `feat/<topic>`, `fix/<topic>`, `docs/<topic>`, `chore/<topic>`, `test/<topic>`, `infra/<topic>`.
- Keep branches short-lived and focused on one change.

## Pull requests

1. Open a pull request **into `dev`**.
2. Describe what changed and why, and how it was checked.
3. Keep it small enough to review in one sitting.
4. The author reviews the final diff on GitHub, then **squash-merges** it.
5. Releases are a pull request from `dev` to `main`, merged with a normal merge commit.

## Commit and PR title format

[Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<optional scope>): <summary in the imperative, at most 72 characters>
```

Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `ci`, `build`, `perf`, `revert`.
Because pull requests are squash-merged, the **pull request title becomes the commit message**, so it
must follow this format.

## What never gets committed

- Secrets, tokens, keys or `.env` files.
- Real customer documents or scans. Use generated sample data only.
