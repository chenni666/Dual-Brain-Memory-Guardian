# Memory Operations (Dual-Brain)

This project now runs on a dual-brain model:

- Left Brain (Markdown): explicit rules and scoped overrides.
- Right Brain (Pinecone): semantic recall for corrections, reflections, and historical episodes.

## Runtime Commands (npm)

| Intent | Command |
|------|---------|
| Initialize index for model | `npm run memory:init` |
| Save correction/reflection to DEEP memory | `npm run memory:save -- --type correction --content "..."` |
| Semantic recall (Top-K) | `npm run memory:search -- --query "..." --top-k 3` |
| Start bulk import | `npm run memory:import:start -- --uri s3://bucket/path --error-mode continue` |
| Check import progress | `npm run memory:import:status -- --id <import-id>` |
| List imports | `npm run memory:import:list -- --limit 20` |
| Cancel import | `npm run memory:import:cancel -- --id <import-id>` |
| Freshness snapshot | `npm run memory:freshness` |
| Forget specific records/filters | `npm run memory:forget -- --id <id>` or `npm run memory:forget -- --type reflection --project qclaw` |
| Forget tenant memory in Pinecone | `npm run memory:forget-all -- --tenant default` |
| Syntax verify scripts | `npm run verify` |

### CLI parser note

- Values that start with `--` are supported via `--key=value` form.
- Example: `npm run memory:save -- --content=--prefixed-value --type correction`

## Automatic Operations

### On Session Start

1. Load `memory.md` (HOT).
2. Load smallest matching WARM files (`projects/*` and `domains/*`).
3. Generate recall query from current task (tech stack + failure modes + constraints).
4. Call semantic recall in Pinecone with `top_k=3` by default.
5. Merge retrieved experience with Markdown rules.

## Left-Right Merge Protocol

When executing a task:

1. Apply Markdown constraints first.
2. Use Pinecone hits to avoid historical pitfalls.
3. If Pinecone experience conflicts with Markdown, keep Markdown behavior and ask whether rules should be updated.

Priority order:

- Project Markdown > Domain Markdown > Global Markdown > Pinecone recall.

### On Correction Received

1. Parse correction type (`correction`, `reflection`, `pitfall`, `rejection`).
2. Save full normalized event to Pinecone DEEP memory (`memory:save`).
3. Check whether it is a hard contract (explicit "always/never" or project rule).
4. If hard contract, write concise version to Markdown (HOT/WARM).
5. Track repetition count. Repeated, high-signal rules can be promoted.

### On Pattern Match During Work

1. Cite source type in reasoning (Markdown rule or Pinecone recall).
2. Never treat Pinecone memory as mandatory contract unless promoted to Markdown.
3. Log usage count for future promotion/demotion decisions.

## Data Model Guidance (Pinecone)

Use structured IDs and metadata for reliable filtering and traceability.

- ID pattern: `tenant#event_type#timestamp#shortid`
- Namespace pattern: `selfimprove-<tenant>`
- Text field (integrated embedding): `content`
- Recommended metadata:
  - `event_type`
  - `domain`
  - `project`
  - `tenant_id`
  - `task_context`
  - `resolution`
  - `created_at`
  - `tags` (string list)

## Search Guidance

For integrated embedding indexes:

- Use `searchRecords` with text query.
- Keep `top_k` small (3-5) for planning, increase when synthesizing digests.
- Use metadata filters for `event_type`, `domain`, `project`, and `tags`.

## Ingestion Strategy

- Online events and corrections: `upsertRecords` (text input).
- Large backfills: `startImport` with Parquet vectors from object storage.
- For large datasets, prefer import over frequent upsert loops.

## Freshness and Consistency

Pinecone is eventually consistent.

After writes:

1. Use bounded fetch retry (`waitForWrite`) when strict read-after-write is needed.
2. Use `describeIndexStats` for namespace-level count checks.
3. Avoid assuming immediate visibility after upsert/import completion.

## Forget and Deletion Operations

- `Forget X`: remove from Markdown scopes and delete matching vector records by id or metadata filter (`memory:forget`).
- `Forget everything`: clear `~/self-improving/` and run Pinecone tenant clear (`memory:forget-all`).

## Edge Cases

### Contradiction

- Markdown says TypeScript, Pinecone recalls Python.
- Resolution: obey Markdown, attach optional note to user about historical difference.

### Namespace Ambiguity

- If tenant or project is unclear, default to current active tenant.
- Ask user before promoting ambiguous memory to global rules.

### Import Limits and Caveats

- Import only works for serverless indexes.
- Import into new namespaces only.
- Integrated embedding indexes import vectors, not text.
