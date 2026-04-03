# Setup - Self-Improving Dual-Brain Agent

## First-Time Setup

### 1. Create Local Memory Structure

PowerShell (Windows):

```powershell
New-Item -ItemType Directory -Force -Path "$HOME/self-improving/projects","$HOME/self-improving/domains","$HOME/self-improving/archive" | Out-Null
```

POSIX shell (macOS/Linux):

```bash
mkdir -p ~/self-improving/{projects,domains,archive}
```

### 2. Initialize Core Markdown Files

Create:

- `~/self-improving/memory.md` from `memory-template.md`
- `~/self-improving/corrections.md`
- `~/self-improving/index.md`
- `~/self-improving/heartbeat-state.md`

### 3. Enable npm Runtime

In this project root:

```bash
npm install
```

This installs `@pinecone-database/pinecone` from `package.json`.

### 4. Configure Environment

Copy `.env.example` to your local environment setup and set at least:

For npm commands in this repo, `.env` is auto-loaded by the CLI.

- `PINECONE_API_KEY`
- `PINECONE_INDEX_NAME` (default: `self-improving-memory`)
- `PINECONE_MODEL` (must stay `multilingual-e5-large` unless intentionally changed)

Optional:

- `PINECONE_IMPORT_INTEGRATION_ID` for private object storage import
- `MEMORY_TENANT` for tenant-scoped namespace

### 5. Initialize Pinecone Index

```bash
npm run memory:init
```

This command bootstraps or validates an integrated embedding index with:

- model: `multilingual-e5-large`
- field map: `text -> content`

### 6. Add SOUL.md Steering (Dual-Brain)

Add this section to `SOUL.md`:

```markdown
**Self-Improving (Dual-Brain)**
Before non-trivial tasks:
1) Read `~/self-improving/memory.md` and smallest matching `projects/`/`domains/` files.
2) Run semantic recall from Pinecone for likely pitfalls.
3) Merge both contexts, with Markdown contracts always winning on conflict.

After corrections or meaningful failures:
- Save the detailed episode to Pinecone DEEP memory.
- If it is an explicit durable rule, also write a concise Markdown rule.
```

### 7. Refine AGENTS.md Memory Behavior (Non-Destructive)

Update the `## Memory` section by adding:

- Self-improving continuity source (`~/self-improving/`)
- Pre-task recall ritual (Left Brain first, then Right Brain)
- Conflict policy (Markdown > Pinecone)
- Post-task routing rule (hard rule to Markdown, episode to Pinecone)

### 8. Add HEARTBEAT.md Steering

Ensure `HEARTBEAT.md` includes:

```markdown
## Self-Improving Check

- Read `./skills/self-improving/heartbeat-rules.md`
- Use `~/self-improving/heartbeat-state.md` for run markers
- If no local changes and no promotable DEEP digest signal, return `HEARTBEAT_OK`
```

### 9. Optional Proactivity Companion

If user agrees:

1. `clawhub install proactivity`
2. Read installed proactivity skill
3. Continue its setup flow immediately

If user declines, continue with self-improving dual-brain only.

## Verification

Run these checks:

```bash
npm run verify
npm run memory:init
npm run memory:freshness
```

Optional end-to-end smoke test:

```bash
npm run memory:save -- --type correction --content "sample correction" --domain code
npm run memory:search -- --query "sample correction" --top-k 3
```

Expected outcome:

- Index reachable
- Save command returns id/namespace
- Search command returns at least one related hit
