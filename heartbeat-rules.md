# Heartbeat Rules (Dual-Brain)

Use heartbeat to keep `~/self-improving/` consistent while avoiding destructive edits.

## Source of Truth

- Keep workspace `HEARTBEAT.md` snippet minimal.
- Keep mutable run markers in `~/self-improving/heartbeat-state.md`.
- Keep raw historical episodes in Pinecone DEEP memory instead of local compaction-heavy logs.

## Start of Every Heartbeat

1. Ensure `~/self-improving/heartbeat-state.md` exists.
2. Write `last_heartbeat_started_at` immediately (ISO 8601).
3. Read previous `last_reviewed_change_at`.
4. Scan `~/self-improving/` for local changes after that timestamp, excluding `heartbeat-state.md`.
5. Run weekly or scheduled vector digest query for last 7 days (`correction`, `reflection`) from Pinecone.

## If Nothing Changed

- Set `last_heartbeat_result: HEARTBEAT_OK`.
- Append short note: no material local change and no promotable digest signal.
- Return `HEARTBEAT_OK`.

## If Something Changed

Do conservative maintenance only:

- Refresh `index.md` if file counts or references drift.
- Keep confirmed Markdown rules intact.
- Promote repeated DEEP signals into concise HOT/WARM Markdown rules only when evidence is strong.
- Update `last_reviewed_change_at` only after successful review.

## Digest Behavior (Vector-Assisted)

- Do not destructively compress raw event history from Pinecone.
- Query recent DEEP memory and detect repeated corrections (for example, 3+ similar events).
- Promote only the distilled rule to Markdown.
- Keep raw episodes in Pinecone for future audit and retrieval.

## Safety Rules

- Most heartbeat runs should do little or nothing.
- Prefer append/update over rewrite.
- Never delete uncertain text from Markdown.
- Never reorganize files outside `~/self-improving/`.
- If context is ambiguous, write a follow-up suggestion instead of forcing a change.

## State Fields

Keep `~/self-improving/heartbeat-state.md` lightweight:

- `last_heartbeat_started_at`
- `last_reviewed_change_at`
- `last_vector_digest_at`
- `last_heartbeat_result`
- `last_actions`

## Behavior Standard

Heartbeat should improve trust, not create churn.
If no clear rule is violated, do nothing.
