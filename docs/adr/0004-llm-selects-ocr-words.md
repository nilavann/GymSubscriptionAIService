# ADR-0004: The LLM only selects OCR words by index

- **Status:** Accepted. Planned for `app/llm_tasks.py`.

## Context

Scanned forms are untrusted input and contain personal data. An LLM that writes values or
coordinates can hallucinate them into a record, and text inside a scan can try to instruct the
model. Yet an LLM is useful for two chores: proposing field names and boxes on a sample form, and
rescuing a field the template missed.

## Decision

OCR (Textract) supplies all text and all coordinates. The model receives numbered OCR words
(`[17]Name [19]Ravi`) and answers, through a forced tool call, with names and word indices. The
server validates every index, builds boxes from the real OCR boxes, and assembles values from OCR
text. LLM-sourced values are tagged `source: "llm"` and stay `needs_review`. The model sees text
only (no images), only nearby words when repairing, and is optional (off when `BEDROCK_MODEL_ID`
is empty). A model failure never fails extraction.

## Consequences

- The model cannot invent a value; the worst case is picking a wrong real word, which is flagged.
- It cannot fix OCR misreads, only choose among words OCR found.
- Tests will pin this with scripted fake models, including a prompt-injection case.
