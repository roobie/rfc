# Project MINDMAP

[0] **META: 🎯 PRIME DIRECTIVE FOR AI AGENTS:** - This MINDMAP documents the codebase. Read nodes [1-9] for format rules, then [10-14] for project overview. Follow `[N]` references to navigate. **Always update this file as you work.**

[1] **META: Mind Map Format** - This is a graph-based documentation format where each node is exactly one line: `[N] **Title** - content with any number of [N] references`. The format is markdown-compatible, grep-able, and git-friendly [2][3][4]. Nodes enable atomic updates, instant search, and LLM-native citation syntax [5][6].

[2] **META: Node Syntax** - Format is `[N] **Title** - description with [N] references`. Each node is exactly one line. Titles use markdown bold `**...**`. References use citation syntax `[N]` which LLMs recognize from academic papers [1][3].

[3] **META: Node Types** - Nodes are prefixed by type: `**AE: X**` (Architecture Element), `**WF: X**` (Workflow), `**DR: X**` (Decision Record), `**BUG: X**` (Bug Record), `**TODO: X**` (Planned Work), `**DOING: X**` (Work under way), `**DONE: X**` (Work completed), `**META: X**` (Documentation about this mindmap) [1][2][4].

[4] **META: Quick Start for Agents** - First time? (1) Read [1-9] for format, (2) Read [10-14] for project overview, (3) Grep for your task: `grep -i "llm"` then read matching nodes, (4) Follow `[N]` links, (5) Update nodes as you work per protocol [6][7][8].

[5] **META: Why This Format Works** - Line-oriented = atomic updates (replace line N to update node N), instant grep (`grep "^\[42\]"`), diff-friendly (only edited lines change), zero parsing overhead [1][2]. The `[N]` citation syntax leverages LLM training on academic papers [3].

[6] **META: Update Protocol** - **MANDATORY:** (1) Before work, grep for related nodes and read them [4], (2) After changes, update affected nodes immediately, (3) Add new nodes if concept is referenced 3+ times OR non-obvious from code, (4) For bugs create `**BUG:**` node with root cause + solution [3][7].

[7] **META: Node Lifecycle Example** - Initial: `[15] **AE: LLM Client** - Uses OpenAI SDK [20][25]`. After change: `[15] **AE: LLM Client** - Uses OpenAI SDK, supports local models [20][25][27] (updated 2026-02-02)` [6][3].

[8] **META: Reality vs Mindmap** - **Critical:** If MINDMAP contradicts code, code is truth—update MINDMAP immediately [6]. This MINDMAP is an index, not a spec. Stale nodes are worse than missing nodes.

[9] **META: Scaling Strategy** - Current project: <100 nodes. If exceeds 100, split into domain files like `MINDMAP.llm.md`, `MINDMAP.execution.md` [10]. Link from main: `[15] **AE: LLM System** - See MINDMAP.llm.md for details` [1][3].

---

[11] **WF: MINDMAP interactions** - VERY IMPORTANT💯: it is **mandatory** to use the program `mindmap-cli` to interact with MINDMAP files. I.e. querying, reading and updating shall be done by invoking `mindmap-cli`. It is forbidden to update METADATA.md directly. Learn how to use this tool by invoking `mindmap-cli help`. Required reading: [PROTOCOL_MINDMAP.md](./PROTOCOL_MINDMAP.md)
