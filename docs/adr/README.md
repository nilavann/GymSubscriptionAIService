# Architecture Decision Records

One short file per significant, hard-to-reverse decision: what we chose, why, and what it costs us.
Write one when you pick a technology, change a boundary, or decide something a newcomer would
otherwise ask "why is it like this?" about. Do not edit an accepted ADR to change history; add a new
one that supersedes it.

| ADR                                              | Decision                                        | Status                        |
| ------------------------------------------------ | ----------------------------------------------- | ----------------------------- |
| [0001](0001-record-decisions.md)                 | Record decisions as ADRs                        | Accepted                      |
| [0002](0002-cdk-for-infrastructure.md)           | AWS CDK (TypeScript) for infrastructure         | Accepted, not yet implemented |
| [0003](0003-amplify-hosting-two-environments.md) | Amplify Hosting; dev and prod in one account    | Accepted, not yet implemented |
| [0004](0004-llm-selects-ocr-words.md)            | The LLM only selects OCR words by index         | Accepted, not yet implemented |
| [0005](0005-dynamodb-owner-partition.md)         | DynamoDB keyed by owner; model stored as JSON   | Accepted, not yet implemented |
| [0006](0006-direct-to-s3-uploads.md)             | Browser uploads straight to S3 (presigned POST) | Accepted, not yet implemented |
| [0007](0007-ports-and-adapters.md)               | Ports and adapters for external services        | Accepted, not yet implemented |
| [0008](0008-single-repository.md)                | One repository for web, API and infrastructure  | Accepted                      |

Template: copy any ADR. Keep it to one screen.
