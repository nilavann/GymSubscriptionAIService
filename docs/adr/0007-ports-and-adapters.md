# ADR-0007: Ports and adapters for external services

- **Status:** Accepted. Planned for `app/ports.py` and `app/deps.py`.

## Context

The core logic depends on S3, Textract, DynamoDB and Bedrock. We want fast tests, offline
development and browser end-to-end tests without AWS.

## Decision

Define the needs as `typing.Protocol` ports (`FileStore`, `OCR`, `Repository`, `LLMClient`).
Business code depends only on ports. `app/deps.py` is the single composition root that picks AWS
adapters or local adapters (`BACKEND=local`). Pure logic (`extractor.py`) imports no AWS at all.

## Consequences

- Unit and integration tests run with in-memory fakes; a contract test will run the same assertions
  against both the in-memory and the DynamoDB (moto) repository so they cannot drift apart.
- A little indirection. Real AWS behaviour is still verified separately per environment.
