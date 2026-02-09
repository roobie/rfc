# Knowledge Repo v0.1.0 Specification (DRAFT)

**A git-native, LLM-ready knowledge management system**

---

## Design Principles

- **Simplicity first:** Minimal required fields, flat structure, plain Markdown
- **Git is the platform:** Version control, collaboration, and distribution built-in
- **Human and machine readable:** Natural language for people, structured metadata for tools
- **Offline-capable:** No external dependencies required for core functionality

---

## Repository Structure

```text
knowledge/
├── entries/
│   ├── 0001-python-importerror-fix.md
│   ├── 0002-rust-ownership-basics.md
│   └── 0003-docker-multistage-builds.md
├── .knowledge/
│   ├── index.json           # Auto-generated, git-ignored
│   └── config.yaml          # Repo-level settings
├── meta/
│   ├── README.md            # Human-readable repo overview
│   └── CONTRIBUTING.md      # Contribution guidelines
└── .git/
```

**Key decisions:**

- **`entries/`**: Single flat directory, entries named `{id}-{slug}.md`
- **`.knowledge/`**: Tool-generated files (indexes, caches) — git-ignored by default
- **`meta/`**: Human-facing documentation about the repo itself

---

## Entry Schema (v0.1.0)

### Required Front Matter

```yaml
---
id: "0001"
title: "Fixing ImportError: No module named X in Python"
type: q-and-a
tags:
  - python
  - import
  - troubleshooting
created: 2025-01-15
---
```

### Optional Front Matter

```yaml
updated: 2025-02-09        # Last significant update
status: reviewed            # draft | reviewed | outdated
difficulty: beginner        # beginner | intermediate | advanced
author: alice               # GitHub handle or name
related: ["0045", "0089"]   # IDs of related entries
source:
  url: https://example.com/original-post
  license: CC-BY-4.0
```

### Body Structure (Recommended Templates)

**For `type: q-and-a`:**

```markdown
## Question

How do I fix `ImportError: No module named X` in Python?

## Short Answer

Check that you're using the correct virtual environment and that the package is installed.

## Detailed Answer

[Explanation with examples, code snippets, troubleshooting steps]

## Common Pitfalls

- Shadowing module names with local files
- Wrong Python interpreter active
- Package installed globally but using a virtualenv

## See Also

- Entry #0045: Virtual environment setup
- Entry #0089: Understanding Python's import system
```

**For `type: guide`:**

```markdown
## Overview

[What this guide covers and who it's for]

## Prerequisites

- Python 3.8+
- Basic command line knowledge

## Steps

### 1. Setup

[Instructions]

### 2. Implementation

[Instructions with code examples]

### 3. Verification

[How to test it worked]

## Troubleshooting

[Common issues and fixes]
```

**For `type: pattern`:**

```markdown
## Pattern Name

Dependency Injection

## Problem

[What problem does this solve?]

## Solution

[The pattern, with code examples]

## When to Use

- [Scenario 1]
- [Scenario 2]

## When NOT to Use

- [Anti-pattern scenarios]

## Trade-offs

[Pros and cons, performance considerations]
```

---

## Entry Types (v0.1.0)

| Type | Purpose | Example |
|------|---------|---------|
| **q-and-a** | Specific problem with solution | "How to fix ImportError in Python" |
| **guide** | Step-by-step tutorial or how-to | "Getting started with async/await" |
| **pattern** | Reusable solution pattern or anti-pattern | "Dependency injection in Python" |
| **note** | Quick reference, definition, or concept | "Python GIL explained" |

---

## ID Convention

**Format:** `{sequential-number}-{slug}`

- **Sequential number:** `0001`, `0002`, etc. (zero-padded to 4 digits)
- **Slug:** Lowercase, hyphenated, derived from title
- **Examples:**
  - `0001-python-importerror-fix.md`
  - `0042-kubernetes-pod-lifecycle.md`

**Assignment:**
- In v0.1.0, manually assigned (check highest existing ID + 1)
- The CLI tool will auto-assign in future versions

---

## Configuration File

**`.knowledge/config.yaml`:**

```yaml
version: "0.1.0"
name: "Python Knowledge Base"
description: "Curated Python programming knowledge"
license: CC-BY-4.0
maintainers:
  - alice
  - bob
tags:
  # Optional: define canonical tag list for validation
  allowed:
    - python
    - rust
    - docker
    - kubernetes
    - async
    - testing
```

---

## CLI Tool (Minimal v0.1.0)

### Installation

```bash
# Future: cargo install knowledge-cli or pip install knowledge-cli
# For v0.1.0: simple Python script or shell script
```

### Core Commands

```bash
# Initialize a new knowledge repo
k init "My Knowledge Base"

# Create new entry (interactive)
k new q-and-a "How to fix ImportError"
# Creates entries/NNNN-how-to-fix-importerror.md with template

# Search entries
k search "asyncio"                    # Full-text search
k search --tag python --tag async     # Filter by tags
k search --type guide                 # Filter by type

# Get specific entry
k get 0001                            # Open in $EDITOR or print to stdout

# Validate repo
k validate                            # Check schema, duplicates, broken references

# Build index (for faster search)
k index                               # Generates .knowledge/index.json
```

---

## Index Format

**`.knowledge/index.json`** (auto-generated, not committed):

```json
{
  "version": "0.1.0",
  "generated": "2025-02-09T10:30:00Z",
  "entries": [
    {
      "id": "0001",
      "path": "entries/0001-python-importerror-fix.md",
      "title": "Fixing ImportError: No module named X in Python",
      "type": "q-and-a",
      "tags": ["python", "import", "troubleshooting"],
      "status": "reviewed",
      "difficulty": "beginner",
      "created": "2025-01-15",
      "updated": "2025-02-09"
    }
  ],
  "tags": {
    "python": ["0001", "0002", "0015"],
    "async": ["0002", "0008"]
  },
  "types": {
    "q-and-a": ["0001", "0003"],
    "guide": ["0002", "0004"]
  }
}
```

---

## Git Workflow

### Adding a New Entry

```bash
# 1. Create branch
git checkout -b add-asyncio-guide

# 2. Create entry
k new guide "Introduction to Python asyncio"
# Edit entries/0042-introduction-to-python-asyncio.md

# 3. Validate
k validate

# 4. Commit
git add entries/0042-introduction-to-python-asyncio.md
git commit -m "Add guide: Introduction to Python asyncio"

# 5. Push and create PR
git push origin add-asyncio-guide
# Create PR on GitHub/GitLab
```

### Updating an Entry

```bash
# 1. Edit the file
vim entries/0001-python-importerror-fix.md
# Update 'updated:' field in front matter

# 2. Commit with descriptive message
git commit -am "Update entry #0001: Add Python 3.13 compatibility notes"
```

---

## Validation Rules

The `k validate` command checks:

1. **Schema compliance:**
   - All required fields present (`id`, `title`, `type`, `tags`, `created`)
   - Valid `type` value
   - Valid date formats (YYYY-MM-DD)

2. **Uniqueness:**
   - No duplicate IDs
   - No duplicate filenames

3. **References:**
   - All `related` IDs exist
   - All internal links (`#0001`) resolve

4. **File naming:**
   - Filename matches pattern `{id}-{slug}.md`
   - ID in filename matches front matter `id`

5. **Optional warnings:**
   - Entries with no `updated` field that are >6 months old
   - Entries marked `status: draft` that are >30 days old
   - Unused tags (defined in config but not used anywhere)

---

## LLM Integration (Basic)

### Search as RAG Context

```python
# Pseudocode for RAG integration
def answer_question(user_query):
    # 1. Search knowledge base
    results = k_search(user_query, limit=5)
    
    # 2. Build context
    context = "\n\n---\n\n".join([
        f"# Entry #{r.id}: {r.title}\n{r.body}"
        for r in results
    ])
    
    # 3. Prompt LLM
    prompt = f"""Answer the user's question using these knowledge base entries:

{context}

User question: {user_query}

Provide a clear answer and cite entry IDs when relevant."""
    
    return llm.complete(prompt)
```

### Future: Embeddings (v0.2.0+)

```yaml
# In .knowledge/config.yaml
embeddings:
  model: text-embedding-3-small
  dimension: 1536
  storage: .knowledge/embeddings.db  # SQLite or vector DB
```

---

## Repository Types

### 1. Personal Knowledge Base

```text
my-knowledge/
├── entries/
│   ├── 0001-til-python-walrus-operator.md
│   ├── 0002-debugging-docker-network-issue.md
│   └── 0003-favorite-vim-plugins.md
└── meta/
    └── README.md  # "My personal TIL and troubleshooting notes"
```

**Characteristics:**
- Private repo
- Informal, personal voice
- Mix of TIL, troubleshooting, bookmarks

### 2. Team/Company Knowledge Base

```text
acme-engineering-kb/
├── entries/
│   ├── 0001-deploying-to-staging.md
│   ├── 0002-api-authentication-flow.md
│   └── 0003-oncall-runbook-database.md
└── .knowledge/
    └── config.yaml  # license: proprietary, maintainers: [sre-team]
```

**Characteristics:**
- Private or internal repo
- Formal review process (required reviewers on PRs)
- Links to internal systems

### 3. Public Domain Knowledge

```text
awesome-python-kb/
├── entries/
│   ├── 0001-python-importerror-fix.md
│   ├── 0002-asyncio-task-groups.md
│   └── ...
└── meta/
    ├── README.md
    └── CONTRIBUTING.md
```

**Characteristics:**
- Public GitHub repo
- Open contributions (PR-based)
- Permissive license (CC-BY, MIT, etc.)
- Clear contribution guidelines

---

## Example Entry (Complete)

**`entries/0001-python-importerror-fix.md`:**

```markdown
---
id: "0001"
title: "Fixing ImportError: No module named X in Python"
type: q-and-a
tags:
  - python
  - import
  - troubleshooting
  - virtualenv
created: 2025-01-15
updated: 2025-02-09
status: reviewed
difficulty: beginner
author: alice
related: ["0045", "0089"]
---

## Question

How do I fix `ImportError: No module named X` in Python?

## Short Answer

**Check that you're using the correct virtual environment** and that the package is installed in that environment. Verify with:

```bash
which python          # Shows which Python interpreter is active
python -m pip list    # Shows installed packages
```

## Detailed Answer

`ImportError` occurs when Python cannot find a module you're trying to import. The most common causes are:

### 1. Wrong Virtual Environment

You may have installed the package in one environment but are running Python from another.

**Solution:**

```bash
# Activate the correct environment
source venv/bin/activate  # Linux/Mac
# or
venv\Scripts\activate     # Windows

# Verify it's active
which python

# Install the package
python -m pip install package-name
```

### 2. Package Not Installed

The package simply isn't installed in your current environment.

**Solution:**

```bash
python -m pip install package-name
```

### 3. Name Shadowing

You have a local file with the same name as the module you're trying to import (e.g., `requests.py` in your project directory).

**Solution:**

Rename your local file to something else.

### 4. PYTHONPATH Issues

In rare cases, your `PYTHONPATH` environment variable may be misconfigured.

**Solution:**

```bash
# Check current PYTHONPATH
echo $PYTHONPATH

# Verify Python's module search paths
python -c "import sys; print('\n'.join(sys.path))"
```

## Common Pitfalls

- **Installing with `pip` but running with `pip3`:** Make sure you're using the same Python version for installation and execution
- **System Python vs. virtualenv:** Always use virtual environments for projects
- **Typos in module names:** Double-check spelling and capitalization

## See Also

- Entry #0045: Setting up Python virtual environments
- Entry #0089: Understanding Python's import system
- Entry #0102: Using `uv` as a faster pip alternative
```

---

## Out of Scope for v0.1.0

These features are intentionally deferred to keep the initial version simple:

- ❌ Vector embeddings / semantic search
- ❌ Web UI or hosted service
- ❌ Voting/reputation system
- ❌ Multi-repo aggregation
- ❌ Auto-summarization with LLMs
- ❌ Rich media (images, videos) — Markdown images are fine, but no asset management
- ❌ Comments or discussions (use GitHub issues/PRs)
- ❌ Translations or i18n

---

## Success Criteria for v0.1.0

A successful v0.1.0 means:

1. ✅ **Spec is clear:** Anyone can read this doc and create a compliant repo
2. ✅ **CLI works:** `new`, `search`, `validate`, `index` commands function
3. ✅ **Seed repo exists:** 20-30 high-quality entries in one domain (e.g., Python)
4. ✅ **Git workflow proven:** At least 3 people have contributed via PR
5. ✅ **LLM integration demo:** Simple RAG script shows retrieval + answer generation
6. ✅ **Documentation complete:** README, CONTRIBUTING, and this spec published

---

## Implementation Checklist

### Phase 1: Foundation (Week 1)
- [ ] Write `.knowledge/config.yaml` schema
- [ ] Create entry templates for each type
- [ ] Write validation logic (schema checker)
- [ ] Set up example repo with 5 entries

### Phase 2: CLI (Week 2)
- [ ] Implement `k init`
- [ ] Implement `k new` with templates
- [ ] Implement `k search` (ripgrep wrapper)
- [ ] Implement `k validate`
- [ ] Implement `k index` (JSON generation)

### Phase 3: Content & Testing (Week 3)
- [ ] Write 20-30 seed entries (Python domain)
- [ ] Dog-food: Use the repo for real questions
- [ ] Iterate on templates based on actual usage
- [ ] Write CONTRIBUTING.md

### Phase 4: LLM Demo (Week 4)
- [ ] Write simple RAG script (Python)
- [ ] Demo: "Ask a question → get grounded answer"
- [ ] Document LLM integration pattern
- [ ] Publish blog post with examples

---

## Open Questions for v0.1.0

1. **CLI implementation language:**
   - **Python:** Easier LLM ecosystem integration, slower startup
   - **Rust:** Fast, single binary, better for text processing
   - **Recommendation:** Start with **Python** for speed of development, rewrite in Rust if adoption grows

2. **ID assignment:**
   - Manual (check highest + 1) or auto-assign?
   - **Recommendation:** Manual for v0.1.0, auto in v0.2.0

3. **Tag validation:**
   - Free-form or restricted to config.yaml list?
   - **Recommendation:** Free-form with warnings for typos (Levenshtein distance)

4. **Entry file location:**
   - Flat `entries/` or allow subdirectories?
   - **Recommendation:** Flat for v0.1.0, subdirs optional in v0.2.0

---

## Next Steps

1. **Review this spec** — does it capture the essence while staying simple?
2. **Choose CLI implementation** — Python or Rust?
3. **Create seed repo** — pick a domain you know well (Python, Rust, Docker, etc.)
4. **Build MVP CLI** — focus on `new`, `validate`, `search` first
5. **Write 10 entries** — test the templates and workflow in practice
6. **Iterate based on usage** — adjust schema/templates as needed
7. **Publish v0.1.0** — repo + CLI + blog post

---

## Getting Started (For Early Adopters)

### Quick Start: Create Your First Repo

```bash
# 1. Clone the template (or create from scratch)
git clone https://github.com/username/knowledge-repo-template my-knowledge
cd my-knowledge

# 2. Initialize
k init "My Knowledge Base"

# 3. Create your first entry
k new q-and-a "How to set up SSH keys"

# 4. Edit the generated file
# entries/0001-how-to-set-up-ssh-keys.md

# 5. Validate and commit
k validate
git add .
git commit -m "Initial entry: SSH key setup"
```

### Quick Start: Search and Use

```bash
# Full-text search
k search "docker networking"

# Filter by tags
k search --tag docker --tag troubleshooting

# Get a specific entry
k get 0042

# Rebuild index after adding entries manually
k index
```

---

## File Size & Performance Targets

**v0.1.0 should handle:**

- **1,000 entries** with sub-second search
- **10,000 entries** with <3 second index rebuild
- **Repository size:** <50MB for 1,000 text entries
- **CLI startup time:** <100ms (Rust) or <500ms (Python)

---

## License Recommendations

For the **specification itself:**
- **CC0 (Public Domain)** — anyone can implement it

For **knowledge repos:**
- **Personal/private:** Any license or none
- **Public community repos:** **CC-BY-4.0** (attribution required) or **CC-BY-SA-4.0** (share-alike)
- **Company internal:** Proprietary or internal-only

For the **CLI tool:**
- **MIT** or **Apache-2.0** (permissive, allows commercial use)

---

## Community & Governance (Post-Launch)

### Contribution Model

**For the spec:**
- GitHub issues for proposals
- RFC-style process for major changes
- Semantic versioning (v0.1.0 → v0.2.0 → v1.0.0)

**For seed repos:**
- Standard PR workflow
- Required: passes `k validate`
- Recommended: 1-2 reviewer approvals
- Maintainers have merge rights

### Quality Guidelines

**Accept entries that:**
- ✅ Solve a real problem or answer a real question
- ✅ Include working examples
- ✅ Cite sources when appropriate
- ✅ Use clear, concise language

**Reject/request changes for:**
- ❌ Duplicate content (unless significantly different perspective)
- ❌ Outdated information marked as current
- ❌ Promotional content or spam
- ❌ Plagiarized content without attribution

---

## Comparison to Alternatives

| Feature             | Knowledge Repo v0.1    | StackOverflow        | Notion/Confluence     | Obsidian                  |
|---------------------|------------------------|----------------------|-----------------------|---------------------------|
| **Offline-first**   | ✅ Full functionality  | ❌ Requires internet | ❌ Requires internet  | ✅ Yes                    |
| **Version control** | ✅ Git native          | ❌ No                | ⚠️ Limited history     | ⚠️ Via plugins             |
| **LLM-ready**       | ✅ Structured metadata | ❌ HTML scraping     | ❌ Proprietary format | ⚠️ Markdown but no schema  |
| **Forkable**        | ✅ Git clone/fork      | ❌ No                | ❌ No                 | ⚠️ Manual sync             |
| **Collaboration**   | ✅ PR-based            | ✅ Community voting  | ✅ Real-time editing  | ⚠️ Sync plugins            |
| **Search**          | ✅ ripgrep, tags       | ✅ Excellent         | ✅ Good               | ✅ Good                   |
| **Cost**            | ✅ Free                | ✅ Free (paid teams) | 💰 Paid               | ✅ Free (paid sync)       |

**Unique advantages:**
- **Distributed ownership:** No single point of failure or vendor lock-in
- **Tool agnostic:** Use any editor, any LLM, any search tool
- **Composable:** Merge multiple repos, create overlays, build custom tooling

---

## Example Repos to Inspire v0.1.0

### 1. `python-knowledge`
**Focus:** Python programming Q&A and patterns

**Sample entries:**
- `0001-python-importerror-fix.md`
- `0002-asyncio-task-groups.md`
- `0003-type-hints-best-practices.md`
- `0004-pytest-fixtures-guide.md`
- `0005-virtual-environment-setup.md`

**Tags:** `python`, `async`, `testing`, `types`, `packaging`, `virtualenv`

### 2. `devops-runbooks`
**Focus:** Infrastructure troubleshooting and operations

**Sample entries:**
- `0001-kubernetes-pod-crashloop.md`
- `0002-docker-network-debugging.md`
- `0003-ssl-certificate-renewal.md`
- `0004-database-backup-restore.md`

**Tags:** `kubernetes`, `docker`, `ssl`, `database`, `troubleshooting`, `oncall`

### 3. `web-dev-patterns`
**Focus:** Web development patterns and anti-patterns

**Sample entries:**
- `0001-rest-api-versioning.md`
- `0002-jwt-vs-session-auth.md`
- `0003-cors-explained.md`
- `0004-rate-limiting-strategies.md`

**Tags:** `api`, `authentication`, `security`, `performance`, `architecture`

---

## Extensibility Points (For Future Versions)

The v0.1.0 design intentionally leaves room for:

### 1. Custom Entry Types
```yaml
# In .knowledge/config.yaml
entry_types:
  - q-and-a
  - guide
  - pattern
  - note
  - runbook        # Custom type
  - architecture   # Custom type
```

### 2. Custom Fields
```yaml
# In entry front matter
custom:
  severity: critical      # For runbooks
  estimated_time: 30min   # For guides
  prerequisites: ["0001", "0002"]
```

### 3. Plugins/Extensions
```yaml
# In .knowledge/config.yaml
plugins:
  - name: embeddings
    config:
      model: text-embedding-3-small
  - name: auto-summarize
    config:
      model: gpt-4o-mini
      on_commit: true
```

### 4. Multi-Repo Aggregation
```yaml
# In .knowledge/config.yaml
imports:
  - url: https://github.com/org/python-kb
    prefix: "python-"     # IDs become python-0001, python-0002
  - url: https://github.com/org/rust-kb
    prefix: "rust-"
```

---

## Metrics for Success (6 Months Post-Launch)

**Adoption:**
- 🎯 **100+ stars** on reference implementation repo
- 🎯 **10+ community repos** following the spec
- 🎯 **50+ contributors** across all repos

**Usage:**
- 🎯 **5,000+ entries** across public repos
- 🎯 **10+ organizations** using it internally
- 🎯 **3+ tool integrations** (VSCode, Raycast, etc.)

**Quality:**
- 🎯 **90%+ entries** pass validation
- 🎯 **Average entry age** <6 months (shows active maintenance)
- 🎯 **50%+ entries** have `status: reviewed`

---

## Critical Path to v0.1.0 Release

### Must Have (Blockers)
1. ✅ Complete specification document (this doc)
2. ✅ Entry schema finalized
3. ✅ CLI with `new`, `search`, `validate`, `index`
4. ✅ 20+ seed entries in one domain
5. ✅ README and CONTRIBUTING docs
6. ✅ Basic LLM integration example

### Should Have (High Priority)
1. ⚠️ Entry templates for all 4 types
2. ⚠️ Validation with helpful error messages
3. ⚠️ Git hooks for auto-validation
4. ⚠️ CLI published (PyPI or crates.io)

### Nice to Have (Can Defer)
1. 💡 VSCode snippets for entry creation
2. 💡 GitHub Action for PR validation
3. 💡 Multiple domain seed repos (Python + Rust + Docker)
4. 💡 Video walkthrough / demo

---

## Final Recommendation: CLI Implementation

**Start with Python for v0.1.0:**

**Pros:**
- ✅ Faster to implement (2-3 weeks vs 4-6 weeks)
- ✅ Easier LLM integration (OpenAI SDK, etc.)
- ✅ Rich ecosystem for Markdown parsing
- ✅ Lower barrier for contributors

**Cons:**
- ⚠️ Slower startup time (~300-500ms)
- ⚠️ Requires Python installation

**Architecture:**
```python
knowledge/
├── cli/
│   ├── __init__.py
│   ├── commands/
│   │   ├── init.py
│   │   ├── new.py
│   │   ├── search.py
│   │   ├── validate.py
│   │   └── index.py
│   ├── models.py        # Entry, Config classes
│   ├── templates.py     # Entry templates
│   └── utils.py         # File I/O, YAML parsing
├── tests/
└── setup.py
```

**Dependencies:**
- `click` — CLI framework
- `pyyaml` — YAML parsing
- `python-frontmatter` — Markdown front matter
- `rich` — Beautiful terminal output

**Installation:**
```bash
pip install knowledge-cli
# or
pipx install knowledge-cli  # Isolated install
```

---

## Closing Thoughts

**This v0.1.0 spec prioritizes:**
1. ✅ **Clarity** — Anyone can implement a compliant repo
2. ✅ **Simplicity** — Minimal required fields and structure
3. ✅ **Practicality** — Solves real problems (findability, collaboration, LLM integration)
4. ✅ **Extensibility** — Clear path to v0.2.0, v1.0.0 without breaking changes

**The core insight remains:**
> Git + Markdown + structured metadata = a powerful, distributed, LLM-ready knowledge system

**Next action:** Pick a domain you know well, create 10 entries by hand following this spec, and see what breaks or feels awkward. That real-world usage will validate (or refine) these decisions.

Ready to build? 🚀
