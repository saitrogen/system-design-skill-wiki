<img width="1024" height="600" alt="image" src="https://github.com/user-attachments/assets/b9d6a626-399b-4b01-b8ad-af4343dace32" />


Queryable knowledge base of system design decisions with explicit trade-offs, operational gotchas, and real case studies.

## What is this?

A macro skill for agents planning system architecture. Not a tutorial or reference; a decision-support system that captures:
- **What it optimizes for**: Each technology's core identity
- **When it fits/doesn't fit**: Concrete signals and anti-patterns
- **Operational reality**: Production gotchas, not theory
- **Real examples**: Discord, Netflix, Uber, Notion case studies
- **Trade-offs**: What you win vs what you sacrifice

## For Agents

Use this skill when users ask architectural questions:
- "Should we use X or Y?"
- "What database fits [workload]?"
- "Has anyone done [architecture pattern]?"

Agents systematically navigate domain indexes to find answers.

See `system-design-skill-wiki/SKILL.md` for agent integration guide.

## Install This Skill

```bash
npx skills add saitron/system-design-skill-wiki
```

## For Contributors & Maintainers

- **How to contribute**: See [`docs/CONTRIBUTING.md`](docs/CONTRIBUTING.md)
- **Development standards**: See [`docs/MAINTAINERS.md`](docs/MAINTAINERS.md)
- **Repository architecture**: See [`docs/STRUCTURE.md`](docs/STRUCTURE.md)

## Skill Contents

```
SKILL.md (agent specification)
wiki/ (31+ decision pages by domain)
├── index.md (main entry)
├── databases/ (6 tools + 3 decisions)
├── queues/ (2 tools + 1 decision)
├── caching/ (3 tools)
├── deployment/ (4 tools + 1 pattern)
├── patterns/ (event-driven, log-based)
└── case-summaries/ (Discord, Notion, Uber, Netflix)

schema/ (templates + conventions)
├── tool-template.md
├── decision-template.md
├── index.md (guidance)
├── conventions.md
└── tags.md (100+ registered tags)

raw-sources/ (external reading lists)
└── index.md
```

## Quick Links

- **Agent guide**: `SKILL.md`
- **Main wiki entry**: `wiki/index.md`
- **Schema guidance**: `schema/index.md`
- **Contributing**: `docs/CONTRIBUTING.md`
- **Development standards**: `docs/MAINTAINERS.md`
