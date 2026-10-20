# Gym Subscription AI Service

Upload a scanned form, get the field values back. You teach the system a form **once** by outlining
the values on a filled sample (a _template_). Every later scan of that form is read automatically,
and anything doubtful is flagged for a person to check.

> **Status: planning.** This repository currently holds documentation and repository set-up only.
> There is no application code yet. Each part below is added in its own pull request and the status
> table is updated as it lands.

## How it will work

```
sample scan ─► S3 ─► Textract (words + boxes) ─► you outline the fields (optionally AI-suggested)
                                                      └► saved as a template: field boxes + printed-label anchors
new scan    ─► S3 ─► Textract ─► align to the template's anchors ─► read each field box
                                                      └► values + confidence; doubtful ones flagged
```

The alignment is plain geometry, not AI, so it is fast, cheap and testable. An optional LLM only
**chooses among words the OCR already found**, so it cannot invent a value.

## Planned technology

| Part           | Choice                                                                |
| -------------- | --------------------------------------------------------------------- |
| API            | Python, FastAPI, Pydantic                                             |
| Web app        | React, TypeScript, Vite, hosted on AWS Amplify                        |
| Sign-in        | Amazon Cognito with Google                                            |
| Files and data | Amazon S3 (private, encrypted) and DynamoDB                           |
| OCR and AI     | Amazon Textract; Amazon Bedrock (optional)                            |
| Infrastructure | AWS CDK in TypeScript, separate dev and prod environments             |
| Quality        | Husky hooks, Ruff, mypy, ESLint, Prettier, pytest, Vitest, Playwright |

The reasoning behind each choice will be recorded in `docs/` as it is documented.

## Roadmap

Work is delivered as small pull requests into `dev`, in this order:

| #   | Branch                            | Delivers                                                  | Status    |
| --- | --------------------------------- | --------------------------------------------------------- | --------- |
| 1   | `docs/readme-and-skeleton`        | README, contributing guide, repository set-up files       | in review |
| 2   | `docs/architecture-and-practices` | Architecture, tech stack, structure, patterns, practices  | planned   |
| 3   | `docs/specs`                      | Spec-driven method and the first behaviour specs          | planned   |
| 4   | `chore/repo-tooling`              | Git hooks, commit rules, formatting, CI skeleton          | planned   |
| 5   | `feat/api-extraction-core`        | Extraction logic and local adapters, with tests           | planned   |
| 6   | `feat/api-aws-auth-files`         | AWS adapters, Cognito sign-in check, file upload/download | planned   |
| 7   | `feat/api-llm-assist`             | Optional LLM field suggestion and repair                  | planned   |
| 8   | `feat/web-app`                    | React web app                                             | planned   |
| 9   | `test/e2e-journey`                | Browser end-to-end test                                   | planned   |
| 10  | `infra/cloudformation`            | Interim infrastructure template (CDK follows)             | planned   |

## Working in this repository

Read [CONTRIBUTING.md](CONTRIBUTING.md) for the branch, commit and pull request rules.

## Repository layout

Only the files below exist today. Folders are added by the pull requests above.

```
.
├── README.md          this file
├── CONTRIBUTING.md    how to propose changes
├── .editorconfig      shared editor settings
├── .gitignore         files that never get committed
├── .nvmrc             Node version (22)
└── .python-version    Python version (3.13)
```
