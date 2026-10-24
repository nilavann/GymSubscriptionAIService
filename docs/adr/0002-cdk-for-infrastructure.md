# ADR-0002: AWS CDK (TypeScript) for infrastructure

- **Status:** Accepted. Implementation pending; `infra/template.yaml` (plain CloudFormation) is the
  interim and is replaced stack by stack.

## Context

Everything runs on AWS only. The team already writes TypeScript for the web app. We need
reviewable, repeatable infrastructure for two environments (ADR-0003).

## Decision

Use AWS CDK in TypeScript, in `infra/cdk/`, with four stacks per environment (Data, Auth, Api, Web)
plus a CI deploy role. Terraform was considered: it is cloud-neutral, but needs a state file to be
stored and protected, a second language, and we have no multi-cloud need. CDK synthesises
CloudFormation, so AWS holds the state.

## Consequences

- One language and toolchain (lint, test, review) across web and infrastructure.
- Locked to AWS. Acceptable for this product.
- Stateful resources (S3, DynamoDB, KMS) use `RETAIN` and termination protection; stack tests assert it.
