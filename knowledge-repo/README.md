# Knowledge Repo

**A git-native, LLM-ready knowledge management system**

---

## Quick Start

Welcome to the Knowledge Repo specification and reference implementation.

### What is it?

A lightweight, distributed knowledge management system built on:
- **Git** for version control and collaboration
- **Markdown** for human-readable content
- **Structured metadata** for machine parsing
- **Random IDs** for decentralized entry creation

### Why use it?

✅ **Offline-first:** No subscriptions or internet required  
✅ **Version-controlled:** Full git history, branches, PRs  
✅ **LLM-ready:** Structured metadata makes it ideal for RAG, embeddings, and AI tools  
✅ **Decentralized:** No central ID assignment — create entries anywhere  
✅ **Simple:** Markdown + YAML front matter, no proprietary format  

---

## Repository Structure

```
knowledge/
├── README.md                     # This file
├── CONTRIBUTING.md              # How to contribute
├── DRAFT.md                      # Full specification (v0.1.0)
├── entries/
│   ├── fv/
│   │   ├── fvb_iq.md            # Entry with random ID (sharded by fv)
│   │   └── fvm7pl.md            # Directory is first 2 chars of ID
│   ├── k3/
│   └── ...                       # One dir per 2-char base36 prefix
├── .knowledge/
│   ├── index.json               # Auto-generated index (git-ignored)
│   └── config.yaml              # Repository configuration
└── .git/
```

### Key Design Decisions

1. **Random IDs:** 4 bytes base64url-encoded (e.g., `FvB_iQ`) instead of sequential numbers
2. **Sharded directories:** Entries stored in `entries/{XX}/` based on first 2 ID characters
3. **Multiple answers:** `q-and-a` entries support multiple alternative answers, not just one
4. **Root README:** Quick-start guide right at the repo root

---

## Entry Types

| Type | Purpose | Example |
|------|---------|---------|
| **q-and-a** | Problem + multiple solution approaches | "How to fix ImportError in Python?" |
| **guide** | Step-by-step tutorial | "Getting started with asyncio" |
| **pattern** | Reusable design pattern | "Dependency injection in Python" |
| **note** | Quick reference or definition | "What is the Python GIL?" |

---

## Example Entry

**File:** `entries/Fv/FvB_iQ.md`

```yaml
---
id: "FvB_iQ"
title: "Fixing ImportError: No module named X in Python"
type: q-and-a
tags:
  - python
  - import
  - troubleshooting
created: 2025-01-15
updated: 2025-02-09
status: reviewed
difficulty: beginner
author: alice
related: ["K3mNwx", "zZ9pAb"]
---
```

Followed by markdown content with:
- Multiple `## Answer` sections for q-and-a type
- Clear structure with headings and code examples

---

## Getting Started

### For Repository Managers

1. **Read the spec:** See `DRAFT.md` for complete specification
2. **Initialize a repo:** Use the CLI tool `k init "My Knowledge Base"`
3. **Create entries:** `k new q-and-a "Your question here"` (auto-generates random ID)
4. **Validate:** `k validate` checks schema and structure
5. **Search:** `k search "keyword"` with tag filtering

### For Contributors

1. **Fork the repo** or create a feature branch
2. **Create entries** with `k new <type> "<title>"`
3. **Edit the markdown** — follow the template for your entry type
4. **Validate:** `k validate` before committing
5. **Submit PR** with descriptive message

### For LLM Integration

```python
# Search knowledge base
results = k_search("your question", limit=5)

# Build context from top results
context = "\n\n---\n\n".join([
    f"# {r.title} ({r.id})\n{r.body}"
    for r in results
])

# Prompt your LLM with context
answer = llm.complete(f"{context}\n\nQuestion: {user_query}")
```

---

## ID Format: Base36 Random

**Why random instead of sequential?**

- ✅ **Decentralized:** No central counter needed
- ✅ **Concurrent-safe:** Multiple people create entries simultaneously without conflicts
- ✅ **Collision-resistant:** 36^8 = ~2.8 trillion possibilities
- ✅ **Compact:** 8 characters (base36-encoded)
- ✅ **Shardable:** First 2 chars determine directory

**Examples:**
- `fvb_iq` → stored in `entries/fv/fvb_iq.md`
- `k3mnwx` → stored in `entries/k3/k3mnwx.md`
- `zz9pab` → stored in `entries/zz/zz9pab.md`

**Why base36 (not base64url)?**

Base36 uses only `0-9` and `a-z` (naturally lowercase, no special characters):
- **Naturally lowercase:** No case-sensitivity issues across platforms
- **No special chars:** URL-safe, filesystem-safe (no `-`, `_`, `+`, `/`)
- **Simpler implementation:** No base64url encoding, no padding
- **Human-readable:** Easier to type and read than base64url
- **Cross-platform:** Identical behavior on Linux, macOS, Windows without any configuration

---

## Multiple Answers for Q&A

`q-and-a` entries can include multiple answers (e.g., different approaches, trade-offs):

```markdown
## Question
How do I handle errors in async Python code?

## Answers

### Answer 1: Try/Except in Async Functions
[Explanation and example]

### Answer 2: Using asyncio.TaskGroup
[Explanation and example - requires Python 3.11+]

### Answer 3: Context Managers for Resource Management
[Explanation and example]
```

This allows entries to capture different perspectives without forcing a single "best" answer.

---

## Search & Discovery

**Full-text search:**
```bash
k search "asyncio"
```

**Filter by tags:**
```bash
k search --tag python --tag async --tag testing
```

**Filter by type:**
```bash
k search --type guide
```

**Get a specific entry:**
```bash
k get FvB_iQ
```

---

## Validation

```bash
k validate
```

Checks:
- ✅ All required front matter fields present
- ✅ No duplicate IDs
- ✅ File location matches ID (first 2 chars determine directory)
- ✅ All referenced entries exist
- ✅ Schema compliance (valid dates, types, etc.)
- ⚠️ Warnings for draft entries >30 days old, single-answer q-and-a entries, etc.

---

## Contributing

See `CONTRIBUTING.md` for:
- Pull request process
- Entry quality guidelines
- Review criteria
- Maintainer guidelines

---

## License

- **Specification:** CC0 (Public Domain) — free to implement or fork
- **This reference repo:** MIT — see LICENSE file
- **Individual knowledge repos:** Your choice (CC-BY, CC-BY-SA, MIT, proprietary, etc.)

---

## Next Steps

1. **Read the full spec:** `DRAFT.md` contains all details
2. **Clone or fork:** Use this repo as a template
3. **Create your knowledge base:** `k init "Your KB Name"`
4. **Add entries:** `k new <type> "<title>"`
5. **Share:** Push to GitHub and invite collaborators

---

## Features

### v0.1.0 (Current)
- ✅ Markdown + YAML front matter
- ✅ 4 entry types (q-and-a, guide, pattern, note)
- ✅ Random base64url IDs with directory sharding
- ✅ CLI tools (new, search, validate, index)
- ✅ Full-text + tag-based search
- ✅ Git-native workflow

### v0.2.0 (Planned)
- 🔄 Vector embeddings for semantic search
- 🔄 Custom entry types and fields
- 🔄 Plugin system
- 🔄 Multi-repo aggregation

### Out of Scope
- ❌ Web UI or hosted service
- ❌ Voting/reputation system
- ❌ Comments or discussions (use GitHub issues)
- ❌ Rich media asset management

---

## Comparison to Alternatives

| Feature | Knowledge Repo | StackOverflow | Notion | Obsidian |
|---------|---|---|---|---|
| **Offline-first** | ✅ Full | ❌ No | ❌ No | ✅ Yes |
| **Git native** | ✅ Yes | ❌ No | ⚠️ Limited | ⚠️ Plugins |
| **LLM-ready** | ✅ Structured | ❌ HTML | ❌ Proprietary | ⚠️ Markdown only |
| **Forkable** | ✅ Full | ❌ No | ❌ No | ⚠️ Manual |
| **Free & Open** | ✅ Yes | ✅ Community | 💰 Paid | ✅ Free |

**Unique to Knowledge Repo:**
- Distributed, no single point of failure
- Tool-agnostic (use any editor, any LLM)
- Composable (merge repos, create overlays)

---

## Questions?

- **Read the full spec:** See `DRAFT.md`
- **Contribute guidelines:** See `CONTRIBUTING.md`
- **Open an issue:** Report problems or suggest improvements
- **Fork and experiment:** This is meant to be a template

---

## Example Repositories (Inspiration)

**Python Knowledge Base**
- Focus: Python Q&A and patterns
- Entries: ImportError fixes, asyncio, type hints, pytest, virtual envs
- Tags: `python`, `async`, `testing`, `types`, `packaging`

**DevOps Runbooks**
- Focus: Infrastructure troubleshooting
- Entries: Kubernetes pod debugging, Docker networks, SSL certs, database backup
- Tags: `kubernetes`, `docker`, `ssl`, `database`, `oncall`

**Web Development Patterns**
- Focus: API and frontend patterns
- Entries: REST versioning, JWT vs sessions, CORS, rate limiting
- Tags: `api`, `auth`, `security`, `performance`, `architecture`

---

**Made with ❤️ by the Knowledge Repo community**
