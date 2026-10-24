# ADR-0003: Amplify Hosting; dev and prod in one AWS account

- **Status:** Accepted. Implementation pending.

## Context

Branches are `main` (production), `dev` (integration) and short-lived feature branches. The team is
small. We use default AWS URLs (no custom domain yet) and region ap-south-1 (Mumbai).

## Decision

- Host the SPA on Amplify Hosting: it builds from the repo on push, one app per environment,
  `dev` branch to the dev app, `main` to the prod app.
- Run two complete copies of the stacks (Data, Auth, Api, Web) in one AWS account, selected by a
  CDK context value and named per environment.
- The backend is deployed by GitHub Actions running `cdk deploy`, so two systems deploy: Amplify
  (frontend) and Actions (everything else).

S3 + CloudFront was the alternative: one pipeline and full code-only setup, but no per-branch
preview builds and more plumbing.

## Consequences

- Cognito does not accept wildcard callback URLs, so feature-branch preview URLs cannot sign in.
  Test sign-in locally or on `dev`.
- The API address and Cognito ids are baked into the SPA at build time; when CDK changes them, the
  Amplify app must rebuild.
- The one-time GitHub connection for Amplify is a manual console step.
- First deploy needs two passes to learn the Amplify URL for Cognito's callback list.
