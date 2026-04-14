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

## For Contributors

Add new pages, case studies, or patterns. See CONTRIBUTING.md for process.

## Repository Structure

```
├── README.md (this file)
├── CONTRIBUTING.md (how to contribute)
├── MAINTAINERS.md (development standards)
├── LICENSE
└── system-design-skill-wiki/ (the skill)
    ├── SKILL.md (agent specification)
    ├── wiki/ (31+ decision pages by domain)
    │   ├── index.md (main entry)
    │   ├── databases/, queues/, caching/, deployment/, patterns/, case-summaries/
    ├── schema/ (templates + conventions)
    │   ├── tool-template.md
    │   ├── decision-template.md
    │   ├── index.md
    │   ├── conventions.md
    │   └── tags.md
    └── raw-sources/ (external reading lists)
```

## Quick Links

- **Agent guide**: `system-design-skill-wiki/SKILL.md`
- **Contributing**: `CONTRIBUTING.md`
- **Development standards**: `MAINTAINERS.md`
- **Main wiki entry**: `system-design-skill-wiki/wiki/index.md`
- **Schema guidance**: `system-design-skill-wiki/schema/index.md`
