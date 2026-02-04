# MINDMAP: Graph-Based Documentation for Codebases

## Overview

MINDMAP is a specialized documentation format designed for codebases, particularly those worked on by AI agents. It provides a graph-based structure where each piece of information is a "node" connected via references, making it easy to navigate, search, and maintain. This format is inspired by mind maps and academic citation systems, optimized for grep, git, and AI interactions.

The primary goal of MINDMAP is to document the codebases in a way that's atomic, searchable, and evolution-friendly. Unlike traditional prose documentation, MINDMAP emphasizes structured, linked knowledge that can be updated surgically without disrupting the whole document.

## How It Works

### Node Format
Each node is exactly one line in the format:
```
[N] **Type: Title** - Description with [references] to other nodes.
```

- **[N]**: A unique numeric ID for the node.
- **Type**: Prefix indicating the node category, such as:
  - **AE**: Architecture Element (e.g., components, modules).
  - **WF**: Workflow (processes, procedures).
  - **DR**: Decision Record (choices made and why).
  - **BUG**: Bug Record (issues and fixes).
  - **TODO/DOING/DONE**: Work items.
  - **META**: Documentation about MINDMAP itself.
- **Title**: A bolded, concise name.
- **Description**: Free text, possibly with [N] references to other nodes.
- **References**: Citations like [5] that link to related nodes, enabling a graph structure.

### Key Benefits
- **Atomic Updates**: Change one node without affecting others.
- **Instant Search**: Use `grep` to find nodes by ID, type, or keyword.
- **Git-Friendly**: Only modified lines change in diffs.
- **AI-Native**: References use syntax LLMs recognize from academic papers.
- **Validation**: Ensures format integrity and cross-reference consistency.

### Scaling
For small projects (<100 nodes), use a single `MINDMAP.md` file. For larger ones, split into domain-specific files (e.g., `MINDMAP.llm.md`) and link from the main file.

## Interaction Protocol

All interactions with MINDMAP files **must** be done through the `mindmap-cli` tool. Direct edits to `MINDMAP.md` are forbidden to maintain integrity.

### Getting Started
1. Ensure `mindmap-cli` is installed and on your PATH.
2. Set your `EDITOR` environment variable for interactive editing.

### Common Commands
- **Inspect**: `mindmap-cli show <id>` to view a node; `mindmap-cli search <term>` to find nodes; `mindmap-cli lint` to check for issues.
- **Add**: `mindmap-cli add --type WF --title "Example" --desc "Description [refs]"`
- **Update**: `mindmap-cli patch <id> --desc "New desc"`
- **Delete**: First redirect references, then `mindmap-cli delete <id>`
- **Edit**: `mindmap-cli edit <id>` opens your editor for manual changes.

### Workflow
1. Plan your changes.
2. Use `mindmap-cli` commands to make updates.
3. Validate with `mindmap-cli lint` and check references.
4. Commit changes to git.

### For AI Agents
- Always read relevant nodes before working.
- Update nodes immediately after code changes.
- If MINDMAP conflicts with code, update MINDMAP to match.

## Files
- `@MINDMAP.md`: The main mindmap file for the codebase.
- `@PROTOCOL_MINDMAP.md`: Detailed protocol for using `mindmap-cli`.

This system ensures documentation stays accurate, linked, and maintainable as the codebase evolves.
