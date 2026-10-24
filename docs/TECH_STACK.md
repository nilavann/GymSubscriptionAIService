# Tech stack

What we plan to use, at which version, and why. Versions are the **targets**, taken from an earlier
prototype that was built and tested with them. They are pinned for real (in `requirements*.txt`,
`package.json` and the lockfiles) when each part is added, and Dependabot proposes updates weekly.
Rationale for the big choices is in the [ADRs](adr/README.md). Nothing in this table is built yet.

## Runtime: backend (`app/`)

| Concern            | Choice                           | Why                                                                             |
| ------------------ | -------------------------------- | ------------------------------------------------------------------------------- |
| Language           | Python 3.13 (supports 3.11+)     | Strong AWS SDK, simple data handling; Lambda-supported                          |
| Web framework      | FastAPI 0.155                    | Typed request/response models, generated OpenAPI, dependency injection for auth |
| Validation, config | Pydantic 2.14, pydantic-settings | One model layer for API, storage and settings; env-driven config                |
| AWS SDK            | boto3 1.43                       | S3 presigning, DynamoDB, Textract, Bedrock Converse                             |
| Auth verification  | PyJWT 2.15 (with `cryptography`) | Verifies Cognito RS256 ID tokens against the pool's JWKS                        |
| Lambda adapter     | Mangum 0.22                      | Runs the ASGI app behind API Gateway                                            |
| Server (dev)       | Uvicorn 0.54                     | Local and e2e server                                                            |

## Runtime: frontend (`web/`)

| Concern     | Choice                             | Why                                                                  |
| ----------- | ---------------------------------- | -------------------------------------------------------------------- |
| Language    | TypeScript 6 (strict)              | Types shared in spirit with the API models                           |
| UI          | React 19, React Router 7           | Mainstream, testable components                                      |
| Build       | Vite 8                             | Fast dev server and static output for Amplify                        |
| Auth client | AWS Amplify Auth 6 (`aws-amplify`) | Hosted-UI sign-in with Google and token handling; only used for auth |
| Styling     | Plain CSS with theme tokens        | Small app; light/dark via `prefers-color-scheme`                     |
| Node        | 22 (`.nvmrc`)                      | Active LTS                                                           |

## AWS services

| Service              | Used for                                                    | Status                            |
| -------------------- | ----------------------------------------------------------- | --------------------------------- |
| S3 (+ KMS)           | Samples and scans; direct browser upload via presigned POST | planned (CloudFormation, CDK)     |
| DynamoDB             | Templates and jobs, keyed by owner                          | planned (CloudFormation, CDK)     |
| Cognito + Google IdP | Sign-in, ID tokens                                          | planned (CloudFormation, CDK)     |
| Textract             | OCR (sync for images, async for PDFs)                       | planned (API adapter)             |
| Bedrock (Claude)     | Optional field suggestion and repair                        | planned, optional, off by default |
| Lambda + HTTP API    | Hosting the API; JWT authorizer                             | **planned** (CDK)                 |
| Amplify Hosting      | SPA build and hosting, per environment                      | **planned** (CDK)                 |
| Secrets Manager      | Google OAuth client secret                                  | **planned**                       |
| CloudWatch           | Logs, alarms                                                | **planned**                       |
| IAM (OIDC)           | GitHub Actions deploy role, no stored keys                  | **planned**                       |

## Infrastructure as code

| Interim (planned)                                            | Target                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------- |
| `infra/template.yaml` (CloudFormation), linted with cfn-lint | AWS CDK 2 in TypeScript, `infra/cdk/`, tested with Jest |

## Quality tooling

Added in the tooling pull request and then extended with each part as it arrives.

| Purpose                 | Python side                              | Web side                                      | Repo level                        |
| ----------------------- | ---------------------------------------- | --------------------------------------------- | --------------------------------- |
| Lint                    | Ruff 0.6 (bugbear, security, pylint set) | ESLint 10 + typescript-eslint, react-hooks    |                                   |
| Format                  | Ruff format                              | Prettier 3                                    | Prettier for docs, config, YAML   |
| Types                   | mypy 1.11 (`strict`, pydantic plugin)    | `tsc` (strict, no unused)                     |                                   |
| Security scan           | Bandit, pip-audit, Ruff `S` rules        | `npm audit`                                   | Dependabot                        |
| Unit + integration test | pytest 8, pytest-cov, moto 5.1           | Vitest 5, Testing Library, jsdom, v8 coverage |                                   |
| End-to-end test         |                                          |                                               | Playwright 1.56 (Chromium)        |
| Infra lint              | cfn-lint                                 |                                               |                                   |
| Git hooks               |                                          |                                               | Husky 9, lint-staged 16           |
| Commit rules            |                                          |                                               | commitlint (Conventional Commits) |
| Spec traceability       |                                          |                                               | `scripts/spec_trace.py`           |
| CI                      |                                          |                                               | GitHub Actions                    |

## Considered and not chosen

| Option                       | Why not (for now)                                                                                                          |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| Terraform                    | Needs state storage and a second language; no multi-cloud need ([ADR-0002](adr/0002-cdk-for-infrastructure.md))            |
| S3 + CloudFront for the SPA  | No branch previews, more pipeline to maintain ([ADR-0003](adr/0003-amplify-hosting-two-environments.md))                   |
| Pass files through the API   | Size limits and cost ([ADR-0006](adr/0006-direct-to-s3-uploads.md))                                                        |
| LLM reads the image directly | Hallucination and injection risk, cost; OCR plus geometry is deterministic ([ADR-0004](adr/0004-llm-selects-ocr-words.md)) |
| Single-table DynamoDB design | Two simple tables are easier to reason about for this access pattern ([ADR-0005](adr/0005-dynamodb-owner-partition.md))    |
| Redux / React Query          | State is page-local; a small custom hook is enough to start                                                                |
