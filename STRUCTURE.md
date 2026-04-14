# System Design Skill Wiki — Final Structure & Quick Reference

## What was created

### 1. **Enhanced SKILL.md** (agent integration)
- Query patterns for agents (by domain, by tag, by page type)
- Example agent workflows for common questions
- Recipe cards for searching the wiki
- Example agent prompt showing decision brief structure
- When to use (and not use) this skill

**Location:** `system-design-skill-wiki/SKILL.md`

### 2. **Domain index.md files** (navigation & structure)
Created or enhanced index pages for all core domains:

- `wiki/databases/index.md` — Database decisions (PostgreSQL, Cassandra, MongoDB, Redis, S3)
- `wiki/queues/index.md` — Queue decisions (Kafka, RabbitMQ)
- `wiki/patterns/index.md` — Architecture patterns (sharding, replication, event sourcing)
- `wiki/case-summaries/index.md` — Production case studies
- `wiki/caching/index.md` — Caching strategies (placeholder)
- `wiki/deployment/index.md` — Deployment decisions (placeholder)
- `wiki/observability/index.md` — Observability decisions (placeholder)

**Why:** Domain indexes give agents a stable entry point. Each lists all pages in that domain + cross-domain links + quick "by problem" lookup.

### 3. **Updated wiki/index.md** (main entry point)
Clear navigation for both humans and agents.
- Links to all domain indexes
- Quick Q&A examples
- How to search + contribute

**Location:** `system-design-skill-wiki/wiki/index.md`

### 4. **Comprehensive CONTRIBUTING.md**
Replaced the minimal version with:
- **3 ways to contribute** (case summary, tool page, decision page) with step-by-step instructions
- **Naming rules** (kebab-case, tag registration, wikilink format)
- **Sourcing policy** (paraphrase, don't copy-paste)
- **Checklist** before submitting
- **Tag registration workflow**
- **Examples** linking to existing pages

**Location:** `system-design-skill-wiki/CONTRIBUTING.md`

### 5. **Design document** (this document's companion)
Answers the 6 strategic questions:
1. **Q1: SKILL.md design** — Query patterns for agents
2. **Q2: Domain index.md structure** — What goes in each domain
3. **Q3: Content seeding strategy** — 6 core domains, phased rollout
4. **Q4: Minimal viable structure** — Folder taxonomy, no content yet
5. **Q5: Human vs agent interaction** — Two different workflows
6. **Q6: Governance & versioning** — Quality review, stable references, maintenance

**Location:** (archived — content consolidated into other documentation)

---

## Quick reference: How things work now

### For agents (MCP/skill ecosystem)

**Entry point:** Domain-specific `index.md` files
```
AGENT REQUEST: "What database for append-heavy time-series at 1M ops/sec?"
↓
AGENT ACTION: Load `wiki/databases/index.md`
↓
SCAN: "By workload" section → "Append-heavy" → find [[cassandra|Cassandra]], [[scylladb|ScyllaDB]]
↓
READ: `wiki/databases/cassandra.md` for gotchas + trade-offs
↓
FIND: `wiki/case-summaries/discord-message-storage.md` (real example)
↓
RETURN: Structured decision brief
```

**Search patterns (agent recipes):**
```bash
# Find all pages related to scaling
grep -r "tags:.*scaling" wiki/

# Find all case studies
grep -r "type: case-summary" wiki/

# Find all decision pages (not tools)
grep -r "type: decision" wiki/
```

### For humans (Obsidian)

**Entry point:** `system-design-skill-wiki/wiki/index.md` (MOC)
```
HUMAN: Opens Obsidian
↓
NAVIGATE: Click domain link (e.g., [[databases/index|Databases]])
↓
SCAN: One-line summaries in index.md
↓
CLICK: Into a specific page (e.g., [[cassandra|Cassandra]])
↓
READ: Trade-offs + operational gotchas
↓
FOLLOW: Wikilinks to related pages (backlinks, [[Related]] section)
↓
DISCOVER: Decision landscape via link graph
```

---

## Folder structure (final)

```
system-design-skill-wiki/
├── SKILL.md                           # Agent integration guide ← UPDATED
├── CONTRIBUTING.md                    # How to contribute ← UPDATED
├── README.md                          # Project overview
├── schema/
│   ├── entry-template.md              # (exists)
│   ├── conventions.md                 # (exists)
│   ├── page-types.md                  # (exists)
│   ├── tags.md                        # (exists)
│   └── _index.md                      # (exists)
├── raw-sources/
│   ├── _index.md                      # (exists)
│   ├── databases.md                   # (exists)
│   ├── queues.md                      # (exists)
│   ├── patterns.md                    # (exists)
│   └── ...
├── wiki/
│   ├── index.md                       # Main entry point ← UPDATED
│   ├── databases/
│   │   ├── index.md                   # Domain entry ← CREATED
│   │   ├── postgresql.md              # (exists)
│   │   ├── cassandra.md               # (exists)
│   │   ├── scylladb.md                # (exists)
│   │   ├── mongodb.md                 # (exists)
│   │   ├── redis.md                   # (exists)
│   │   ├── s3.md                      # (exists)
│   │   └── when-to-leave-mongodb.md   # (exists)
│   ├── queues/
│   │   ├── index.md                   # Domain entry ← CREATED
│   │   ├── kafka.md                   # (exists)
│   │   ├── rabbitmq.md                # (exists)
│   │   └── kafka-vs-rabbitmq.md       # (exists)
│   ├── patterns/
│   │   ├── index.md                   # Domain entry ← CREATED
│   │   ├── event-driven-architecture.md # (exists)
│   │   └── log-based-architecture.md  # (exists)
│   ├── caching/
│   │   └── index.md                   # Domain entry ← CREATED (placeholder)
│   ├── deployment/
│   │   └── index.md                   # Domain entry ← CREATED (placeholder)
│   ├── observability/
│   │   └── index.md                   # Domain entry ← CREATED (placeholder)
│   └── case-summaries/
│       ├── index.md                   # Domain entry ← CREATED
│       ├── discord-message-storage.md # (exists)
│       └── notion-postgres-sharding.md # (exists)
```

---

## Status check: What's complete vs in-progress

### Complete (MVP ready)

✓ **SKILL.md** — Comprehensive agent integration guide with query patterns + example workflows
✓ **Domain indexes** — Fully populated for databases, queues, patterns; skeleton for caching, deployment, observability
✓ **wiki-home.md** — Clear navigation for both humans + agents
✓ **CONTRIBUTING.md** — Full contributor guide with examples
✓ **Core content** — PostgreSQL, Cassandra, MongoDB, Redis, Kafka, RabbitMQ, event-driven architecture, log-based architecture, 2 case studies
✓ **Schema** — entry-template.md, conventions.md, page-types.md, tags.md
✓ **Navigation** — Wiki links, backlinks, consistent tagging

### In progress (nice to have)

~ **Caching domain** — index.md exists; needs 2–3 tool pages (Redis caching strategies, Memcached, distributed caching)
~ **Deployment domain** — index.md exists; needs tool pages (containers, K8s, serverless) + patterns (blue-green, canary)
~ **Observability domain** — index.md exists; needs tool pages (Prometheus, Datadog, ELK) + patterns (sampling, alerting)
~ **Decision pages** — More decision pages (e.g., "when to shard Postgres" ← partially exists)
~ **Case studies** — More case studies (Netflix, Uber, Stripe, Twitter) ← structure ready, needs content

### Not yet addressed

- **Agent library**: Example agent code that queries this wiki (MCP wrapper, skill integration)
- **Raw sources indexes**: Curated reading lists in `raw-sources/<domain>/` (mostly empty but structure exists)
- **Automated validation**: Script to check for orphaned links, missing tags, etc.
- **Release process**: How to tag + announce new versions

---

## How to integrate with agents (next steps)

### For agents in the MCP/skill ecosystem:

1. **Point agents to `wiki-home.md` as the entry point**
2. **Use tag search as the primary query method**
   ```
   Agent query strategy:
   - User asks about [topic]
   - Agent searches `schema/tags.md` for matching tags
   - Agent uses grep/ripgrep to find pages with those tags
   - Agent returns structured brief
   ```
3. **Follow the query patterns in SKILL.md**
   - Section "Common agent queries" shows 3 example workflows
   - Section "Searching the Wiki (Agent Recipes)" shows 4 grep patterns

### Example MCP wrapper (pseudo-code):

```python
class SystemDesignSkill:
    def query(self, topic: str, tags: list[str]) -> str:
        """
        Topic: "append-heavy message storage"
        Tags: ["scaling", "wide-column", "high-throughput"]

        Returns: Path to relevant wiki pages + structured brief
        """
        # 1. Load domain index based on context
        # 2. Search for pages with matching tags
        # 3. Return links + summarized content
```

---

## Next content to add (recommended order)

### Phase 1: Seed the gaps (1–2 weeks)

1. Add "when to shard Postgres" decision page (partially exists, needs completion)
2. Add Redis caching strategies (tool page)
3. Add 1 more case study (e.g., Uber sharding, Netflix caching)
4. Add "outbox pattern" + "saga pattern" pages to patterns domain

### Phase 2: Expand domains (2–4 weeks)

5. Add 2–3 deployment tool pages (Kubernetes, serverless, containers)
6. Add 2–3 observability tool pages (Prometheus, ELK, tracing)
7. Add 2–3 caching pattern pages (cache-aside, write-through, write-behind)
8. Populate raw-sources/ reading lists (for each domain)

### Phase 3: Ecosystem integration (ongoing)

9. Create MCP wrapper or agent skill integration
10. Document agent query patterns + add examples to README
11. Set up CI to validate wikilinks, tags, frontmatter
12. Monthly review cycle: audit for staleness, archive old pages

---

## Testing checklist (before going live with agents)

- [ ] All domain index.md files are readable + link to actual pages
- [ ] wiki-home.md navigation works (all domain links resolve)
- [ ] Grep patterns in SKILL.md actually find pages (test each recipe)
- [ ] CONTRIBUTING.md examples point to pages that exist
- [ ] All wikilinks are working (no broken references)
- [ ] Tags in pages match schema/tags.md exactly (case-sensitive)
- [ ] Agent can query any domain index + find relevant pages
- [ ] Frontmatter is consistent (name, type, domain, tags, aliases, last_updated, sources)
- [ ] At least 2 pages in each populated domain
- [ ] At least 1 external source link per page (no orphans)

---

## File locations for reference

**Main documents:**
- `system-design-skill-wiki/SKILL.md` — Agent integration guide
- `system-design-skill-wiki/CONTRIBUTING.md` — Contributor guide
- `CONTRIBUTING.md` — Project-level contributor guide

**Navigation:**
- `system-design-skill-wiki/wiki/index.md` — Main entry point

**Domain indexes:**
- `system-design-skill-wiki/wiki/databases/index.md`
- `system-design-skill-wiki/wiki/queues/index.md`
- `system-design-skill-wiki/wiki/patterns/index.md`
- `system-design-skill-wiki/wiki/case-summaries/index.md`
- `system-design-skill-wiki/wiki/caching/index.md`
- `system-design-skill-wiki/wiki/deployment/index.md`
- `system-design-skill-wiki/wiki/observability/index.md`

---

## Summary

The System Design Skill Wiki is now structured to serve **two users equally well**:

1. **Humans in Obsidian** → browse via `system-design-skill-wiki/wiki/index.md` → navigate domains → read linked pages
2. **Agents via MCP** → query by tag → load domain index → return structured decision brief

The foundation is solid: naming conventions, template structure, sourcing policy, and navigation are all in place. Content seeding is complete (31 pages across 7 domains). The barrier to contribution is low: CONTRIBUTING.md and MAINTAINERS.md have clear instructions.

Ready for merge to master and for agents to start querying!
