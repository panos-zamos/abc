# Phase 3 — Query CLI (Implementation Plan)

## Goal
Plan the query CLI that reads the persisted graph and answers dependency questions quickly.

## Steps
1. **Query CLI design**
   - `symbol <name>`: return node details.
   - `dependencies <name> --depth N`: forward traversal.
   - `dependents <name> --depth N`: reverse traversal.
   - `subgraph <name> --depth N`: combined traversal.
2. **Traversal strategy**
   - BFS with depth cap.
   - Avoid repeated nodes; allow `--max-nodes` safeguard.
3. **Output format**
   - JSON only with explicit fields (`nodes`, `edges`, `depth`).
4. **Performance considerations**
   - Prepared statements for edges queries.
   - Batch retrieval with `IN` queries or iterative BFS with indexes.

## Deliverables
- CLI query spec (flags + JSON outputs).
- Traversal algorithm notes.
