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

The reasoning behind each choice is recorded in [docs/adr/](docs/adr/README.md).

## Roadmap

Work is delivered as small pull requests into `dev`, in this order:

| #   | Branch                       | Delivers                                                                     | Status    |
| --- | ---------------------------- | ---------------------------------------------------------------------------- | --------- |
| 1   | `docs/readme-and-skeleton`   | README, contributing guide, repository set-up files                          | merged    |
| 2   | `docs/architecture`          | Architecture, tech stack, structure, repository management, decision records | in review |
| 3   | `docs/engineering-practices` | Design patterns, practices, code review, testing strategy                    | planned   |
| 4   | `docs/specs`                 | Spec-driven method and the first behaviour specs                             | planned   |
| 5   | `chore/repo-tooling`         | Git hooks, commit rules, formatting, CI skeleton                             | planned   |
| 6   | `feat/api-extraction-core`   | Extraction logic and local adapters, with tests                              | planned   |
| 7   | `feat/api-aws-auth-files`    | AWS adapters, Cognito sign-in check, file upload/download                    | planned   |
| 8   | `feat/api-llm-assist`        | Optional LLM field suggestion and repair                                     | planned   |
| 9   | `feat/web-app`               | React web app                                                                | planned   |
| 10  | `test/e2e-journey`           | Browser end-to-end test                                                      | planned   |
| 11  | `infra/cloudformation`       | Interim infrastructure template (CDK follows)                                | planned   |

## Documentation

| Read                                           | To learn                                                                |
| ---------------------------------------------- | ----------------------------------------------------------------------- |
| [Architecture](docs/ARCHITECTURE.md)           | How the system fits together: runtime, extraction, data, security       |
| [Tech stack](docs/TECH_STACK.md)               | Each technology, its version and why                                    |
| [Project structure](docs/PROJECT_STRUCTURE.md) | Folder layout, dependency rules, where new code goes                    |
| [Repository management](docs/REPOSITORY.md)    | One repo for web, API and infrastructure: builds, deploys, environments |
| [Decision records](docs/adr/README.md)         | Why each major choice was made                                          |
| [Contributing](CONTRIBUTING.md)                | Branch, commit and pull request rules                                   |

## Repository layout

Only the files below exist today. Folders are added by the pull requests above.

```
.
├── README.md          this file
├── CONTRIBUTING.md    how to propose changes
├── docs/              architecture, tech stack, structure, repository management, decisions (adr/)
├── .editorconfig      shared editor settings
├── .gitignore         files that never get committed
├── .nvmrc             Node version (22)
└── .python-version    Python version (3.13)
```
