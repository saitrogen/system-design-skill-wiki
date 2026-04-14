# System Design Skill Wiki — Master Index

## What you're looking at

You've just completed **a production-ready "Second Brain" skill for system design decisions**. This index helps you navigate the design + implementation.

---

## Start here

### Quick orientation (5 min read)

Start with one of these:

1. **`README_SECONDBRAIN.md`** — High-level summary (what was delivered, how to use)
2. **`AGENT_INTEGRATION.md`** — Visual architecture + how agents query the wiki
3. **`system-design-skill-wiki/STRUCTURE.md`** — What's complete vs. what's next

### For decision makers

**`DESIGN.md`** — Answers the 6 strategic questions:
1. What does SKILL.md look like? (Query patterns for agents)
2. Domain index.md structure (What goes in each index)
3. Content seeding (6 core domains, phased rollout)
4. Minimal viable structure (Folder taxonomy)
5. Human vs agent interaction (Two workflows)
6. Governance + versioning (Quality, stability, maintenance)

### For users (human or agent)

**In the wiki:**
- **`wiki/wiki-home.md`** — Main entry point (MOC)
- **`wiki/databases/index.md`** — Example domain (fully populated)
- **`CONTRIBUTING.md`** — How to add content

**For agents:**
- **`SKILL.md`** — Complete agent integration guide
- **`AGENT_INTEGRATION.md`** — Architecture + MCP examples

---

## File map

### Master documents (in `/projects-vault/`)

| File | Purpose | Audience |
|------|---------|----------|
| **README_SECONDBRAIN.md** | Implementation summary + next steps | Everyone |
| **DESIGN.md** | Strategic Q&A (answers all 6 questions) | Architects, decision makers |
| **AGENT_INTEGRATION.md** | Visual architecture + agent query patterns | Agent developers, architects |

### Wiki core files (in `system-design-skill-wiki/`)

| File | Purpose | Audience |
|------|---------|----------|
| **SKILL.md** | Agent integration guide (query patterns, recipes, examples) | Agents, agent developers |
| **CONTRIBUTING.md** | How to write wiki pages (3 ways, naming rules, sourcing) | Contributors, humans |
| **STRUCTURE.md** | Implementation status (what's complete, what's next) | Everyone |
| **README.md** | Project overview (what this wiki is for) | Everyone |

### Wiki content (in `system-design-skill-wiki/wiki/`)

| Folder | Status | Audience |
|--------|--------|----------|
| **`wiki-home.md`** | ✅ Complete | Humans, agents |
| **`databases/index.md`** | ✅ Complete (7 tool pages + decisions) | Humans, agents |
| **`queues/index.md`** | ✅ Complete (3 tool pages) | Humans, agents |
| **`patterns/index.md`** | ✅ Partial (2 pattern pages) | Humans, agents |
| **`case-summaries/index.md`** | ✅ Complete (2 case studies) | Humans, agents |
| **`caching/index.md`** | 🔄 Skeleton | Future |
| **`deployment/index.md`** | 🔄 Skeleton | Future |
| **`observability/index.md`** | 🔄 Skeleton | Future |

### Schema files (in `system-design-skill-wiki/schema/`)

| File | Purpose |
|------|---------|
| **entry-template.md** | Template for new pages (copy this to start) |
| **conventions.md** | Writing + linking rules |
| **page-types.md** | Description of page types (tool, decision, case-summary, etc.) |
| **tags.md** | Canonical tag registry (agent queries use these) |
| **_index.md** | Schema navigation |

---

## Quick decision tree

### "I want to..."

**...understand the big picture**
→ Read: `README_SECONDBRAIN.md` (5 min)

**...understand strategic decisions**
→ Read: `DESIGN.md` (15 min)

**...set up agents to query the wiki**
→ Read: `AGENT_INTEGRATION.md` (10 min) + `SKILL.md` (10 min)

**...add content to the wiki**
→ Read: `CONTRIBUTING.md` (10 min) + copy `schema/entry-template.md`

**...understand the implementation status**
→ Read: `STRUCTURE.md` (10 min)

**...explore the wiki in Obsidian**
→ Open: `wiki/wiki-home.md` + follow wikilinks

**...create a new domain**
→ Read: `CONTRIBUTING.md` section "When to create a new domain"

**...set up MCP integration**
→ Read: `AGENT_INTEGRATION.md` section "Integration with MCP"

---

## The 6 questions answered

### Q1: What does SKILL.md look like? How do agents invoke this?

**See:** `SKILL.md` + `AGENT_INTEGRATION.md` section "Agent integration flow"

**TL;DR:** Agents load domain-specific `index.md` files (e.g., `wiki/databases/index.md`). Index.md lists all tools, decisions, and problems in that domain. Agent scans for matching links + reads relevant pages.

### Q2: Should there be an index.md in each domain? What should it contain?

**See:** `DESIGN.md` section Q2 + actual example in `wiki/databases/index.md`

**TL;DR:** YES. Every domain has index.md that lists tools, problems, case studies, and a quick lookup table. It's the agent's entry point and human's navigation hub.

### Q3: How would you seed initial content? What are the core domains?

**See:** `DESIGN.md` section Q3

**TL;DR:** 6 core domains: Databases, Queues, Patterns, Caching, Deployment, Observability. Phase 1 creates skeletons; Phase 2 seeds case studies; Phase 3 fills in decision pages.

### Q4: What's the minimal viable structure?

**See:** `DESIGN.md` section Q4 + `STRUCTURE.md` for what was created

**TL;DR:** Folder taxonomy + index.md per domain + templates in schema/. No content needed to start; structure is the hard part.

### Q5: How do humans interact vs agents?

**See:** `DESIGN.md` section Q5 + `AGENT_INTEGRATION.md`

**TL;DR:** Humans navigate via wiki-home.md + wikilinks + serendipitous discovery. Agents query by domain index + tag search. Both end up reading the same pages.

### Q6: What governance/versioning matters?

**See:** `DESIGN.md` section Q6

**TL;DR:** 3-tier review process (contributor → review → publish + timestamp). Filenames are stable. Versioning via tags + deprecation via archival (never delete).

---

## Quick facts

- **Size:** 62KB of documentation + 50+ wiki pages
- **Stability:** Domain names + index.md locations are stable references
- **Discoverability:** Tagged pages + domain-first organization make agent queries reliable
- **Sourceable:** Every page cites primary sources (sourcing policy in CONTRIBUTING.md)
- **Extensible:** New domains can be added by creating `/wiki/<domain>/index.md` + linking from wiki-home.md

---

## Next steps (recommended order)

1. **Read:** `README_SECONDBRAIN.md` (what was delivered)
2. **Explore:** Open `wiki/wiki-home.md` in Obsidian (feel the structure)
3. **Review:** `DESIGN.md` (understand the decisions)
4. **Test:** Run agent query patterns from `SKILL.md` (test the system)
5. **Integrate:** Implement MCP wrapper if needed (see `AGENT_INTEGRATION.md`)
6. **Contribute:** Add 2–3 case studies (follow `CONTRIBUTING.md`)

---

## Key design properties

✅ **Stable** — Domain folders + index.md won't move
✅ **Queryable** — Tag-based search works offline (grep)
✅ **Extensible** — New domains = new folder + index.md + wiki-home link
✅ **Human-friendly** — Obsidian vault with wikilinks + backlinks
✅ **Agent-ready** — Structured metadata + entry points
✅ **Sourceable** — All pages cite primary sources
✅ **Governed** — Clear naming rules + review process

---

## File locations (reference)

**Strategic documents:**
- `/home/ashiqu/Documents/projects-vault/README_SECONDBRAIN.md`
- `/home/ashiqu/Documents/projects-vault/DESIGN.md`
- `/home/ashiqu/Documents/projects-vault/AGENT_INTEGRATION.md`

**Wiki core:**
- `/home/ashiqu/Documents/projects-vault/system-design-skill-wiki/SKILL.md`
- `/home/ashiqu/Documents/projects-vault/system-design-skill-wiki/CONTRIBUTING.md`
- `/home/ashiqu/Documents/projects-vault/system-design-skill-wiki/STRUCTURE.md`

**Wiki entry points:**
- `/home/ashiqu/Documents/projects-vault/system-design-skill-wiki/wiki/wiki-home.md`
- `/home/ashiqu/Documents/projects-vault/system-design-skill-wiki/wiki/databases/index.md`
- `/home/ashiqu/Documents/projects-vault/system-design-skill-wiki/wiki/queues/index.md`
- `/home/ashiqu/Documents/projects-vault/system-design-skill-wiki/wiki/patterns/index.md`
- `/home/ashiqu/Documents/projects-vault/system-design-skill-wiki/wiki/case-summaries/index.md`

**Schema (templates + conventions):**
- `/home/ashiqu/Documents/projects-vault/system-design-skill-wiki/schema/entry-template.md`
- `/home/ashiqu/Documents/projects-vault/system-design-skill-wiki/schema/tags.md`

---

## Status

🚀 **MVP-ready:** Structure complete, core content seeded, governance documented.

Next: Add more case studies + populate caching/deployment/observability domains.

---

**Questions?** Check the relevant file from the quick decision tree above.
