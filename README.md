# Staged Memory System

An internal folder structure with semantic search — no external API calls. Full control of your data with fast recall. Set up in minutes.

---

## What It Is

A filesystem-based memory and orchestration system for AI agents. Instead of relying on external APIs for context, memory, and workflow management, the Staged Memory System uses plain markdown files organized in a layered folder structure. Your files ARE your memory. Your folders ARE your workflow.

**Key properties:**
- **Zero API dependencies for core memory** — no external services, no vendor lock-in
- **Instant recall** — filesystem reads are faster than any API call
- **Semantic search** — GBrain integration provides vector search across all content
- **Portable** — copy a folder, commit to git, sync to any machine
- **Glass-box** — every file is human-readable and editable
- **Agent-agnostic** — works with Hermes, Claude Code, Cursor, or any agent that reads files

---

## Quick Start

### 1. Clone

```bash
git clone https://github.com/CreativLogic/Staged-Memory-System.git ~/sms-workspace
cd ~/sms-workspace
```

### 2. Understand the Structure

Read through the folder map below. Every folder has a purpose. Every file has a home.

### 3. Point Your Agent Here

Tell your agent:
```
Your workspace is ~/sms-workspace.
Read CLAUDE.md first.
Follow the layered loading protocol.
All context lives in files — read before computing.
```

### 4. Set Up GBrain (Optional — for Semantic Search)

```bash
# Install GBrain
curl -fsSL https://bun.sh/install | bash
bun install -g github:garrytan/gbrain

# Initialize with local embeddings (no API needed)
gbrain init --pglite --embedding-model all-MiniLM-L6-v2

# Import workspace
gbrain import ~/sms-workspace

# Generate embeddings
gbrain embed --stale

# Search semantically
gbrain query "your search terms"
```

### 5. Set Up Context Mode (Optional — for Context Compression)

```bash
npm install -g context-mode
```

Add to your agent's MCP config:
```yaml
mcp_servers:
  context-mode:
    command: context-mode
    enabled: true
```

---

## The Five-Layer Structure

The system is built on five context layers. Agents load only what they need, when they need it. This prevents context window bloat and keeps the model focused.

```
Layer 0: CLAUDE.md / SOUL.md    → "Who am I?"
  Agent identity, hard rules, framework declaration.
  Always loaded. Short — under 60 lines.

Layer 1: CONTEXT.md             → "Where do I go?"
  Task routing table, what stages exist, which to run.
  Read on entry. Short — under 30 lines.

Layer 2: Stage CONTEXT.md       → "What do I do?"
  Per-task contract: Inputs → Process → Outputs.
  Every stage has its own CONTEXT.md. Max 80 lines.

Layer 3: Reference material     → "What rules apply?"
  Brand guides, style rules, pricing, identity files.
  Loaded selectively. Configured once, referenced everywhere.

Layer 4: Working artifacts      → "What am I working with?"
  Databases, outreach logs, stage outputs.
  Changes every run. One stage's output is the next stage's input.
```

**The rule:** Load a layer, check if you have what you need. If yes, stop. Every unnecessary token dilutes attention.

---

## Folder Structure

```
workspace/
├── CLAUDE.md                    Layer 0 — Agent identity, always loaded first
├── CONTEXT.md                   Layer 1 — Task routing, stage inventory
│
├── _config/                     Layer 3 — Configured once, stable across runs
│   ├── identity.md              Business context, pricing, target market
│   ├── branding.md              Voice, tone, positioning, personality
│   └── pricing.md               Offer tiers, what's included
│
├── _shared/rules/               Layer 3 — Cross-workspace reference
│   ├── style-guide.md           Writing style, formatting rules
│   ├── psychology.md            Persuasion principles
│   └── identity.md              Personal identity (founder/owner)
│
├── resources/                   Layer 3 — Reference databases, saved knowledge
│   ├── PERSISTENT-MEMORY.md     Cross-session knowledge (all agents link here)
│   ├── USER-IDENTITY.md         Who the user is — preferences, don't-dos
│   └── REFERENCE-MANUAL.md      Full system documentation
│
├── setup/
│   └── questionnaire.md         One-time onboarding — fills _config/ files
│
├── stages/                      Layer 2 — Execution contracts
│   ├── StageName/
│   │   ├── CONTEXT.md           Stage contract (Inputs → Process → Outputs)
│   │   ├── references/          Layer 3 — Stage-specific reference material
│   │   └── output/              Layer 4 — What this stage produces
│   └── NextStage/
│       ├── CONTEXT.md
│       ├── references/
│       └── output/
│
├── databases/                   Layer 4 — Persistent operational data
│   ├── pipeline.md              Active tracking
│   ├── log.md                   Action/outreach history
│   └── data.md                  Structured data
│
├── knowledge-bases/             Layer 3 — Research, profiles, documentation
│
└── projects/                    Active codebases and tools
```

### Stage Folder Naming

Stage folders use **capital letters, no numbers.** Execution order is defined in CONTEXT.md, not in folder names.

**Correct:** `Research/`, `Outreach/`, `Content/`, `Build/`
**Wrong:** `01-research/`, `02-outreach/`

Why: human-readable, reorderable without renaming, and the order lives in a file where it can be documented with reasoning.

---

## Stage Contracts

Every stage gets a CONTEXT.md with three sections:

### Inputs
```markdown
| Source | File/Location | Section/Scope | Why |
|--------|--------------|---------------|-----|
| Previous stage | ../PriorStage/output/ | Full file | Source material |
| Config | ../../_config/branding.md | Voice section | Tone guidance |
```

### Process
```markdown
1. Read [specific input file]
2. [Execute step with clear instruction]
3. [Execute next step]
4. Write output to output/
5. Run audit checks before saving
```

### Outputs
```markdown
| Artifact | Location | Format |
|----------|----------|--------|
| [Name] | output/[slug].md | Markdown |
```

### Checkpoints (for creative stages)
```markdown
## Checkpoints
1. After Step 2: Present [options]. Wait for human selection.
2. After Step 4: Present draft. Accept edits before finalizing.
```

### Audits (quality gate)
```markdown
## Audits
- [ ] [Check description] — Pass condition: [unambiguous criteria]
```

---

## Why It Works

### 1. Filesystem Speed

Filesystem reads are measured in microseconds. API calls are measured in milliseconds. When your agent needs context 50 times per task, those microseconds compound into seconds saved — and sharper responses.

### 2. Layered Loading

Most systems dump everything into context. 30,000-50,000 tokens of prompts, rules, and history. The model scans past irrelevant content to find what matters. By layering context — only loading what the current task needs — the model stays focused on what's relevant. Typical active context: 2,000-8,000 tokens instead of 30,000+.

### 3. Plain Text as Universal Interface

Every artifact is a markdown file. No databases, no proprietary formats, no special tooling. Any text editor can read it. Any version control system can track it. Any human can inspect it. The system state is the filesystem — open a folder and see exactly where you are.

### 4. Human-in-the-Loop by Default

Stage outputs are plain files. Between stages, a human can open, read, edit, and save before the next stage runs. The system picks up whatever the human left there. No special dashboard, no logging layer, no explanation system needed.

### 5. Portable and Version-Controlled

A workspace is a folder. Commit it to git. Clone it to another machine. Zip it and email it. Sync it through any cloud service. It carries its own prompts, context structure, and stage definitions. There's no server to configure, no environment to replicate.

### 6. Semantic Search Without APIs

GBrain provides vector embeddings and semantic search over all workspace content — using local models that run on your machine. No API keys, no data leaving your system, no per-query costs.

---

## Integrations

### GBrain — Semantic Search

GBrain indexes all workspace content and provides keyword + vector search across every file. Install once, run locally, no API needed.

**Commands:**
```bash
gbrain query "your question"       # Semantic search
gbrain search "keyword"            # Keyword search
gbrain embed --stale              # Re-index changed files
gbrain status                     # Check index state
```

### Context Mode — Context Compression

Keeps raw tool output out of your agent's context window. Sandbox execution means 315 KB of data becomes 5.4 KB. Automatic session continuity tracking.

```bash
npm install -g context-mode
# Add to agent MCP config (see Quick Start)
```

### Cloud Storage Sync

The entire workspace can be synced via:
- **Git** — `git push/pull` for version control
- **Dropbox/Drive** — real-time sync across machines
- **Syncthing** — peer-to-peer, no cloud
- **rsync** — periodic one-way sync to backup server

---

## Adapting to Your Agent

### Hermes Agent
```yaml
# config.yaml
context_file_max_chars: null
# Point to workspace as project directory
```

### Claude Code
```
Point CLAUDE.md to this workspace's root CLAUDE.md.
The five-layer routing is natively understood.
```

### Cursor / VS Code
```
Open the workspace folder. The agent reads CLAUDE.md on session start.
```

### Any Agent That Reads Files
```
Tell it: "Your workspace is this folder. Read CLAUDE.md first.
Load only what you need for the current task."
```

---

## Extending

### Adding a New Stage
```bash
mkdir -p stages/NewStage/{references,output}
cp templates/stage-context-template.md stages/NewStage/CONTEXT.md
# Edit CONTEXT.md with Inputs, Process, Outputs
# Update root CONTEXT.md to include the new stage in routing
```

### Adding a New Workspace
```bash
cp -r templates/workspace-template/ workspaces/NewWorkspace/
# Run setup to configure _config/ files
# Customize stages/
```

### Adding a New Integration
```bash
# Reference files go in resources/
# Active codebases go in projects/
# Cross-workspace rules go in _shared/
```

---

## File Hygiene

- Keep CLAUDE.md under 60 lines
- Keep CONTEXT.md under 30 lines
- Keep stage CONTEXT.md under 80 lines
- Keep reference files under 200 lines
- Skills stay under 500 lines — split if they grow beyond
- Version important files with date suffix: `filename_2026-04-10.md`
- Never delete without backup to `archives/`

---

## License

MIT — see LICENSE file.
