# Knowledge Repo

**A git-native, LLM-ready knowledge management system**

> Markdown + Git + structured metadata = powerful, distributed knowledge base.

---

## The Problem

You have knowledge scattered across:
- Slack threads
- Internal wikis (that no one updates)
- StackOverflow answers
- Blog posts
- Personal notes in random formats

You want a system that:
- ✅ Stays in sync (no forgotten docs)
- ✅ Works offline (no SaaS subscriptions)
- ✅ Integrates with LLMs (for RAG, embeddings, AI)
- ✅ Lets teams collaborate (PR-based, not locked into one person)
- ✅ Is simple to implement (not another complex tool)

---

## The Solution

**Knowledge Repo** is a specification for storing knowledge in:
- **Plain Markdown files** (human-readable, version-controllable)
- **Git repositories** (familiar workflows, no new tooling)
- **Structured YAML front matter** (machine-parseable metadata)
- **Sharded directories** (scalable to millions of entries)

That's it. No database. No proprietary format. No vendor lock-in.

---

## Why It Works

| Aspect                  | Benefit                                                   |
|-------------------------|-----------------------------------------------------------|
| **Git-native**          | Version history, branches, PRs, offline access — built-in |
| **Markdown**            | Human-readable, compatible with every editor and tool     |
| **Structured metadata** | Queryable by tags, types, difficulty, author, etc.        |
| **Decentralized IDs**   | Anyone can create entries without a central counter       |
| **Simple format**       | Learn the spec in 10 minutes, not hours                   |

---

## Quick Example

A single entry:

```markdown
---
id: "fvb_iq"
title: "Fixing ImportError: No module named X in Python"
type: q-and-a
tags: [python, troubleshooting]
created: 2025-01-15
---

## Question
How do I fix `ImportError: No module named X`?

## Answers

### Answer 1: Check Your Virtual Environment
[Solution and example]

### Answer 2: Install the Package
[Solution and example]
```

Stored as: `entries/fv/fvb_iq.md`

That's literally it. No special parsing. Just Markdown.

---

## Designed For Collaboration

**Create an entry:**
```bash
k new q-and-a "How to set up SSH keys"
# Creates: entries/ab/abc123.md (with auto-generated ID)
```

**Search existing entries:**
```bash
k search --tag docker --tag kubernetes
```

**Validate your repo:**
```bash
k validate  # Checks schema, references, structure
```

**Use with LLMs:**
```python
# Search for relevant entries
results = k_search("your question")

# Use as RAG context
prompt = f"Context: {results}\n\nQuestion: {user_query}"
answer = llm.complete(prompt)
```

---

## Perfect For

- **Personal TIL:** Your own quick-reference knowledge base
- **Team docs:** Onboarding guides, runbooks, patterns
- **Public knowledge:** Distributed learning resources
- **AI/LLM integration:** Structured data for RAG, embeddings, fine-tuning

---

## How It Scales

Entries are sharded by ID prefix into directories:
- `entries/aa/`, `entries/ab/`, ..., `entries/zz/` (1,296 directories)
- ~5-10 entries per directory for a 10K-entry repo
- Works equally well at 100 or 10 million entries

Git stays happy. Your filesystem stays happy.

---

## What's Not Included (By Design)

- ❌ Web UI (use GitHub/GitLab)
- ❌ Voting systems (not a community site)
- ❌ Rich media management (Markdown images are fine)
- ❌ Real-time collaboration (Git PRs are good enough)

These can be added in v0.2.0 or by wrapping the spec. The core stays simple.

---

## Getting Started

### For your first repo:

1. **Understand the spec:** Read `DRAFT.md` (comprehensive but straightforward)
2. **Initialize:** `k init "My Knowledge Base"` (creates structure)
3. **Add entries:** `k new q-and-a "Your first question"` (templates provided)
4. **Share:** Push to GitHub, invite collaborators

### For LLM integration:

1. **Index your entries:** `k index` (generates searchable metadata)
2. **Query the repo:** `k search "topic"` or use Python API
3. **Pass to LLM:** Include top results as context

---

## The Spec

See **[DRAFT.md](./DRAFT.md)** for:
- Complete schema definition
- All 4 entry types and templates
- ID generation algorithm
- Validation rules
- CLI tool design
- Example repositories

**tl;dr:** JSON schema + Markdown + Git = Knowledge Repo

---

## Features

### v0.1.0 (Current)
- Markdown entries with YAML front matter
- 4 entry types (q-and-a, guide, pattern, note)
- Auto-generated IDs, directory sharding
- CLI tools (init, new, search, validate, index)
- Git-native workflow

### v0.2.0+ (Planned)
- Vector embeddings for semantic search
- Custom entry types and fields
- Plugin system
- Multi-repo aggregation

---

## Why Not Just Use X?

| Tool                  | Why Knowledge Repo is Different                                      |
|-----------------------|----------------------------------------------------------------------|
| **StackOverflow**     | No central platform; works offline; easier to contribute to your own |
| **Notion/Confluence** | Git-based; no SaaS lock-in; full version history; forkable           |
| **Obsidian**          | Structured metadata; designed for AI integration; team-friendly      |
| **GitHub Wiki**       | Simpler spec; better directory structure; easier to query            |

---

## License

- **Specification:** CC0 (Public Domain) — implement freely
- **CLI tool:** MIT (permissive open source)
- **Your repos:** Your choice (CC-BY, proprietary, whatever you want)

---

## Next Steps

1. **Want to understand it?** → Read [DRAFT.md](./DRAFT.md)
2. **Want to build a tool?** → Follow the spec, implement the CLI
3. **Want to use it?** → Clone, `k init`, start adding entries
4. **Want to contribute?** → See [CONTRIBUTING.md](./CONTRIBUTING.md)

---

## Real-World Example Repos

**Python Knowledge Base**
- Python Q&A: ImportError, asyncio, type hints, pytest
- Audience: Python developers
- Size: 100+ entries

**DevOps Runbooks**
- Kubernetes debugging, Docker troubleshooting, SSL cert renewal, database backups
- Audience: SRE/DevOps teams
- Size: 50+ entries (and growing)

**Web Development Patterns**
- REST versioning, authentication strategies, CORS, rate limiting, caching
- Audience: Backend/frontend engineers
- Size: 30+ entries

---

## Community

This is early-stage. Help shape it:

- **Questions?** Open an issue
- **Ideas for v0.2.0?** Start a discussion
- **Found a bug?** Report it
- **Built something cool?** Show us!

---

**Made with ❤️ for teams, AI, and knowledge that lasts.**
