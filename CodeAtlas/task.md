# 🎯 Goal

* Parse PHP project once
* Extract symbols + edges
* Build persistent dependency graph
* Query fast (dependencies / dependents / transitive)
* Incremental updates later

No LSP. No live typing support.

---

# 🧱 High-Level Architecture

```
                 ┌─────────────────────┐
                 │   PHP Codebase      │
                 └─────────┬───────────┘
                           │
                           ▼
              ┌─────────────────────┐
              │ PHP Parser Worker   │  (CLI batch job)
              │ (nikic/php-parser)  │
              └─────────┬───────────┘
                        │ JSON symbol dump
                        ▼
              ┌─────────────────────┐
              │  Go Graph Builder   │
              │  (index + persist)  │
              └─────────┬───────────┘
                        │
                        ▼
              ┌─────────────────────┐
              │ Graph Storage       │
              │ (SQLite or BoltDB)  │
              └─────────┬───────────┘
                        │
                        ▼
              ┌─────────────────────┐
              │ Query API (HTTP)    │
              │ For AI agents       │
              └─────────────────────┘
```

---

# 🧩 Component Breakdown

## 1️⃣ PHP Parser Worker (Batch Extractor)

Use:

* `nikic/php-parser`
* Composer autoload map

Responsibilities:

* Parse each file
* Extract:

  * FQCN
  * Methods
  * Functions
  * Properties
  * Traits
  * Interfaces
  * Imports (`use`)
  * Constructor param types
  * Method param/return types
  * Static method calls
  * Instantiations (`new`)
  * Class constant references

Output per file:

```json
{
  "file": "src/UserService.php",
  "symbols": [...],
  "edges": [
    { "from": "App\\UserService", "to": "App\\UserRepository", "type": "constructor_param" },
    { "from": "App\\UserService::create", "to": "App\\UserRepository::save", "type": "call" }
  ]
}
```

Important:
Do not attempt deep type inference for MVP.

---

## 2️⃣ Go Graph Builder

Responsibilities:

* Normalize symbol IDs
* Assign integer IDs (faster graph ops)
* Build:

  * forward adjacency list
  * reverse adjacency list
* Persist to disk

### In-Memory Model

```go
type NodeID int

type Graph struct {
    Forward  map[NodeID][]Edge
    Reverse  map[NodeID][]Edge
}
```

Store both directions. This makes dependents instant.

---

## 3️⃣ Storage Layer

For MVP:

### Option A — SQLite (recommended)

Simple schema:

```sql
nodes(id INTEGER PRIMARY KEY, name TEXT UNIQUE, type TEXT)
edges(from_id INTEGER, to_id INTEGER, type TEXT)
```

Add indexes:

```
CREATE INDEX idx_from ON edges(from_id);
CREATE INDEX idx_to ON edges(to_id);
```

Advantages:

* Durable
* Easy introspection
* ACID
* No custom file format needed

---

## 4️⃣ Query API for AI Agents

Expose:

```
GET /symbol/{name}
GET /dependencies/{name}?depth=1
GET /dependents/{name}?depth=1
GET /subgraph/{name}?depth=3
GET /cycles
```

All queries operate on persistent graph.

No re-parsing during queries.

---

# 📦 Data Model (MVP Scope)

## Node Types

* class
* interface
* trait
* method
* function
* file (optional)

## Edge Types

Keep it minimal:

* extends
* implements
* uses_trait
* constructor_param
* method_call
* property_type
* return_type
* instantiation

That’s enough for 80% structural reasoning.

---

# ⚙️ Performance Expectations

### Parsing phase

* 1M LOC → few minutes in PHP
* Run as batch job
* Can parallelize per file (multi-process)

### Graph build

* Likely 200k–500k edges
* Go handles easily in memory
* Query latency: milliseconds

---

# 🔁 Incremental Strategy (Phase 2)

Add:

* File hash tracking
* Only re-parse changed files
* Remove edges from old version
* Reinsert updated edges

You don’t need full reindexing.

---

# 🧠 What This Enables for AI

Agent can now ask:

* “If I modify X, what breaks?”
* “What are transitive dependents?”
* “Show architectural layer violations”
* “Detect circular dependencies”
* “Find God objects”

This becomes an **AI reasoning substrate**, not a coding assistant.

---

# ⚠️ Important Design Discipline

Do not:

* Over-model AST
* Attempt runtime resolution
* Implement full type inference
* Build LSP features

You’re building a **structural graph**, not a compiler.

---

# Rough Scale Estimation

For 1M LOC:

* ~50–150k classes
* ~300–500k edges
* Graph memory footprint in Go: < 500MB easily
* SQLite DB size: maybe 100–300MB

Comfortable.

---

# Bottom Line

For MVP:

> PHP = extraction layer
> Go = graph brain
> SQLite = persistence
> HTTP = agent interface

Clean separation. Predictable performance. Extensible.

---

