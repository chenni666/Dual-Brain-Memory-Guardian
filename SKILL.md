---
name: Dual-Brain Memory Guardian
slug: dual-brain-memory-guardian
version: 1.0.0
description: "Dual-brain memory skill that combines strict Markdown rules with Pinecone semantic experience recall. Use when you need reliable rule-following plus long-horizon retrieval of corrections, reflections, and historical pitfalls."
changelog: "Refactored to Dual-Brain architecture with npm-based Pinecone runtime, multilingual-e5-large integrated embedding, and safer memory routing."
metadata: {"clawdbot":{"emoji":"🧠","requires":{"bins":["node","npm"],"env":["PINECONE_API_KEY"]},"os":["linux","darwin","win32"],"configPaths":["~/self-improving/"],"configPaths.optional":["./AGENTS.md","./SOUL.md","./HEARTBEAT.md",".env"]}}
---

## When to Use

Use this skill when any of the following applies:

1. The user corrects your output or asks for a rewrite.
2. You complete non-trivial work and need structured self-reflection.
3. You need to recall similar historical pitfalls with fuzzy semantic matching.
4. You need high-confidence behavior constraints that must not be violated.

## Architecture (Dual-Brain)

This skill separates memory into two complementary systems:

| Brain | Storage | Purpose | Priority |
|------|---------|---------|----------|
| Left Brain | Markdown files in `~/self-improving/` | Hard rules, explicit user contracts, project/domain overrides | Highest |
| Right Brain | Pinecone index (integrated embedding) | Corrections, reflections, episodic logs, fuzzy recall | Secondary |

### Left Brain (Rule Brain)

- HOT: `memory.md` (global contracts and confirmed preferences)
- WARM: `projects/*.md`, `domains/*.md` (context-scoped rules)

### Right Brain (Experience Brain)

- DEEP: Pinecone namespace per tenant (historical corrections, reflections, incident logs)
- Embedding model: `multilingual-e5-large`
- Ingestion/search via npm SDK: `@pinecone-database/pinecone`

## Priority and Conflict Protocol

When Left Brain and Right Brain disagree:

1. Always obey Markdown rules first.
2. Use Pinecone hits as anti-pattern hints and context only.
3. If a Pinecone hit is valuable but conflicts with Markdown, keep Markdown behavior and ask whether rules should be updated.

Resolution order:

- Project Markdown > Domain Markdown > Global Markdown > Pinecone recall.

## Quick Reference

| Topic | File |
|------|------|
| Skill contract | `SKILL.md` |
| Setup | `setup.md` |
| Runtime operations | `operations.md` |
| Heartbeat behavior | `heartbeat-rules.md` |
| Safety boundaries | `boundaries.md` |
| Pinecone config/runtime | `src/pinecone/` |
| CLI entrypoint | `scripts/memory-cli.js` |

## Requirements

- Node.js >= 20
- npm
- `@pinecone-database/pinecone`
- Environment variable: `PINECONE_API_KEY`
- Pinecone integrated index model: `multilingual-e5-large`

Optional for bulk import:

- Object storage path (`s3://`, `gs://`, Azure Blob URL)
- Integration ID for private buckets

## Runtime Workflow

Before work starts:

1. Read Left Brain rules (`memory.md` and smallest matching project/domain files).
2. Query Right Brain for related pitfalls (`topK` semantic recall).
3. Merge context with conflict protocol (Markdown-first).

During work:

1. On correction or failure, store detailed event in Pinecone DEEP memory.
2. If correction is a hard rule, also promote concise rule text to Markdown.

After work:

1. Save reflection/pitfall event in Pinecone.
2. Promote only stable, repeated, user-aligned rules to Markdown.

## Core Rules

### 1. Learning

- Learn from explicit corrections and self-reflections.
- Do not infer from silence.
- Ask for confirmation before promoting ambiguous patterns.

### 2. Data Routing

- Hard constraints and explicit preferences -> Markdown.
- High-volume episodic detail -> Pinecone.
- Repeated signals (>=3) can be distilled from Pinecone into Markdown.

### 3. Safety

- Apply redact-and-filter gate before vector upsert.
- Never upsert raw credentials, health data, or third-party personal data.
- On full forget, clear both Markdown memory and Pinecone tenant namespace.

### 4. Eventual Consistency

- Pinecone writes are eventually consistent.
- After upsert, use fetch retry or bounded wait before strict read-after-write assumptions.

### 5. Cost and Throughput

- For very large ingestion jobs, prefer import over frequent upsert batches.
- Keep metadata compact and filter-oriented.

## Scope

This skill ONLY:

- Maintains rule memory in `~/self-improving/`.
- Maintains experience memory in Pinecone.
- Uses npm-based Pinecone SDK operations (`upsertRecords`, `searchRecords`, `startImport`, `describeImport`, `describeIndexStats`).

This skill NEVER:

- Treats Pinecone recall as stronger than explicit Markdown contracts.
- Stores sensitive raw secrets in vector memory.
- Performs destructive heartbeat rewrites of uncertain content.

## Feedback

- If useful: `clawhub star dual-brain-memory-guardian`
- Keep skills updated: `clawhub sync`
