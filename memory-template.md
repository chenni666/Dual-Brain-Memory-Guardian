# Memory Template (Dual-Brain)

Copy this structure to `~/self-improving/memory.md` on first use.

```markdown
# Self-Improving Memory

## Confirmed Preferences
<!-- Explicit, durable user contracts. Markdown has highest priority. -->

## Active Patterns
<!-- Repeated and validated patterns promoted from DEEP memory. -->

## Recent Promotions (last 7 days)
<!-- Newly promoted rules distilled from Pinecone correction/reflection events. -->
```

## Initial Directory Structure

Create on first activation:

```bash
mkdir -p ~/self-improving/{projects,domains,archive}
touch ~/self-improving/{memory.md,index.md,corrections.md,heartbeat-state.md}
```

## Index Template

For `~/self-improving/index.md`:

```markdown
# Memory Index

## HOT
- memory.md: 0 lines

## WARM
- (no namespaces yet)

## DEEP
- provider: pinecone
- index: self-improving-memory
- namespace pattern: selfimprove-<tenant>

## COLD
- (optional local archives)

Last compaction: never
Last vector digest: never
```

## Corrections Log Template (Local Audit)

For `~/self-improving/corrections.md`:

```markdown
# Corrections Log

<!--
Use this file for lightweight local audit and promotion markers.
Detailed episodes are stored in Pinecone DEEP memory.

## YYYY-MM-DD
- [HH:MM] Rule promoted from DEEP memory
  Type: format|technical|communication|project
  Source: pinecone event ids or digest note
  Confirmed: pending (N/3) | yes | no
-->
```

## Heartbeat State Template

For `~/self-improving/heartbeat-state.md`:

```markdown
# Self-Improving Heartbeat State

last_heartbeat_started_at: never
last_reviewed_change_at: never
last_vector_digest_at: never
last_heartbeat_result: never

## Last actions
- none yet
```

## DEEP Memory Event Shape (Pinecone)

Use structured IDs and metadata:

```json
{
  "id": "tenant#correction#20260404T101500123#abcd1234",
  "content": "event_type: correction\ndomain: code\nproject: my-app\ncontent: user asked for TypeScript\nresolution: converted implementation to TS",
  "event_type": "correction",
  "domain": "code",
  "project": "my-app",
  "tenant_id": "default",
  "created_at": "2026-04-04T10:15:00.123Z",
  "tags": ["typescript", "api"]
}
```
