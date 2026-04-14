# System Design Skill Wiki — Integration & Governance Design

This document addresses the key architectural decisions for how the "Second Brain" skill integrates with agents, scales content, and stays maintainable.

---

## Q1: What does SKILL.md look like? How do agents invoke this?

### Current State
The SKILL.md file is minimal. It needs to evolve to guide both agents and humans on how to query and interact with the knowledge base.

### Proposed Structure

The SKILL.md should document two interaction patterns:

#### Pattern A: **Query-based lookup** (agent-native)
Agents get a request like "What's the best database for append-heavy workloads?" and query the wiki.

**How it works:**
- Agent scans `wiki/*/index.md` files first for domain taxonomy
- Agent searches `wiki/**/*.md` using tags + filename matching
- Example agent prompt: "Find pages tagged `wide-column` + `scaling`"
- Returns: relevant decision pages + case summaries with decision drivers

**Why this works:**
- Tags + domains create a queryable index without needing a database
- Agents can do simple grep/search without running custom code
- The wiki remains a plain Obsidian vault (no API, no custom server)

#### Pattern B: **Human narrative navigation** (Obsidian-native)
Humans open Obsidian and browse via wikilinks.

**How it works:**
- Start at `wiki/wiki-home.md` (MOC: map of content)
- Each domain has an `index.md` that links to all entries
- Case summaries link back to decision pages, creating a web
- Backlinks in Obsidian reveal related content

**Why this work:**
- Obsidian's native link graph becomes the discovery mechanism
- No extra tooling required; works offline
- Humans can "explore" the decision landscape serendipitously

---

## Q2: Should there be an index.md in each domain folder? What should it contain?

**Answer: YES.** Every domain folder needs an `index.md`.

### Purpose
- **For agents:** a stable landing page for that domain
- **For humans:** entry point that lists all decisions in that domain, with one-line summaries
- **For structure:** prevents "everything is flat"

### Template for `wiki/<domain>/index.md`

```yaml
---
name: "<Domain> decisions"
type: index
domain: <domain>
tags:
  - index
  - <domain>
aliases:
  - "<Domain> guide"
last_updated: 2026-04-12
sources: []
---

# <Domain> decisions

**Quick reference:** How to choose between <tool/pattern options> for <workload>? This index links to all decisions, trade-offs, and case studies in the `<domain>` domain.

## Core decisions

- [[<slug1>|<title>]] — When to use X vs Y
- [[<slug2>|<title>]] — X operational gotchas
- [[<slug3>|<title>]] — Scaling X to N nodes

## Case summaries

- [[<case1>|<case>]] — Real-world experience at <Company>
- [[<case2>|<case>]] — Evolution from X → Y at <Company>

## By problem

### High-volume append workloads
- [[<slug>|...]]

### Multi-tenant / isolation
- [[<slug>|...]]

### I need X, should I [...]?
- [[<slug>|...]]

## Related domains

- [[patterns-index|Patterns]] — Cross-cutting architecture
- [[raw-sources-index|Reading list]] — External primers

## When to ask for help

If you're deciding on `<domain>` but don't see your problem here:
1. Check the **Related** links above
2. Look at `raw-sources/<domain>` for external primers
3. Create a new page (start from `schema/entry-template.md`)
```

### Example: `wiki/databases/index.md`

```yaml
---
name: Database decisions
type: index
domain: databases
tags:
  - index
  - databases
aliases:
  - Database guide
  - Choosing a database
last_updated: 2026-04-12
sources: []
---

# Database decisions

**Quick reference:** How to choose between PostgreSQL, Cassandra, MongoDB, Redis, etc.? This index links to all database decisions, trade-offs, and case studies.

## Core technologies

- [[postgresql|PostgreSQL]] — relational, ACID, single-primary scale limits
- [[cassandra|Cassandra]] — wide-column, append-heavy, eventual consistency
- [[scylladb|ScyllaDB]] — Cassandra-compatible, lower ops toil
- [[mongodb|MongoDB]] — flexible docs, when it fits and when to leave
- [[redis|Redis]] — in-memory cache + data structures
- [[s3|S3]] — object store for "anything big and rarely-accessed"

## When to leave (decision triggers)

- [[when-to-leave-mongodb|Leave MongoDB]] — MVCC/index working set overflow
- [[when-to-shard-postgres|Shard PostgreSQL]] — VACUUM/autovacuum stalling

## Case summaries

- [[discord-message-storage|Discord message storage]] — MongoDB → Cassandra → ScyllaDB evolution
- [[notion-postgres-sharding|Notion Postgres sharding]] — scaling PostgreSQL horizontally

## By workload

### Append-heavy, time-ordered reads (messages, events, logs)
- [[cassandra|Cassandra]] + [[scylladb|ScyllaDB]]
- See: [[discord-message-storage|Discord case study]]

### Multi-row transactions, complex queries
- [[postgresql|PostgreSQL]]
- [[mongodb|MongoDB]] (if schema flexibility matters more than consistency)

### High-volume caching, counters
- [[redis|Redis]]

### Long-term bulk storage
- [[s3|S3]]

## Related domains

- [[patterns-index|Patterns]] — data modeling patterns (sharding, replication, etc.)
- [[queues-index|Queues]] — event sourcing often pairs with append-heavy databases

## Reading lists

- [[raw-sources/databases|Database primers]] — external resources
```

---

## Q3: How would you seed initial content? What are the core domains?

### Core Domains (MVP)

Start with these **6 domains** to cover 80% of system design decisions:

1. **`databases/`** — PostgreSQL, Cassandra, MongoDB, Redis, S3, etc.
2. **`queues/`** — Kafka, RabbitMQ, SQS, event-driven architectures
3. **`caching/`** — Cache-aside, write-through, distributed caching
4. **`patterns/`** — Sharding, replication, event sourcing, CQRS, bulkheads
5. **`deployment/`** — Container orchestration, serverless, regions
6. **`observability/`** — Logging, metrics, tracing, alerting

### Secondary Domains (can be added later)

- `networking/` — DNS, CDN, load balancing, protocol choices
- `security/` — auth, encryption, secrets management
- `ml-systems/` — feature stores, training pipelines, inference
- `mobile/` — offline-first, sync, bandwidth constraints

### Seeding Strategy

**Phase 1: Skeleton (week 1)**
- Create domain folders and `index.md` for each core domain
- Fill index.md with placeholder sections (no content yet)
- Create `wiki-home.md` (MOC) linking to all domain indexes
- Set up `CONTRIBUTING.md` with clear contribution guidelines

**Phase 2: Seed case studies (week 2–3)**
- Add 1–2 **case-summary** pages per domain (real production stories)
- Link each case summary back to decision pages (even if stubs)
- Example: Uber's sharding story, Netflix's cache strategy

**Phase 3: Decision pages (week 3–4)**
- Fill in the most common decision points (e.g., "PostgreSQL vs Cassandra for X")
- Use the **decision** template for these
- Cross-link aggressively

**Example seeding for `databases/`:**

```
wiki/databases/
├── index.md                          # Domain entry point
├── postgresql.md                     # tool: when to use, gotchas
├── cassandra.md                      # tool: wide-column, distributed
├── scylladb.md                       # tool: Cassandra alternative
├── mongodb.md                        # tool: document store
├── redis.md                          # tool: cache + data structures
├── s3.md                             # tool: object store
├── when-to-leave-mongodb.md          # decision: MVCC limits
├── when-to-shard-postgres.md         # decision: scaling trigger
└── raw-sources/databases/_index.md   # curated links to external primers
```

### Content Sourcing Policy

- **80% link-based:** Link to external sources (Discord blog, Postgres docs, etc.)
- **20% original:** Write 1–3 sentence paraphrases + decision drivers specific to your context
- **Zero copy-paste:** If quoting, keep it <3 sentences and attribute

This keeps the wiki lightweight and forces clarity on what's novel vs. just curating existing wisdom.

---

## Q4: What's the minimal viable structure before adding content?

### Folder Structure (no content, just folders + stubs)

```
system-design-skill-wiki/
├── schema/                            # Governance + templates
│   ├── entry-template.md              # (exists)
│   ├── conventions.md                 # (exists)
│   ├── page-types.md                  # (exists)
│   ├── tags.md                        # (exists)
│   └── _index.md                      # Schema navigation
├── raw-sources/                       # Curated external sources
│   ├── _index.md                      # Start here (reading lists)
│   ├── databases.md                   # Curated DB primers + papers
│   ├── queues.md                      # Event-driven, Kafka, etc.
│   ├── patterns.md                    # Distributed systems primers
│   ├── deployment.md
│   ├── caching.md
│   └── observability.md
├── wiki/                              # The actual knowledge base
│   ├── wiki-home.md                   # MOC: entry point
│   ├── databases/
│   │   └── index.md                   # Domain entry point (links all DB pages)
│   ├── queues/
│   │   └── index.md
│   ├── caching/
│   │   └── index.md
│   ├── patterns/
│   │   └── index.md
│   ├── deployment/
│   │   └── index.md
│   ├── observability/
│   │   └── index.md
│   └── case-summaries/
│       └── index.md                   # Cross-domain case list
├── SKILL.md                           # Agent integration guide (enhanced)
├── CONTRIBUTING.md                    # How to add content
└── README.md                          # Project overview
```

### What goes in `wiki-home.md`?

```yaml
---
name: System Design Wiki
type: index
domain: systems-design
tags:
  - index
  - wiki
  - systems-design
aliases:
  - Design home
  - Architecture decisions
last_updated: 2026-04-12
sources: []
---

# System Design Wiki

**A structured Obsidian vault capturing senior developer decision-making on system architecture.**

Not encyclopedic; focused on decision drivers, options, and real-world consequences.

## Start here

- [[databases-index|Databases]] — PostgreSQL vs Cassandra vs others?
- [[queues-index|Queues]] — Kafka, RabbitMQ, when to pick each?
- [[patterns-index|Patterns]] — Sharding, replication, event sourcing
- [[caching-index|Caching]] — Cache-aside, warming, invalidation
- [[deployment-index|Deployment]] — Containers, serverless, regions
- [[observability-index|Observability]] — Logging, metrics, traces

## Case studies

Real production decisions from Discord, Notion, Uber, Netflix, etc.

- [[case-summaries-index|All case studies]] — Start here for narrative learning

## How to use this wiki

**For agents:** Start at a domain `index.md`, search by tags
**For humans:** Browse wikilinks in Obsidian, follow the graph

## How to contribute

- [[CONTRIBUTING|Contributing guide]]
- [[schema/_index|Schema + conventions]]

## What's NOT here

- Exhaustive technology catalogs (use Wikipedia for that)
- Benchmark comparisons (those change monthly)
- Opinion without trade-offs (everything is here for a reason)
```

### Minimal SKILL.md (enhanced)

The SKILL.md should be brief but clear. See Q1 and the updated SKILL.md below.

---

## Q5: How do humans interact with this in Obsidian vs agents?

### Human Interaction (Obsidian-native)

**Primary workflow:**
1. Open Obsidian, navigate to `wiki/wiki-home.md`
2. Click domain index you're interested in (e.g., `wiki/databases/index.md`)
3. Scan one-line summaries, click into a decision page
4. Read the decision drivers, options table, consequences
5. Follow **Related** links to explore adjacent decisions
6. Use Obsidian's **graph view** to see decision clusters

**Serendipitous discovery:**
- Use backlinks pane to see "what links here?"
- Use search by tags (e.g., search for `#scaling` to find all scaling-related pages)
- Use Obsidian's **Omnisearch** to find pages by keyword

**Contributing from Obsidian:**
1. Create a new file in the appropriate domain folder
2. Copy the frontmatter from `schema/entry-template.md`
3. Fill in the sections
4. Add wikilinks to related pages (Obsidian will create backlinks automatically)

### Agent Interaction (Query-based)

**Primary workflow:**
1. Agent receives a user requirement: "We need a database for time-ordered message reads at scale"
2. Agent queries the wiki:
   - Check `wiki/databases/index.md` for domain overview
   - Search for pages tagged `#wide-column`, `#append-heavy`, `#scaling`
   - Find `[[cassandra|Cassandra]]` and `[[discord-message-storage|Discord case study]]`
3. Agent reads the decision pages + case summary
4. Agent returns structured decision brief to the user:
   ```
   **Recommended: Cassandra**

   Why: Append-heavy workload + range-scan queries + extreme scale

   Trade-offs:
   - Operational: Requires understanding of token ranges, compaction
   - Consistency: Eventually consistent (multi-DC complicates this)
   - Learning curve: Steeper than PostgreSQL

   See also: Discord's evolution (MongoDB → Cassandra → ScyllaDB)
   ```

**Agent search strategies:**
- By tag: `grep -r "tags:" wiki/**/*.md | grep "wide-column"`
- By filename: `find wiki -name "*cassandra*"`
- By frontmatter: search for `type: decision` + `domain: databases`
- By full-text: ripgrep for "append-heavy" across all pages

### Why Two Modes?

| Aspect | Human | Agent |
|--------|-------|-------|
| **Start point** | `wiki-home.md` → domain index → decisions | `index.md` for domain → tag search |
| **Navigation** | Wikilinks + graph view | Filename + tag matching |
| **Output** | Read markdown in Obsidian | Return structured brief |
| **Contributing** | Obsidian UI + markdown editing | (rare; usually human-written) |
| **Serendipity** | High (backlinks, search) | Lower (structured query-based) |

---

## Q6: What governance/versioning matters?

### Governance: Content Quality

**Problem:** As the wiki grows, pages become inconsistent, outdated, or conflicting.

**Solution: Three-tier review process**

1. **Contributor creates page**
   - Must follow `schema/entry-template.md` format
   - Must add 1+ external `sources`
   - Must link to 1+ related pages
   - File in the correct domain folder

2. **Async peer review (in git/GitHub)**
   - Reviewer checks:
     - Does it follow the template?
     - Is the source attribution clear?
     - Are the decision drivers concrete (not generic)?
     - Do the trade-offs match the sources?
   - If OK: approve. If issues: request changes.

3. **Publish + timestamp**
   - Once approved, merge to main
   - Set `last_updated` to today
   - Tag in git: `wiki-v1.2.0` (if significant content addition)

### Versioning: Stable References

**Goal:** Pages can be referenced from pull requests, design docs, etc. without breaking.

**Strategy:**
- Use **filenames as stable identifiers** (e.g., `cassandra.md` always refers to Cassandra)
- If renaming a page, create a redirect in `aliases:` (in frontmatter)
- Tag releases when you add significant new domains or case studies
  - `wiki-v1.0.0`: Core domains + first 10 pages
  - `wiki-v1.1.0`: Add case-summaries domain
  - `wiki-v1.2.0`: Add caching, observability domains

**Example release tag:**
```bash
git tag wiki-v1.1.0 -m "Add case-summary domain: Discord, Notion, Uber"
```

### Maintenance: Keeping Pages Fresh

**Problem:** Pages become stale (new findings, tool versions change).

**Solution: `last_updated` field + annual review**

1. When you **update a page meaningfully**, bump `last_updated: YYYY-MM-DD`
2. At the **start of each quarter**, scan pages with `last_updated > 1 year ago`
   - Decide: still accurate? needs refresh? can deprecate?
   - Update or mark as `#archived` (add to tags)
3. **Deprecation:** Don't delete; mark with:
   - `tags: [..., archived]`
   - Add a note in the page: "Archived: Consider [[replacement-page]] instead"

### Conflict Resolution: Competing Trade-offs

**Problem:** Two contributors have different opinions on when to use tool X.

**Solution: Separate pages, not edit wars**

- `wiki/databases/mongodb.md` — MongoDB: when it fits, gotchas
- `wiki/databases/when-to-leave-mongodb.md` — Specific triggers to move away
- Both link to each other; both cite sources

This way, readers get both perspectives + sources to decide.

### Agent-Facing Stability

**Guarantee:** Agents can rely on:
- Domain folders (`wiki/databases/`, `wiki/queues/`) won't move
- `index.md` in each domain is stable and links to all entries
- Filenames follow kebab-case and don't change
- Frontmatter format (especially `tags:`) remains consistent

**If changes needed:**
- Never delete an old page; mark as archived
- Use `aliases:` to redirect old link targets
- Bump the `wiki-vX.Y.0` tag

---

## Summary: Integration Checklist

### For agents to adopt this skill:

- [ ] SKILL.md updated with query patterns + tag examples (Q1)
- [ ] Every domain has `wiki/<domain>/index.md` (Q2)
- [ ] Core domains seeded (at least stubs + case summaries) (Q3)
- [ ] Folder structure created (even if mostly empty) (Q4)
- [ ] Query examples in SKILL.md for agent developers (Q5)
- [ ] CONTRIBUTING.md + versioning strategy documented (Q6)

### For humans to contribute:

- [ ] README.md + CONTRIBUTING.md clearly visible
- [ ] `schema/` folder contains templates they can copy
- [ ] One worked example in each domain (e.g., PostgreSQL, Cassandra)
- [ ] Clear guidance on sourcing + quoting policy

---

## Next Steps

1. **Enhance SKILL.md** (15 min) — Add agent query patterns + tag examples
2. **Create domain indexes** (30 min) — Fill `wiki/<domain>/index.md` for each core domain
3. **Create wiki-home.md** (20 min) — Top-level MOC
4. **Seed 1–2 pages per domain** (ongoing) — Start with `tool` type pages (PostgreSQL, Cassandra, Kafka, etc.)
5. **Add case summaries** (ongoing) — Real production stories (Discord, Notion, etc.)
6. **Create CONTRIBUTING.md** (20 min) — Clear guidelines for contributors
