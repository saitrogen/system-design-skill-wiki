# Maintainers Guide

This guide is for contributors maintaining the System Design Skill Wiki.

## Repository Structure

**Root level** (the skill):
- SKILL.md — Agent integration spec (don't modify without updating docs/CONTRIBUTING.md + schema/)
- wiki/ — Decision pages (31 current pages across 7 domains)
- schema/ — Templates + conventions
  - tool-template.md — Template for tool/technology pages
  - decision-template.md — Template for decision/comparison pages
  - conventions.md — Naming, frontmatter, sourcing, writing style standards
  - tags.md — Canonical tag registry
  - index.md — Guidance on which template to use
- raw-sources/ — External reading lists and curated links

**docs/** (maintainer documentation):
- CONTRIBUTING.md — Guidelines for contributors
- MAINTAINERS.md — This file (development standards)
- STRUCTURE.md — Repository architecture details

## Adding New Content

### Before You Start
1. Read `schema/conventions.md` (complete guide)
2. Verify your tag exists in `schema/tags.md`, add if missing
3. Use the correct template:
   - Tool page? → `schema/tool-template.md`
   - Decision page? → `schema/decision-template.md`

### When Submitting
- [ ] Frontmatter complete (name, type, domain, tags, last_updated, sources)
- [ ] All tags registered in schema/tags.md
- [ ] All wikilinks [[page|text]] use correct case-sensitive slugs
- [ ] Dates in YYYY-MM-DD format
- [ ] Sources in frontmatter are plain URLs only (descriptions in ## Sources section)
- [ ] At least 1 external source + 1 Related wikilink
- [ ] Added page to relevant domain index.md
- [ ] Paraphrased content (no copy-paste from sources)

## Development Standards

### Files Should Follow These Rules

**Frontmatter (all pages):**
```yaml
name: "Human-readable title"
type: tool | decision | case-summary | index | pattern
domain: databases | queues | patterns | caching | deployment | case-summaries
tags:
  - canonical-tag (must exist in schema/tags.md)
aliases: [Alternative Name, Abbreviation]
last_updated: 2026-04-15 (YYYY-MM-DD)
sources:
  - https://example.com/source-1
  - https://example.com/source-2
```

**Naming conventions:**
- Filenames: kebab-case.md (lowercase, hyphens, no spaces)
- Wikilinks: `[[slug|Display Name]]` (slug = filename without .md)
- Tags: lowercase-kebab-case (matches filenames)
- Dates: YYYY-MM-DD format only

### Page Types & Required Sections

**Tool pages** (9 sections):
1. What it is / what it optimizes for
2. When it fits
3. When it doesn't fit
4. Operational gotchas
5. Trade-offs
6. Comparisons (vs other tools)
7. Real-world example
8. Related
9. Sources

**Decision pages** (10 sections):
1. What this is
2. Option A: When to use
3. Option B: When to use
4. When NOT to use
5. Trade-offs matrix
6. The tipping point
7. Pairs well with
8. Real-world examples
9. Related
10. Sources

**Case-summary pages** (7 sections):
1. TL;DR
2. Timeline / evolution
3. Decision drivers
4. What worked
5. What hurt
6. Links to tool/decision pages
7. Sources

**Index pages** (7 sections):
1. Quick reference
2. All tools in domain
3. Decision flows
4. Comparison matrix
5. Case summaries
6. Related domains
7. Sources

### Writing Style

- **Judgment-first**: Lead with what it optimizes for, not what it is
- **Operational**: Focus on production reality, not theory
- **Concrete**: Use specific signals ("tail latency becomes the bottleneck") not generic statements
- **Linked**: At least 1 wikilink per page
- **Sourced**: Paraphrased, not copy-pasted. Keep quotes short.

### Validation Checklist (Before Submitting)

Run these checks:

```bash
# Check for unregistered tags
grep -r "tags:" wiki/ | grep -v schema/tags.md | \
  cut -d: -f3- | tr ',' '\n' | sort -u | \
  while read tag; do grep -q "$tag" schema/tags.md || echo "Unregistered: $tag"; done

# Check for broken wikilinks (escaped pipes)
grep -r '\[\[[^]]*\\|' wiki/

# Check date format (should be YYYY-MM-DD)
grep -r "last_updated: [^0-9]" wiki/

# Check for mixed case in wikilinks
grep -r '\[\[[A-Z]' wiki/ | grep -v 'Case\|Decision\|Database\|Index'
```

## Release Process

1. Create feature branch from master
2. Make changes on branch
3. Validate (run checks above)
4. Create pull request
5. Tag reviewed by maintainers
6. Merge to master
7. Version bump in changelog (optional)

## Questions?

See `docs/CONTRIBUTING.md` for contributor guidelines, or `schema/conventions.md` for detailed standards.
