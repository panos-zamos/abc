# Phase 2 — Extractor & Graph Builder (Implementation Plan)

## Goal
Plan the PHP batch extractor and Go graph builder implementation details without writing code yet.

## Steps
1. **PHP Extractor design**
   - Select traversal approach with `nikic/php-parser`.
   - Outline visitor pattern and symbol extraction steps.
   - Define edge extraction rules per AST node.
   - Define output file strategy (one JSON per source file or merged stream).
2. **Go graph builder design**
   - Parse JSON dumps.
   - Normalize symbol IDs; map to integer IDs.
   - Build forward & reverse adjacency lists.
3. **Persistence strategy**
   - Bulk insert nodes and edges into SQLite.
   - Index creation and transaction strategy.
4. **CLI wiring**
   - Map `build` command to: read JSON dumps → build graph → persist.
   - Map `parse` command to: scan project → output JSON dumps.

## Deliverables
- Detailed extraction rules per edge type.
- Data flow diagram (text) from PHP → JSON → Go → SQLite.
- CLI command behavior and exit codes.
