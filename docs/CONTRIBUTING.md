---
name: Contributing guide
type: schema
domain: schema
tags:
  - schema
  - conventions
  - meta
aliases:
  - How to contribute
last_updated: 2026-04-12
sources: []
---

# Contributing to the System Design Wiki

Welcome! This wiki is built by practitioners sharing real production experience. Here's how to contribute.

## Three ways to contribute

### 1. Add a new case summary (easiest)

You found a great blog post about a company's system design decision. You want to capture it.

**Steps:**

1. Read the source article fully
2. Create `wiki/case-summaries/<company>-<topic>.md`
   - Example: `discord-message-storage.md`, `notion-postgres-sharding.md`
   - Use kebab-case (lowercase, hyphens between words)
3. Copy the frontmatter from a similar case summary page (or use the template below)
   - Set `type: case-summary`
   - Set `domain: case-summaries`
   - Add any new tags to `schema/tags.md` **first**
4. Fill the sections:
   - **TL;DR:** 1–2 sentences
   - **Timeline / evolution:** if applicable (before → after progression)
   - **Decision drivers:** what forced the decision?
   - **What worked:** what succeeded
   - **What hurt:** what went wrong
   - **Links to wiki pages:** link back to `tool` pages and `decision` pages you reference
5. Add at least one external source link (the blog post, paper, etc.)
6. Add this case summary to the relevant domain index (e.g., `wiki/databases/index.md`)

**Example:**
```yaml
---
name: Discord message storage evolution
type: case-summary
domain: case-summaries
tags:
  - case-summary
  - discord
  - databases
  - message-storage
aliases:
  - How Discord stores trillions of messages
sources:
  - https://discord.com/blog/how-discord-stores-trillions-of-messages
---
```

### 2. Add a tool or technology page (medium)

You want to document when PostgreSQL is a good fit, its gotchas, and trade-offs.

**Steps:**

1. Create `wiki/<domain>/<tool-name>.md`
   - Example: `wiki/databases/postgresql.md`, `wiki/queues/kafka.md`
   - Use the **official slug** (not a marketing name)
   - Kebab-case, lowercase
2. Copy the frontmatter and sections from `schema/tool-template.md`
   - Set `type: tool`
   - Set `domain: <databases|queues|patterns|etc.>`
3. Fill the sections (from tool-template.md):
   - **What it is / what it optimizes for:** One sentence on what problem it solves
   - **When it fits:** Concrete use cases
   - **When it doesn't fit:** Be specific about failure modes
   - **Operational gotchas:** Things that bite you at scale
   - **Trade-offs:** What it sacrifices for what it does well
   - **Comparisons table:** vs competing tools (when it wins / loses)
   - **Real-world example:** Link to case studies
   - **Related:** Link to other tools + decision pages
4. Add at least one external source
5. Add wikilinks (`[[slug|Display Name]]`) to related pages
   - Other databases / tools in the same domain
   - Decision pages that mention this tool
   - Case studies that use this tool
6. Add this page to the relevant domain `index.md`

**Example section:**

```markdown
## Operational gotchas

- VACUUM/autovacuum: background maintenance cost scales with data size
- Connection pooling: PgBouncer or similar needed at high concurrency
- Replication lag: standby may not be ready for failover immediately
- Long transactions: block VACUUM and consume transaction slots (TXID wraparound risk)
```

### 3. Add a decision page (advanced)

You want to document a specific trade-off: "When should we leave MongoDB?" or "Kafka vs RabbitMQ for event streams?"

**Steps:**

1. Create `wiki/<domain>/<decision-slug>.md`
   - Example: `when-to-leave-mongodb.md`, `kafka-vs-rabbitmq.md`
   - Kebab-case, lowercase
2. Copy the frontmatter and sections from `schema/decision-template.md`
   - Set `type: decision`
   - Set `domain: <databases|queues|patterns|etc.>`
3. Fill the sections (from decision-template.md):
   - **What this is:** Clear intro on why this decision matters
   - **When to use [Option A]:** Concrete requirements for Option A
   - **When to use [Option B]:** Concrete requirements for Option B
   - **When NOT to use:** Pitfalls and edge cases
   - **Trade-offs table:** Comparison matrix (dimensions, behavior for each option)
   - **The tipping point:** The single-most decisive factor(s) for each option
   - **Pairs well with:** Related patterns and complementary tools
   - **Real-world examples:** Companies that chose each option (with case study links)
   - **Related:** Links to detailed tool pages
4. Add at least one external source (blog post, paper, primary source)
5. Add wikilinks to:
   - The tool pages you're comparing
   - Related decision pages
   - Case studies that illustrate this decision
6. Add this page to the relevant domain `index.md`

**Example:**

```markdown
## Decision drivers

- **Working set size:** MongoDB with dataset + compound index > RAM
- **Access pattern:** Mostly reads by timestamp, not complex queries
- **Scale requirement:** > 10B documents, sustained writes > 100K/sec
- **Operational constraint:** Can't afford high on-call burden
```

---

## Naming rules

Follow these strictly so the wiki stays queryable and linked pages don't break.

| What | Rule | Example |
|------|------|---------|
| **Filenames** | kebab-case, no spaces, lowercase | `mongodb.md`, `when-to-leave-mongodb.md` |
| **Folders** | domain (parent) must match in frontmatter | `wiki/databases/postgres.md` → `domain: databases` |
| **Wikilinks** | `[[slug\|Display Name]]` — slug = filename without .md | `[[kafka\|Apache Kafka]]` |
| **Tags** | Must exist in `schema/tags.md` first! | See tag registry |
| **Aliases** | Alternative names for search + autocomplete | `postgres`, `pg`, `postgresql` |

---

## Sourcing policy

**This is important:** The wiki's value is in **synthesis**, not in copying content. Here's how we do it:

✓ **DO:**
- Paraphrase the source in your own words
- Keep quotes short (< 2 sentences) and attribute them
- Link to the primary source
- Add specific decision drivers / signals from the source

✗ **DON'T:**
- Copy-paste large paragraphs from external sources
- Pretend you discovered something you didn't
- Link to secondary sources when the primary is available

**Example:**

```markdown
✓ GOOD:
"Discord found that once the message dataset + indexes exceeded RAM,
tail latency became unpredictable due to random disk I/O."
See: https://discord.com/blog/how-discord-stores-trillions-of-messages

✗ BAD:
"Once the dataset size exceeded RAM, the MongoDB instance experienced
severe performance degradation. The root cause was that the random read
access pattern caused the operating system to issue random disk I/O
requests, leading to unpredictable tail latency and cache churn."
(copied verbatim from blog)
```

---

## Adding a new tag

Before you use a tag in a page, register it:

1. Open `schema/tags.md`
2. Add a new row to the tag table:
   ```
   | my-tag | category | What this tag means / when to use |
   ```
3. Use the tag in your page's frontmatter

---

## Editing an existing page

If you find a page that's outdated or incomplete:

1. Update the content
2. **Bump `last_updated: YYYY-MM-DD`** in the frontmatter (use today's date)
3. Add any new sources to the `sources:` list
4. If significant changes: consider creating a new page instead (don't rewrite someone else's work without context)

---

## Deprecating or archiving a page

Don't delete pages; mark them as archived:

1. Add `archived` tag to the `tags:` list
2. Add a note at the top:
   ```markdown
   # <Page name>

   **ARCHIVED:** Consider [[replacement-page|replacement page]] instead.
   (Reason: X no longer applies in 2026, or Y changed)
   ```
3. Update related pages to not link to the archived page

---

## When to create a new domain

If you notice pages don't fit existing domains:

1. Create the folder: `wiki/<new-domain>/`
2. Create `wiki/<new-domain>/index.md` (use the index template above)
3. Add a link from `wiki/index.md`
4. Add the domain to `schema/tags.md`

---

## Questions before submitting

Ask yourself:

- [ ] Have I read `schema/conventions.md`?
- [ ] Does my filename follow kebab-case and match my slug?
- [ ] Does my `domain:` in frontmatter match my folder?
- [ ] Are all my tags registered in `schema/tags.md`?
- [ ] Do I have at least one external source?
- [ ] Have I added wikilinks to 1+ related pages?
- [ ] Have I added a link to this page from the relevant domain `index.md`?
- [ ] Is my sourcing paraphrased, not copy-pasted?

---

## Review process (for maintainers)

When you submit a page (via pull request or GitHub issue):

1. Check naming conventions (kebab-case, domain matches)
2. Check frontmatter completeness (all required fields)
3. Scan for sourcing compliance (no copy-paste)
4. Verify wikilinks are correct (pages exist or we'll add them)
5. Check that the page is added to the relevant `index.md`
6. Approve + merge

---

## Examples to copy from

- **Case summary:** `wiki/case-summaries/discord-message-storage.md`
- **Tool page:** `wiki/databases/postgresql.md`
- **Decision page:** `wiki/databases/when-to-leave-mongodb.md`
- **Index page:** `wiki/databases/index.md`

---

## Help

- **Which template should I use?** Check `schema/index.md` for guidance
- **Adding a tool/technology page?** Copy `schema/tool-template.md`
- **Adding a decision comparison page?** Copy `schema/decision-template.md`
- **Questions about structure?** Check `schema/conventions.md`
- **Looking for tags?** Check `schema/tags.md`

Thank you for contributing!