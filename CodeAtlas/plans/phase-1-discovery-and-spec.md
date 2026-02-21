# Phase 1 — Discovery & Spec (CLI-first)

## Goal
Define the CLI interface, data contracts, and storage schema for a PHP dependency graph builder.

## Steps
1. **Confirm CLI requirements**
   - CLI entry: `codeatlas`.
   - Primary commands:
     - `codeatlas parse` (PHP extractor)
     - `codeatlas build` (Go graph builder)
     - `codeatlas query` (graph queries)
   - Confirm input/output conventions for each command.
2. **Define CLI query surface (replacing HTTP)**
   - CLI subcommands:
     - `symbol <name>`
     - `dependencies <name> --depth N`
     - `dependents <name> --depth N`
     - `subgraph <name> --depth N`
   - Output format: JSON only.
3. **Define JSON contract for PHP parser output**
   - One JSON file per source file.
   - Confirm file-level JSON structure.
   - Confirm node/edge payloads and edge types.
   - Confirm symbol naming rules (FQCN + member syntax).
4. **Define storage schema (SQLite)**
   - Confirm nodes/edges schema and indexes.
   - Decide any metadata tables (e.g., `files`, `meta`).
5. **Define graph model & ID normalization**
   - Canonical symbol IDs (case-sensitivity, namespace separators, method delimiter).
6. **Scope MVP vs. later phases**
   - Node types limited to: class, interface, trait, method, function.
   - Explicitly list MVP extraction features and omissions.

## Deliverables
- CLI command spec (names, flags, input/output).
- JSON contract for extractor output.
- SQLite schema definition.
- Symbol naming normalization rules.
- MVP scope list.
