# ADR-0008: One repository for web, API and infrastructure

- **Status:** Accepted

## Context

The product has three deployable parts: a React web app, a Python API that runs as a Lambda
function, and the AWS infrastructure that hosts both. They change together (a new API field and the
screen that shows it) and are owned by one developer today.

## Decision

Keep all three in a single repository (a monorepo), in separate top-level folders: `app/` (API),
`web/` (web app), `infra/` (CDK), plus `e2e/` and `docs/`. Each part has its own dependencies,
tooling and tests, and its own deployment path:

- `cdk deploy` (GitHub Actions) provisions the infrastructure and packages `app/` as the Lambda code.
- Amplify Hosting builds and publishes `web/` only (`appRoot: web`).

CI runs only the jobs a change needs (path filters) and reports one aggregate status so branch
protection can require it. Details: [REPOSITORY](../REPOSITORY.md).

Separate repositories per part were considered. They give independent release cycles and
permissions, which matter for several teams, at the cost of three CI set-ups, three sets of docs and
coordinating every cross-cutting change across repositories.

## Consequences

- A change that spans API and web is one pull request, one review and one squash commit.
- Deploys are identified by one git commit; there are no per-part version numbers to reconcile.
- The API and web deploy at slightly different times, so API changes stay backward compatible for
  one release (add, do not rename or remove, within a single pull request).
- CI configuration is more involved (path filters plus an aggregate job).
- The repository can be split later if teams or release cadences diverge.
