# Staged Memory System

The SMS framework is an internal folder structure with semantic search that offers a built-in context mode and provides you with faster memory recall, without giving up control of your data to external companies. — no external API calls. Full control of your data with fast recall. 5-minutes to setup.


---

## What It Is

A filesystem-based memory and orchestration system for AI agents. Instead of relying on external APIs for context, memory, and workflow management, SMS uses plain markdown files organized in a layered folder structure with instant memory recall and the ability to easily search or query your data. Your files ARE your memory. Your folders ARE your workflow.

**Key properties:**
- **Zero API dependencies for core memory** — no external services, no vendor lock-in
- **Instant recall** — filesystem reads are faster than any API call
- **Semantic search** — GBrain integration provides vector search across all content
- **Portable** — copy a folder, commit to git, sync to any machine
- **Glass-box** — every file is human-readable and editable
- **Agent-agnostic** — works with Hermes, Claude Code, Cursor, or any agent that reads files


I'm Vinnie and I am just a regular guy who geeks out on AI in between running a business and being a dad to 3 boys. After trying a million and one different systems, tools or frameworks...I was broke. g
Just kidding. But i was seriously mentally exhausted and increasingly frustrated. I hated being dependant on the model providers. The memory providers. The api providers. And everything else. It feels like endless costs for things I cant even see or hold in my hand. So I created this. 

My entire AIOS became portable and eliminated the risk of being one update away from breaking. It also changes what it means to use agents entirely. 
SMS becomes less dependant on the model I am using, while produced better results at a level of consistency that I haven't seen before. 
This is especially helpful if you work with clients or teams and need to access eachothers folders or files. 
The portability alone is incredible, and dramatically cuts or lowers your token costs, and doesn't rely on a bunch of tool calls or plugins. 

So give it a try. Spend the five minutes setting it up.
Take another 15-20 to really understand it and I promise you that once you see it, you can't unsee it.

I simply wanted control over my own data without having to run everything completely locally and without adding another monthly subscription fee to my already long list of subscriptions. 
That's why I built this.
It's not flashy and doesn't have any "wow" factors. It just works. Exceptionally well, actually.

**Let me briefly break it down:**

A local filesystem read on NVMe/SSD is typically 10-100 microseconds
- An API call (HTTPS roundtrip + server processing) is typically 50-500 milliseconds
- That's roughly 500-5000x faster for filesystem reads vs API calls.
- Over many operations this compounds significantly.

Tools like Honcho have a limited free tier and then you pay for api calls at the additional expense of not having control of your data.
This is 100% Free, easy to setup and gives you 100% control of your data, with faster speed and accuracy.



---

## Quick Start — Step by Step

You have two options to get started. Pick one.

### Option A: Clone as Your Workspace

```bash
git clone https://github.com/CreativLogic/Staged-Memory-System.git ~/sms-workspace
cd ~/sms-workspace
```

### Option B: Clone into an Existing Folder

```bash
mkdir ~/sms-workspace
cd ~/sms-workspace
git clone https://github.com/CreativLogic/Staged-Memory-System.git .
```

After either option, you should see this structure:
```
sms-workspace/
├── CLAUDE.md       ← You need to create this (see Step 2)
├── CONTEXT.md      ← You need to create this (see Step 2)
├── _config/
├── _shared/
├── resources/
├── stages/
├── databases/
├── templates/
└── README.md
```

---

### Step 1: Understand the Five Layers

Before creating anything, understand the loading model. This is the foundation:

| Layer | File | Job | When Loaded | Max Size |
|-------|------|-----|-------------|----------|
| 0 | `CLAUDE.md` | Agent identity + hard rules | Every session start | 60 lines |
| 1 | `CONTEXT.md` | Task routing | After Layer 0 | 30 lines |
| 2 | `stages/*/CONTEXT.md` | Stage contract | When executing that stage | 80 lines |
| 3 | `_config/`, `_shared/`, `resources/` | Rules, style, identity | Selectively per task | 200 lines |
| 4 | `databases/`, `stages/*/output/` | Working data | Selectively per task | No limit |

### The golden rule:
Load a layer. Check if you have enough. **If yes, stop.** 
Every unnecessary token dilutes attention and degrades performance.

---

### Step 2: Create Your Agent Files

Create the two required files for your workspace.

**CLAUDE.md (Layer 0) — under 60 lines:**

```bash
cat > CLAUDE.md << 'EOF'
# Your Agent Name

<!-- REFERENCE: resources/REFERENCE-MANUAL.md -->

I am [agent name]. My purpose is [one sentence].

## Five-Layer Protocol

| Layer | File | When |
|-------|------|------|
| 0 | CLAUDE.md (this file) | Every session start |
| 1 | CONTEXT.md | After Layer 0 |
| 2 | stages/*/CONTEXT.md | When executing a stage |
| 3 | _config/, _shared/, resources/ | When rules needed |
| 4 | databases/, stages/*/output/ | When data needed |

## References (load on demand)

| When | Where |
|------|-------|
| [Task type] | `[file path]` |
| User identity | `resources/USER-IDENTITY.md` |
| Full system docs | `resources/REFERENCE-MANUAL.md` |

## Hard Rules

- Filesystem first — read before computing or asking
- Never ask for context that exists in a file
- Load a layer, check if you have enough, stop
- Stage folders: capital letters, no numbers
- Never delete without backup to archives/
EOF
```

**CONTEXT.md (Layer 1) — under 30 lines:**

```bash
cat > CONTEXT.md << 'EOF'
# Task Router

## Pipeline

| Stage | Status | Output |
|-------|--------|--------|
| [StageName] | Ready | stages/[StageName]/output/ |

## Routing

| Task | Stage |
|------|-------|
| [description] | stages/[StageName]/CONTEXT.md |

## Stage Order

1. [First stage] → 2. [Second stage]

Order is defined here, not in folder names.
EOF
```

**Verification:** Both files created. Your agent now has a Layer 0 identity and Layer 1 router.

---

### Step 3: Create Your First Stage

Each stage is a named folder under `stages/`. Create one now:

```bash
mkdir -p stages/MyFirstStage/{references,output}

cat > stages/MyFirstStage/CONTEXT.md << 'EOF'
# MyFirstStage — [Purpose]

## Inputs
| Source | File | Section | Why |
|--------|------|---------|-----|
| Config | ../../_config/identity.md | Relevant section | Context |

## Process
1. Read inputs
2. Execute task
3. Write output to output/
4. Run checks before saving

## Outputs
| Artifact | Location | Format |
|----------|----------|--------|
| Result | output/[name].md | Markdown |
EOF
```

**Verification:** `ls stages/MyFirstStage/` shows `CONTEXT.md`, `references/`, `output/`.

---

### Step 4: Add Your Reference Files

Populate the Layer 3 files — these are loaded on demand, not every session.

```bash
# User identity (who the user is, preferences, don't-dos)
cat > resources/USER-IDENTITY.md << 'EOF'
# User Identity

## Who
[Name, role, background]

## Preferences
- [Communication preference]
- [Work style]

## Don't-Dos
- [Thing to never do]
EOF

# Brand/config (if applicable)
cat > _config/identity.md << 'EOF'
# Project Identity

## What
[Project description]

## Target
[Who it's for]
EOF
```

**Verification:** Files exist in `resources/` and `_config/`. These won't bloat your agent prompt — they're only loaded when a task needs them.

---

### Step 5: Point Your Agent Here

Tell your agent to use this workspace. The exact method depends on your platform:

**Hermes Agent:**
```bash
# Set as working directory in your agent config or session
cd ~/sms-workspace
```

**Claude Code:**
Open the folder in Claude Code. It reads CLAUDE.md automatically on session start.

**Cursor / VS Code:**
Open the folder. The AI reads CLAUDE.md from the workspace root.

**Any Agent That Reads Files:**
```
Your workspace is ~/sms-workspace.
Read CLAUDE.md first.
Follow the five-layer loading protocol.
All context lives in files — read before computing.
Never load everything at once.
```

**Verification:** Start a session with your agent. It should read CLAUDE.md, then CONTEXT.md, then route to your stage.

---

### Step 6: Create Your Persistent Memory (Recommended)

This file accumulates knowledge across sessions. All agents reference it.

```bash
cat > resources/PERSISTENT-MEMORY.md << 'EOF'
# Persistent Memory — Cross-Session Knowledge

## Preferences (accumulated)
- [Fact] | Source: [date]

## Decisions Made
- [Decision] | Context: [why] | Date: [when]

## Corrections (mistakes to never repeat)
- [Correction] | Lesson: [what to do instead] | Date: [when]

## Active Projects
- [Project] | Status: [state]
EOF
```

**Verification:** File created. At session end, update it with new learnings.

---

### Step 7: Set Up GBrain for Semantic Search (Recommended)

GBrain indexes all workspace content and provides semantic search — finding facts by meaning, not just keywords. Runs entirely local, no API needed.

```bash
# 1. Install Bun (required by GBrain)
curl -fsSL https://bun.sh/install | bash
# Restart your terminal or source your profile after install

# 2. Install GBrain
bun install -g github:garrytan/gbrain

# 3. Initialize brain with local embeddings
gbrain init --pglite --embedding-model all-MiniLM-L6-v2
# This creates ~/.gbrain/brain.pglite — a local Postgres database
# The embedding model is ~80MB, downloaded once on first use

# 4. Import your workspace
gbrain import ~/sms-workspace
# This syncs all markdown files into the brain
# Expect: "Import complete: N pages imported, M chunks created"

# 5. Generate embeddings
gbrain embed --stale
# This may take 2-5 minutes on first run depending on workspace size
# Subsequent runs only re-embed changed files

# 6. Test semantic search
gbrain query "what is the workspace structure"
# Should return relevant sections from your workspace files
# Ranked by relevance, not just keyword match
```

**Verification:** Run `gbrain status` — should show pages imported and embed percentage. Run `gbrain query "test"` — should return results.

**Troubleshooting:**
- If `gbrain` not found: restart terminal or run `source ~/.bashrc`
- If embed fails: ensure `sentence-transformers` is installed: `pip install sentence-transformers`
- If import is slow: first run indexes everything, subsequent runs are incremental
- If you prefer keyword-only search (no embeddings): `gbrain init --pglite --no-embedding` and skip step 5

---

### Step 8: Set Up Context Mode for Compression (Recommended)

Keeps raw tool output out of your agent's context window. Sandbox execution means megabytes of data become kilobytes.

```bash
# 1. Install globally
npm install -g context-mode

# 2. Verify installation
context-mode --version
# Should output: 1.0.x

# 3. Add to your agent's MCP config
# For Hermes (~/.hermes/config.yaml):
```

```yaml
mcp_servers:
  context-mode:
    command: context-mode
    enabled: true
```

```bash
# For Claude Code: install as a plugin
# /plugin marketplace add mksglu/context-mode
# /plugin install context-mode@context-mode

# 4. Restart your agent
# The following tools become available:
# ctx_execute — run code in sandbox, only stdout enters context
# ctx_search — FTS5 search with BM25 ranking
# ctx_batch_execute — parallel commands with auto-indexing
# ctx_fetch_and_index — web content, raw HTML never enters context
# ctx_index — store content for later search
# ctx_stats — context consumption statistics
```

**Verification:** Run `ctx_doctor` slash command or check agent tool list — should show context-mode tools. Run `ctx_stats` to see context savings.

**Troubleshooting:**
- If tools don't appear: restart your agent completely
- If `command not found`: ensure npm global bin is in PATH (`export PATH="$HOME/.npm-global/bin:$PATH"`)
- If MCP fails to connect: check the command path with `which context-mode`
- For WSL users: you may need to add `--no-sandbox` to the MCP command args

---

### Step 9: Run Your First Full Cycle

Test the entire system end-to-end:

1. **Start a session** with your agent pointed at the workspace
2. **Agent reads** CLAUDE.md → CONTEXT.md → routes to a stage
3. **Agent executes** the stage, writing output to `stages/*/output/`
4. **Review the output** — open the output file, edit if needed
5. **Run the next stage** — it picks up your edited output
6. **End session** — update PERSISTENT-MEMORY.md with learnings
7. **Run GBrain embed** — `gbrain embed --stale` to index new content
8. **Search** — `gbrain query "session topic"` to verify recall

**Expected outcome:** Your agent navigates the workspace without prompting. Context stays lean. Memory persists across sessions. Search finds relevant past content.

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

## Keeping Files Lean — The Reference Pattern

The most common mistake: dumping everything into SOUL/CLAUDE files. Long system prompts dilute attention. The model scans past irrelevant content, missing what matters. Every unnecessary token costs accuracy, speed, and money.

### The Rule

**SOUL/CLAUDE files are indexes, not encyclopedias.** They contain identity, hard rules, and reference pointers. Everything else lives in its own file and is loaded on demand.

### What Belongs in SOUL/CLAUDE (Layer 0)

- Agent identity — who you are, what you do
- Hard rules — the 5-8 things you must never violate
- Reference map — a table pointing to where detailed instructions live
- The five-layer loading protocol

**Target: under 60 lines.**

### What Does NOT Belong in SOUL/CLAUDE

- Detailed methodology or frameworks → goes in `resources/` or `_shared/rules/`
- Complete workflow instructions → goes in stage CONTEXT.md files
- Lists of skills, models, or tools → goes in `CONTEXT.md` or a reference file
- Configuration details → goes in config files
- Personal preferences longer than one line → goes in `USER-IDENTITY.md`

### The Reference Pattern

Instead of embedding instructions, point to them:

```markdown
## References (load on demand)

| When | Where |
|------|-------|
| Writing copy | `_shared/rules/style-guide.md` |
| Outreach emails | `stages/Outreach/references/email-rules.md` |
| User preferences | `resources/USER-IDENTITY.md` |
| Full system docs | `resources/REFERENCE-MANUAL.md` |
```

### Example: Bad vs Good SOUL

**Bad (bloated — 200+ lines):**
```markdown
# Agent Soul
I am a copywriter. Here are the 47 rules of copywriting:
1. Never use passive voice because...
2. Always start headlines with numbers because...
3. The AIDA framework works by first grabbing Attention through...
[... 180 more lines of methodology]
```

**Good (lean — 50 lines, references methodology):**
```markdown
# Agent Soul
I am a copywriter. Every word measured by one standard: does it move the reader?

## References
| When | Where |
|------|-------|
| Before writing any copy | `_shared/rules/copywriting-guide.md` |
| Client-facing content | `_config/branding.md` |

## Hard Rules
- Never write without reading the guide first
- Research before writing — research IS the work
- One CTA, one action, one outcome
```

### Creating New Agents

1. **Start with the template:** Copy `templates/workspace-template/` as your agent's home
2. **Write SOUL first:** Identity + hard rules + reference map. Under 60 lines.
3. **Write CONTEXT second:** What stages exist, which to run for what task. Under 30 lines.
4. **Move methodology to references:** Put detailed instructions in `_shared/rules/` or stage `references/` folders
5. **Test the loading:** Can the agent find what it needs in 2 layers or less? If not, restructure.

### The Bloat Test

After writing any agent file, ask:
- "Can I remove this line without the agent losing something critical?"
- "Does this instruction belong in a reference file instead?"
- "Would an agent still know what to do if they only read the first 20 lines?"

If any answer is yes, move content to a reference file and add a pointer.

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
