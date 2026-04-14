---
name: system-design-skill-wiki
description: Query the system design decision wiki when the user asks for architectural guidance on databases, queues, caching, patterns, deployment, or observability. Includes real production case studies (Discord, Notion, etc.) with explicit trade-offs and decision drivers. Start with domain-specific index.md files; use tags to filter by concept.
---

# System Design Skill Wiki — Agent Integration Guide

This is a queryable knowledge base of system design decisions captured as **explicit trade-offs** with links to primary sources. Use this skill when:

- The user asks "Should we use X or Y for [workload]?"
- The user needs a decision brief on a technology (PostgreSQL, Cassandra, Kafka, Redis, etc.)
- You’re planning architecture and need to surface hidden assumptions or gotchas
- You need real production examples (Discord, Notion, Uber, Netflix, etc.)

---

## Quick Start for Agents

### 1. Query by domain

Each domain has an `index.md` that lists all decisions + case studies in that area.

```
GET: wiki/databases/index.md
→ Returns: List of all database decisions, tools, and trade-offs
→ Agent scans for relevant links based on user requirement

GET: wiki/queues/index.md
→ Returns: All queue + event-driven decisions

GET: wiki/patterns/index.md
→ Returns: Architecture patterns (sharding, replication, CQRS, etc.)
```

### 2. Query by tag

All pages are tagged consistently. Search across the wiki for tags matching the user’s problem.

```
Common search patterns:

$ grep -r "tags:.*scaling" wiki/
→ Find all pages related to scaling decisions

$ grep -r "tags:.*wide-column" wiki/
→ Find all wide-column (Cassandra/ScyllaDB) pages

$ grep -r "tags:.*case-summary" wiki/
→ Find all real production case studies

$ grep -r "type: decision" wiki/
→ Find all decision pages (vs tool pages)
```

**Canonical tags:** See `schema/tags.md` for the complete registry. Key ones:
- **Technologies:** `cassandra`, `postgresql`, `mongodb`, `redis`, `kafka`, `rabbitmq`
- **Concepts:** `scaling`, `sharding`, `performance`, `wide-column`, `distributed-systems`
- **Page types:** `decision`, `tool`, `pattern`, `case-summary`, `index`
- **Domains:** `databases`, `queues`, `patterns`, `caching`, `deployment`, `observability`

### 3. Common agent queries

**User: "We need to store 100M messages/day with sub-20ms p99 latency. What database?"**

Agent workflow:
```
1. Load wiki/databases/index.md
2. Scan for "append-heavy" section → find [[cassandra]] and [[scylladb]]
3. Read wiki/databases/cassandra.md (tool page) for:
   - What it optimizes for
   - When NOT to use (gotchas)
   - Trade-offs table
4. Read wiki/case-summaries/discord-message-storage.md
   - Real example: Discord evolution (MongoDB → Cassandra → ScyllaDB)
   - Performance signals (p99 latency before/after)
   - Data model that worked
5. Return structured brief:
   ┌─────────────────────────────────────────┐
   │ Recommendation: Cassandra or ScyllaDB   │
   │                                         │
   │ Why: Append-heavy + range scans         │
   │ + extreme scale = wide-column model     │
   │                                         │
   │ Trade-offs:                             │
   │ • Operational: token ranges, compaction│
   │ • Consistency: eventual (multi-DC hard)│
   │                                         │
   │ Real case: Discord (see link below)    │
   └─────────────────────────────────────────┘
```

**User: "What are the gotchas with PostgreSQL at scale?"**

Agent workflow:
```
1. Load wiki/databases/postgresql.md
2. Scan "Operational gotchas" section
   - VACUUM/autovacuum + bloat
   - Schema migrations at scale
   - Replication lag + failover
3. Check "Real-world examples" → [[notion-postgres-sharding]]
4. Read wiki/case-summaries/notion-postgres-sharding.md
   - What triggered the move (VACUUM stalling)
   - How they solved it (application-level sharding)
5. Return: "VACUUM is the bottleneck at scale; watch for
           bloat + autovacuum stalling. Notion sharded
           their fleet when this became an on-call burden."
```

**User: "When should we migrate from MongoDB?"**

Agent workflow:
```
1. Look for: wiki/databases/when-to-leave-mongodb.md
   (a decision page, not a tool page)
2. Scan "Context" + "Decision drivers" sections
   - When does the working set exceed RAM?
   - How do you know it’s happening? (latency signals)
   - What’s the alternative?
3. Return structured brief with:
   - The specific signal (working set > RAM)
   - The consequence (random disk I/O → tail latency cliff)
   - Migration options (Cassandra, PostgreSQL, etc.)
```

---

## Page Structure & Queries

### Tool pages (one per technology)

**Filename:** `wiki/<domain>/<tool-name>.md`
**Example:** `wiki/databases/cassandra.md`, `wiki/queues/kafka.md`

**Sections:**
- What it is / what it optimizes for
- When it fits / doesn’t fit
- Operational gotchas
- Trade-offs table
- Related tools (via wikilinks)

**Agent query:** Use when user says "Tell me about X"

### Decision pages (trade-offs, migrations)

**Filename:** `wiki/<domain>/<decision-name>.md`
**Example:** `wiki/databases/when-to-leave-mongodb.md`, `wiki/patterns/sharding-strategy.md`

**Sections:**
- TL;DR
- Problem + context
- Decision drivers
- Options table (option A vs option B vs option C)
- Recommendation + consequences

**Agent query:** Use when user says "Should we X or Y?"

### Case-summary pages (real production stories)

**Filename:** `wiki/case-summaries/<company>-<decision>.md`
**Example:** `wiki/case-summaries/discord-message-storage.md`, `wiki/case-summaries/notion-postgres-sharding.md`

**Sections:**
- TL;DR
- Timeline / evolution
- Decision drivers
- What worked / what hurt
- Links back to tool/decision pages

**Agent query:** Use when user says "Show me a real example" or "Has anyone done this?"

### Domain index pages (navigation)

**Filename:** `wiki/<domain>/index.md`
**Example:** `wiki/databases/index.md`, `wiki/queues/index.md`

**What it contains:**
- One-line summary of the domain
- List of all tools in this domain (with wikilinks)
- Quick "by problem" matrix (e.g., "append-heavy" → Cassandra)
- Case studies that apply to this domain
- Links to related domains

**Agent query:** Use this as the entry point for any domain-specific question. It gives you the lay of the land.

### Schema reference

See `schema/` for:
- `tool-template.md` — template for tool/technology pages
- `decision-template.md` — template for decision/trade-off pages
- `index.md` — schema guidance
- `conventions.md` — naming, linking, sourcing rules
- `page-types.md` — full description of page types
- `tags.md` — canonical tag registry

---

## Searching the Wiki (Agent Recipes)

### Recipe 1: Find all pages related to a concept

```bash
# Find all pages mentioning "sharding"
grep -r "sharding" wiki/ --include="*.md"

# Find all pages tagged with "scaling"
grep -r "tags:.*scaling" wiki/

# Find all decision pages (not tools, not indexes)
grep -r "type: decision" wiki/
```

### Recipe 2: Find tool pages for a domain

```bash
# All database tools
find wiki/databases -name "*.md" -exec grep -l "type: tool" {} \;

# All queue tools
find wiki/queues -name "*.md" -exec grep -l "type: tool" {} \;
```

### Recipe 3: Find case studies that mention a tool

```bash
# Case studies involving Cassandra
grep -r "cassandra" wiki/case-summaries

# Case studies involving PostgreSQL
grep -r "postgresql\|postgres" wiki/case-summaries
```

### Recipe 4: Build a decision tree for a workload

```bash
# Start: User wants "distributed cache"
# 1. Load wiki/caching/index.md → lists all caching strategies
# 2. Scan by problem: "High-throughput, low-latency" → Redis
# 3. Load wiki/caching/redis.md (tool) → understand gotchas
# 4. Search for "redis" across wiki/ → find related decisions
# 5. Load wiki/case-summaries/*redis* → see real examples
```

---

## Example Agent Prompt

```
You are helping an engineer decide on a database for their system.

The user says: "We’re building a real-time analytics platform.
We need to ingest 1M events/second, aggregate by user + cohort,
and serve queries with <500ms latency. What database should we use?"

Your workflow:
1. Load wiki/databases/index.md
2. Find the "by workload" section:
   - High-volume writes? → consider Cassandra, ClickHouse, TimescaleDB
   - Real-time aggregations? → consider TimescaleDB, ClickHouse
3. Load wiki/databases/timescaledb.md (if exists) or wiki/databases/clickhouse.md
4. Check the "when it fits" section
5. Search wiki/ for "timescaledb" or "clickhouse" + "analytics"
6. Find real examples: wiki/case-summaries/...
7. Return a structured brief:

---
Recommendation: TimescaleDB or ClickHouse
(depending on your SQL query complexity vs raw throughput needs)

Why: Both optimize for append-heavy time-series data + fast aggregations

TimescaleDB if:
- You want PostgreSQL-familiar SQL
- Complex transactions matter

ClickHouse if:
- You need maximum throughput
- SQL complexity is lower
- You’re OK with eventual consistency on aggregates

Real case: [link to case study]
---
```

---

## Sourcing Policy (for contributors & agents)

All pages link to primary sources. An agent reading a page should be able to:
1. See where each claim comes from (check `sources:` section)
2. Find quotes or citations that support the claim
3. Verify the claim against the original source (blog post, paper, etc.)

**No copy-paste from external sources.** Keep quotes short and paraphrased.

---

## When to Query This Skill

✓ User asks: "Should we use X or Y?"
✓ User asks: "When is X a good fit?"
✓ User asks: "What are the operational gotchas with X?"
✓ User asks: "Has anyone at [company] done this?"
✓ You’re doing architectural planning and need to surface trade-offs

✗ User asks: "What is X?" (and wants a generic definition)
  → Consider: Is this in the wiki as a `tool` page? If yes, query it. If no, this is out of scope.

✗ User asks: "Compare X, Y, Z by benchmark"
  → This wiki doesn’t track benchmarks (they change monthly). Link to wiki pages for conceptual trade-offs; user does their own benchmarking.

---

## Quick Links (for agents + humans)

- **Start here:** `wiki/index.md` (complete map of all pages)
- **By domain:** `wiki/databases/index.md`, `wiki/queues/index.md`, `wiki/caching/index.md`, `wiki/deployment/index.md`, `wiki/patterns/index.md`, `wiki/case-summaries/index.md`
- **Case studies:** `wiki/case-summaries/index.md`
- **Schema guide:** `schema/index.md`
- **Contributing:** `CONTRIBUTING.md`
- **Tag registry:** `schema/tags.md`