# ADR-0005: DynamoDB keyed by owner; model stored as JSON

- **Status:** Accepted. Planned for `app/services/repo.py`.

## Context

Every record belongs to one user. Access patterns are few: get one, list mine, newest first.

## Decision

Two tables (`templates`, `jobs`), partition key `owner_id` (Cognito `sub`), sort key `id`. Job ids are
`<epoch-ms>-<random>`, so a reverse `Query` returns newest first with no scan and no index. The full
model is stored as a JSON string in `doc`; a few fields (`status`, `created_at`, `name`) are copied
to top-level attributes for listing.

## Consequences

- Tenant isolation holds by construction: every read is a `GetItem` or `Query` in the caller's
  partition, so there is no query that could return another user's rows.
- Schema changes need no migration (Pydantic defaults handle old documents); attribute-level queries
  such as "all failed jobs" are not possible without adding an index.
- Items cap at 400 KB; large OCR output will move to S3 for multi-page templates.
