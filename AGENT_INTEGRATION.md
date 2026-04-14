# System Design Skill Wiki — Architecture & Agent Integration

## High-level architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          "Second Brain" Skill                            │
│         A queryable decision knowledge base for system design            │
└─────────────────────────────────────────────────────────────────────────┘

                                 ┌──────────────┐
                                 │  User Query  │
                                 │              │
                                 │ "What DB for │
                                 │  append-    │
                                 │  heavy at   │
                                 │  1M/sec?"   │
                                 └──────┬───────┘
                                        │
                  ┌─────────────────────┴─────────────────────┐
                  │                                            │
         ┌────────▼────────┐                        ┌────────▼────────┐
         │  Human Path     │                        │  Agent Path     │
         │  (Obsidian)     │                        │  (MCP/Skill)    │
         └────────┬────────┘                        └────────┬────────┘
                  │                                           │
         ┌────────▼──────────────────┐              ┌────────▼──────────────┐
         │ 1. Open wiki-home.md       │              │ 1. Load domain index   │
         │ 2. Click domain            │              │    (e.g., databases/)  │
         │ 3. Scan summaries          │              │                        │
         │ 4. Read full page          │              │ 2. Search by tag       │
         │ 5. Follow wikilinks        │              │    grep -r "scaling"   │
         │ 6. Use backlinks           │              │                        │
         │ 7. Discover via graph      │              │ 3. Return structured   │
         │                            │              │    decision brief       │
         └────────┬──────────────────┘              └────────┬───────────────┘
                  │                                           │
                  └──────────────────┬──────────────────────┬─┘
                                     │
                          ┌──────────▼──────────┐
                          │    Wiki Database     │
                          │   (Obsidian vault)   │
                          │                      │
                          │ ┌──────────────────┐ │
                          │ │ /wiki/databases/ │ │
                          │ │ /wiki/queues/    │ │
                          │ │ /wiki/patterns/  │ │
                          │ │ /wiki/case-.../ │ │
                          │ │ /wiki/*/index.md │ │
                          │ │                  │ │
                          │ └──────────────────┘ │
                          │                      │
                          │ ┌──────────────────┐ │
                          │ │ /schema/         │ │
                          │ │ (templates)      │ │
                          │ │                  │ │
                          │ └──────────────────┘ │
                          └──────────────────────┘
```

---

## Agent integration flow (detailed)

### Example: Agent query for "append-heavy database"

```
USER REQUEST:
"We're building a real-time analytics system. We need to ingest 1M events/sec,
store them for 30 days, and query by time-range. What database should we use?"

                              ↓

AGENT PROCESSING:

Step 1: Identify domain
   Context: "database" question → focus on `wiki/databases/`

Step 2: Load domain index
   GET: /wiki/databases/index.md
   Scan for: "append-heavy" + "high-throughput" sections

Step 3: Find matching tools
   From index.md's "By workload" section:
   ├─ "Append-heavy, time-ordered reads"
   │  ├─ [[cassandra|Cassandra]]
   │  ├─ [[scylladb|ScyllaDB]]
   │  └─ (see: [[discord-message-storage|Discord case]])

Step 4: Read tool pages
   GET: /wiki/databases/cassandra.md
   Scan: "When it fits" + "Operational gotchas" + Trade-offs

Step 5: Find case studies
   GET: /wiki/case-summaries/discord-message-storage.md
   Extract: Performance signals, data model, migration experience

Step 6: Compile structured brief
   ├─ Recommendation: Cassandra or ScyllaDB
   ├─ Why: Wide-column model fits append-heavy + range scans
   ├─ Trade-offs:
   │  ├─ Operational: Token ranges, compaction, JVM GC
   │  ├─ Consistency: Eventual (not strong)
   │  └─ Learning curve: Steeper than PostgreSQL
   ├─ Real example: Discord (see link)
   └─ Sources: [...links...]

RETURN TO USER:
"Based on your access pattern, **Cassandra or ScyllaDB** is the best fit.
Both optimize for append-heavy time-series workloads at scale.

⚠️ Trade-offs to consider:
- Operational: Compaction + GC tuning required
- Consistency: Eventually consistent (acceptable for metrics)
- Cost: Need to account for replication factor + disk space

See: Discord's experience migrating from MongoDB to Cassandra..."
```

---

## Query strategies for agents

### Strategy 1: Domain-first (most common)

```
User topic → Identify domain → Load index.md → Scan for problem type → Return links

Example:
"database" question
   → wiki/databases/index.md
   → "By workload: append-heavy"
   → [[cassandra]], [[scylladb]]
```

### Strategy 2: Tag-based search (when topic crosses domains)

```
grep -r "tags:.*<tag>" wiki/

Examples:

Find all scaling-related pages:
$ grep -r "tags:.*scaling" wiki/
  → databases/postgresql.md (has 'scaling' tag)
  → patterns/index.md (has 'scaling' tag)
  → case-summaries/notion-postgres-sharding.md

Find all case studies:
$ grep -r "type: case-summary" wiki/
  → case-summaries/discord-message-storage.md
  → case-summaries/notion-postgres-sharding.md
  → (add more here)

Find performance-related pages:
$ grep -r "tags:.*performance" wiki/
```

### Strategy 3: Full-text search (when topic is specific)

```
ripgrep or grep for specific keywords:

Find pages mentioning "partition":
$ grep -r "partition" wiki/ --include="*.md"

Find pages about replication:
$ grep -r "replication" wiki/ --include="*.md"

Find pages mentioning "MVCC":
$ grep -r "MVCC" wiki/ --include="*.md"
```

### Strategy 4: Decision tree (multi-step)

```
Question: "When should we use PostgreSQL?"

Step 1: Load index.md
   /wiki/databases/index.md

Step 2: Scan trade-off matrix
   If workload = "complex queries + transactions"
   → Use PostgreSQL

Step 3: Read tool page
   /wiki/databases/postgresql.md
   → "Operational gotchas" + "When NOT to use"

Step 4: Find related decision pages
   Look for: "when-to-leave-postgres" or "when-to-shard-postgres"

Step 5: Check for case studies
   grep -r "postgres\|postgresql" wiki/case-summaries/
   → Find real examples
```

---

## Query API (pseudo-code for agent developers)

```python
class SystemDesignWiki:
    """Query interface for the system design skill wiki."""

    def query_by_domain(self, domain: str) -> dict:
        """Load domain index and return all entries."""
        # Load: wiki/<domain>/index.md
        # Parse: Extract tools, decisions, case studies
        # Return: Dict of {tool_name: wikilink, description}
        pass

    def query_by_tag(self, tag: str) -> list[str]:
        """Find all pages with a specific tag."""
        # Search: grep -r "tags:.*<tag>" wiki/
        # Parse: Extract page paths + metadata
        # Return: List of file paths matching tag
        pass

    def query_by_problem(self, domain: str, problem: str) -> dict:
        """Find pages solving a specific problem in a domain."""
        # Load: wiki/<domain>/index.md
        # Scan: "By problem" section
        # Return: Relevant tool/decision pages
        pass

    def get_decision_brief(self, topic: str) -> str:
        """Generate a structured decision brief for a topic."""
        # 1. Identify domain from topic
        # 2. Load domain index
        # 3. Find tool/decision pages
        # 4. Read relevant sections
        # 5. Find case studies
        # 6. Return formatted brief with sources
        pass

    def find_case_study(self, technology: str) -> dict:
        """Find production case studies mentioning a technology."""
        # Search: grep -r "<technology>" wiki/case-summaries/
        # Return: List of case study files + summaries
        pass
```

---

## Integration with MCP (example)

### Skill registration

```yaml
# In MCP config (pseudocode)
skills:
  - name: system-design-skill-wiki
    description: |
      Query the system design decision wiki for architectural guidance
      on databases, queues, caching, patterns, deployment, observability.
      Use when: user asks about tech choice trade-offs, needs real examples,
      or is making architectural decisions.
    handler: system_design_wiki.query
    context:
      vault_path: /path/to/system-design-skill-wiki/
```

### Example agent usage

```python
# In agent code
agent = Agent()
agent.use_skill("system-design-skill-wiki")

# Agent receives user query
user_query = """
We're using MySQL but it's not scaling. Should we shard, move to Postgres,
or pick a different database? How have others done this?
"""

# Agent constructs skill query
skill_result = agent.call_skill(
    "system-design-skill-wiki",
    query="database scaling for MySQL",
    tags=["scaling", "sharding", "migrations"],
    domain="databases"
)

# Skill returns:
# {
#   "domain": "databases",
#   "relevant_pages": [
#     "wiki/databases/postgresql.md",
#     "wiki/databases/when-to-shard-postgres.md",
#     "wiki/case-summaries/notion-postgres-sharding.md"
#   ],
#   "decision_brief": "MySQL scaling options: ...",
#   "sources": ["https://..."]
# }

# Agent formats for user
agent.respond(skill_result)
```

---

## File organization for agent queries

```
system-design-skill-wiki/
│
├── SKILL.md                    ← Agent reads this first
│   ├─ "Quick Start for Agents"
│   ├─ "Common agent queries"
│   ├─ "Searching the Wiki (Agent Recipes)"
│   └─ "Query API"
│
├── wiki/
│   ├── wiki-home.md            ← Backup entry point
│   │
│   ├── databases/
│   │   ├── index.md            ← Primary entry point for DB questions
│   │   │   ├─ Core technologies
│   │   │   ├─ By access pattern
│   │   │   ├─ By scale trigger
│   │   │   └─ Trade-off comparison table
│   │   │
│   │   ├── postgresql.md        ← Tool page (when it fits, gotchas)
│   │   ├── cassandra.md
│   │   ├── mongodb.md
│   │   ├── redis.md
│   │   └── when-to-leave-mongodb.md ← Decision page
│   │
│   ├── queues/index.md          ← Primary entry point for queue questions
│   │   ├── kafka.md
│   │   ├── rabbitmq.md
│   │   └── kafka-vs-rabbitmq.md
│   │
│   ├── patterns/index.md         ← Primary entry point for pattern questions
│   │
│   ├── caching/index.md          ← Placeholder for future
│   ├── deployment/index.md       ← Placeholder for future
│   ├── observability/index.md    ← Placeholder for future
│   │
│   └── case-summaries/
│       ├── index.md
│       ├── discord-message-storage.md
│       └── notion-postgres-sharding.md
│
└── schema/
    ├── entry-template.md
    ├── conventions.md
    ├── page-types.md
    └── tags.md                  ← Agent reads this to understand tag taxonomy
```

---

## Key design properties (why this works for agents)

### 1. **Stable entry points**
- Every domain has `index.md`
- Filenames don't change (kebab-case, lowercase)
- Agents can reliably load `wiki/<domain>/index.md` without custom logic

### 2. **Structured metadata**
- Frontmatter (YAML) with consistent fields
- Tags are canonical (registered in `schema/tags.md`)
- Page types (tool, decision, case-summary, index) are standardized

### 3. **Searchable by tag**
- Every page has tags in frontmatter
- Tags map to concepts (not keywords)
- Simple grep works: `grep -r "tags:.*<tag>" wiki/`

### 4. **Minimal dependencies**
- No custom database required
- No API server required
- Plain markdown files + grep/ripgrep = queries work offline
- Scales from agent running locally to cloud-based MCP service

### 5. **Bidirectional linking**
- Pages link to each other (wikilinks)
- Humans discover via backlinks
- Agents can follow links to related pages

---

## Testing checklist for agent integration

Before deploying agents that query this wiki:

- [ ] **Load test:** Agent can successfully `load wiki/databases/index.md` + parse YAML
- [ ] **Search test:** `grep -r "tags:.*scaling" wiki/` returns expected pages
- [ ] **Link resolution:** Wikilinks like `[[cassandra|Cassandra]]` resolve to actual files
- [ ] **Metadata test:** All pages have required frontmatter (name, type, domain, tags, sources)
- [ ] **Query test:** Run 5 agent workflows from "Common agent queries" section of SKILL.md
- [ ] **Case study test:** Agent can find + extract key signals from case studies
- [ ] **Output test:** Agent returns structured brief with sources (as per SKILL.md example)
- [ ] **Tag consistency:** All tags used in pages exist in `schema/tags.md`

---

## Performance characteristics

| Operation | Complexity | Notes |
|-----------|------------|-------|
| Load domain index | O(1) | Index.md is small (~2KB) |
| Search by tag | O(n) | Linear scan of all pages (~100 pages in mature wiki) |
| Find case study | O(n) | Linear scan of case-summaries folder (~20 pages) |
| Parse frontmatter | O(k) | k = lines of frontmatter (~10 lines) |
| Follow wikilinks | O(m) | m = links in a page (~10 links) |

**Estimated:** Full wiki query + response generation: <100ms on modern hardware.

---

## Future enhancements (optional)

1. **Full-text indexing:** Pre-build Lucene/Elasticsearch index for faster search
2. **Semantic search:** Embed pages + query by semantic similarity
3. **Versioning API:** Expose `wiki-v1.2.0` tags + changelogs
4. **Change feeds:** Stream new pages to agents in real-time
5. **Validation hooks:** CI checks for broken links, missing tags, etc.

---

## Conclusion

The **system design skill wiki integrates naturally with agents** because it prioritizes:
- **Stable structure** (domain-first, index.md entry points)
- **Queryable metadata** (canonical tags, standardized frontmatter)
- **Self-contained format** (plain markdown, no special tools needed)

Agents can start querying immediately using the strategies in **SKILL.md**. No custom code required—grep + domain indexes are enough for MVP. Full MCP integration can be added later without changing the wiki structure.
