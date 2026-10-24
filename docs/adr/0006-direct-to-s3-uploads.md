# ADR-0006: Browser uploads straight to S3 (presigned POST)

- **Status:** Accepted. Planned as `POST /files/upload-url`.

## Context

Scans are up to 10 MB. Passing them through the API wastes compute and hits Lambda's request size
limit.

## Decision

The API mints an object key (`{sub}/{templates|documents}/{uuid}.{ext}`) and returns a short-lived
presigned POST whose conditions pin the content type and `content-length-range`. The browser posts
the file to S3; the API later refers to it by key and rejects any key outside the caller's prefix.
Downloads use short-lived presigned GET links.

## Consequences

- Files never touch the API; S3 enforces type and size.
- Orphan objects are possible if a user uploads and never submits; a lifecycle rule expires
  `documents/` after 90 days.
- The bucket needs CORS for the app origin. A local-mode shim imitates the flow offline.
