# Project Mind Map

[0] **🎯 PRIME DIRECTIVE FOR AI AGENTS:** This mindmap is your primary knowledge index. Read nodes [1-9] first (they explain the system), then read overview nodes [10-14] for project context. Follow `[N]` links to navigate. **Always update this file as you work.**

[1] **Meta: Mind Map Format** - This is a graph-based documentation format where each node is one line: `[N] **Title** - content with [N] references`. The format is homoiconic—these instructions are themselves nodes demonstrating the format [2][3][4]. Nodes enable atomic line-by-line updates, grep-based search, VCS-friendly diffs, and LLM-native citation syntax [5][6].

[2] **Meta: Node Syntax** - Format is `[N] **Title** - description with [N] references`. Each node is exactly one line (use `\n` for internal breaks if needed). Titles use markdown bold `**...**`. References use citation syntax `[N]` which LLMs recognize from academic papers [1][3]. Node IDs are sequential integers starting from 1.

[3] **Meta: Node Types** - Nodes are prefixed by type: `**AE: X**` (Architecture Element), `**WF: X**` (Workflow), `**DR: X**` (Decision Record), `**BUG: X**` (Bug Record), `**TODO: X**` (Planned Work), `**Meta: X**` (Documentation about this mindmap itself) [1][2][4]. Use `**[DEPRECATED → N]**` prefix for outdated nodes that redirect elsewhere [6].

[4] **Meta: Quick Start for New Agents** - First time here? (1) Read [1-9] to understand the format, (2) Read [10-14] for project overview, (3) Grep for your task: `grep -i "auth"` then read matching nodes, (4) Follow `[N]` links to dive deeper, (5) Update nodes as you work per protocol [6][7][8].

[5] **Meta: Why This Format Works** - Line-oriented structure allows atomic updates (replace line N to update node N), instant grep lookup (`grep "^\[42\]"` finds node 42), diff-friendly changes (only edited lines change), and zero parsing overhead [1][2]. The `[N]` citation syntax leverages LLM training on academic papers—agents already know how to follow references [3].

[6] **Meta: Update Protocol** - **MANDATORY:** (1) Before starting work, grep for related nodes and read them [4], (2) After making changes, update affected nodes immediately, (3) Add new nodes only if concept is referenced 3+ times OR non-obvious from code, (4) For bug fixes create `**BUG:**` node with root cause + solution + commit hash [3], (5) For deprecation use `**[DEPRECATED → N_new]**` prefix and keep the line [3], (6) If node seems outdated mark `(verify YYYY-MM-DD)` and fix within 2 commits [7][8].

[7] **Meta: Node Lifecycle Example** - Initial: `[12] **AE: AuthService** - Handles JWT validation using jsonwebtoken [15][22]`. After refactor: `[12] **AE: AuthService** - Handles JWT validation using Passport.js [15][22][31] (updated 2026-02-02)`. After deprecation: `[12] **[DEPRECATED → 45] AE: AuthService** - Replaced by PassportAuthService [45]` [6][3].

[8] **Meta: Reality vs Mindmap** - **Critical rule:** If the mindmap contradicts the actual codebase, the code is the source of truth—but you must update the mindmap immediately to reflect reality [6]. The mindmap is an index, not a specification. Stale nodes are worse than missing nodes because they mislead future agents.

[9] **Meta: Scaling Strategy** - Small projects: <50 nodes. Medium: <100 nodes. Large: split into domain-specific files like `MINDMAP.auth.md`, `MINDMAP.payments.md` [10]. Link from main mindmap: `[15] **AE: Auth System** - See MINDMAP.auth.md for details. Uses JWT [12][22]`. Each sub-mindmap has its own [1-9] meta nodes and [10-14] overview nodes following this same format [1][3].

---

[10] **Project Purpose** - [One sentence: what does this codebase do?]

[11] **Tech Stack** - [Languages, frameworks, key libraries with versions]

[12] **Entry Points** - [Where does execution start? Main files, API routes, CLI commands]

[13] **Architecture** - [High-level: monolith/microservices, layers, data flow, key patterns]

[14] **Key Decisions** - [Top 3-5 non-obvious choices and why, reference DR nodes for details]

---

[15] **AE: [ComponentName]** - [Description with references [N][M]]

[16] **WF: [ProcessName]** - [Step-by-step description with references [N][M]]

[17] **DR: [DecisionTitle]** - [What was chosen, why, alternatives considered [N][M]]

[18] **BUG: [IssueTitle]** - [Root cause, fix applied, commit hash, date fixed [N][M]]

[19] **TODO: [TaskTitle]** - [What needs doing, blockers, priority, owner if known [N][M]]
