# Schema & Templates

This directory contains the structural guidelines for the System Design Skill Wiki.

## Templates (Choose ONE per page)

### 1. Tool Template (`tool-template.md`)

**Use for:** Individual tools, technologies, platforms (PostgreSQL, Redis, Kafka, etc.)

**Structure:**
- What it is / what it optimizes for
- When it fits
- When it doesn't fit
- Operational gotchas
- Trade-offs
- Comparisons (vs competing tools)
- Real-world example
- Related + Sources

**Example:** [[databases/cassandra|Cassandra]]

---

### 2. Decision Template (`decision-template.md`)

**Use for:** Choice between two or more options ("When to use X vs Y?")

**Structure:**
- What this is
- When to use Option A
- When to use Option B
- When NOT to use
- Trade-offs matrix
- The tipping point (decisive factors)
- Pairs well with (related patterns/tools)
- Real-world examples
- Related + Sources

**Example:** [[queues/kafka-vs-rabbitmq|Kafka vs RabbitMQ]]

---

### 3. Index Template (implicit)

**Use for:** Domain overview pages (`databases/index.md`, `queues/index.md`, etc.)

**Structure:**
- Quick reference (1 sentence)
- Core technologies (list with links)
- Decision flows (By access pattern, By scale trigger, etc.)
- Case summaries
- Trade-off comparisons (matrix)
- Related domains
- Raw sources
- When to ask for help

**Example:** [[databases/index|Database decisions]]

---

## Other Files

- `conventions.md` — Naming rules, wikilink format, kebab-case
- `tags.md` — Approved tag vocabulary
- `page-types.md` — Explanation of page type frontmatter values
- `entry-template.md` — Legacy template (use tool or decision template instead)

---

## Quick Reference: Which Template Should I Use?

```
Is this page about ONE tool/technology?
├─ Yes → Use tool-template.md
└─ No → This page is about a CHOICE between options
    └─ Yes → Use decision-template.md
```

---

## Contributing

When adding a new page:
1. Copy the appropriate template (tool or decision)
2. Update the frontmatter: name, domain, tags, aliases, sources
3. Follow the section order (don't skip sections)
4. Use wikilinks for related pages (`[[page-slug|Display Text]]`)
5. Add at least one external source link
6. Tag properly; see `tags.md` for approved tags

**Frontmatter tip:** The `sources:` field is a YAML list of URLs:
```yaml
sources:
  - https://example.com/article-1
  - https://example.com/article-2
```
Add descriptions in the ## Sources section at the end of your page, not in the frontmatter.

See `../CONTRIBUTING.md` for full contributor guidelines and `conventions.md` for formatting rules.
