# Project Mind Map

## Instructions

> **🎯 PRIME DIRECTIVE FOR AI AGENTS:**
> This is your primary knowledge index. Always start here. Read overview nodes [1-5] first, then follow `[N]` links. Cite node IDs when referencing information. **Update this file as you work**—it's your source of truth.

This is a graph-based documentation format stored as plain text where each node is a single line containing an ID, title, and inline references.

**Node Format:** `[N] **Node Title** - description with [N] references`

Each node occupies exactly one line (use `\n` escapes for internal line breaks if needed). This enables:
- **Atomic updates:** Replace line N to update node N
- **Grep lookup:** `grep "^\[42\]"` finds node 42 instantly
- **Diff-friendly:** Changes affect only the lines you edit
- **LLM-native:** Citation syntax `[N]` mirrors academic papers

The format supports many-to-many relationships, cyclic references, cross-cutting concerns, and progressive refinement making it suitable for trees, lists, or arbitrary graphs.

**Update Protocol (MANDATORY):**
- **Before starting:** `grep -i "keyword"` to find related nodes → read them
- **After changes:** Update affected nodes immediately (not "later")
- **New concepts:** Add a node only if referenced 3+ times OR non-obvious from code
- **Bug fixes:** Create `[N] **BUG: [Title]**` node with root cause + solution + commit hash (if exists)
- **Deprecation:** Mark as `[N] **[DEPRECATED → N_new] Old Title** - ...` (keep the line, redirect it)
- **Staleness:** If you suspect a node is outdated, mark `(verify YYYY-MM-DD)` and fix within 2 commits

⚠️ **If the mindmap contradicts reality, reality wins—but update the mindmap immediately.**

**Node Conventions:**
- `**AE: X**` = architecture elements
- `**WF: X**` = workflow
- `**DR: X**` = decision record
- `**BUG: X**` = bugs and regressions
- `**TODO: X**` = planned work

**Scale:** Keep under 50 nodes for small projects, under 100 for medium. If you exceed 100, split into domain-specific maps - i.e. MINDMAP.domain-concept.md, and reference it from here.

**Cross-File Links:**
- For large projects (>100 nodes), split by domain: `MINDMAP.auth.md`, `MINDMAP.payments.md`
- Link from main mindmap: `[15] **AE: Authentication System** - See MINDMAP.auth.md for details. Overview: JWT-based, refresh tokens in httpOnly cookies [12][22]`
- Each sub-mindmap follows the same format and starts with its own [1-5] overview nodes (when creating the file, include the `## Instructions` block).

**Example Node Lifecycle:**

Initial:
[12] **AE: AuthService** - Handles JWT validation using jsonwebtoken library [15][22]

After refactoring:
[12] **AE: AuthService** - Handles JWT validation using Passport.js [15][22][31] (updated 2026-02-02)

After deprecation:
[12] **[DEPRECATED → 45] AE: AuthService** - Old JWT service, replaced by PassportAuthService [45]

---

## Nodes

[1] **Project Purpose** - [One sentence: what does this codebase do?]

[2] **Tech Stack** - [Languages, frameworks, key libraries]

[3] **Entry Points** - [Where does execution start? Main files, API routes]

[4] **Architecture** - [High-level: monolith/microservices, layers, data flow]

[5] **Key Decisions** - [Top 3 non-obvious choices and why]

[6] **AE: [Name]** - ...

[7] **WF: [Name]** - ...

[8] **DR: [Name]** - ...

[9] **TODO: [Name]** - ...
