# Phase 4 — Incremental Updates (Future)

## Goal
Plan incremental re-parsing and graph updates.

## Steps
1. **File hashing strategy**
   - Hash per file (content or mtime + size).
   - Track in SQLite `files` table.
2. **Change detection**
   - Compare hashes to decide re-parse.
3. **Graph update**
   - Remove old edges/nodes for changed files.
   - Insert new edges/nodes.
4. **CLI command**
   - Add `update` command to reindex changed files only.

## Deliverables
- Incremental update design notes.
- Proposed schema changes.
