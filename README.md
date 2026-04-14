![System Design Skill Wiki](https://github.com/user-attachments/assets/b9d6a626-399b-4b01-b8ad-af4343dace32)

<p align="center">
  <a href="#-the-problem">Problem</a> •
  <a href="#-how-it-works">How It Works</a> •
  <a href="#-install">Install</a> •
  <a href="#-whats-inside">What's Inside</a> •
  <a href="#-contribute">Contribute</a>
</p>

---

## 🎯 The Problem

Most developers aren't system architects.

They can write code, ship features, debug. But ask them *"should we use Cassandra or PostgreSQL?"* and they're guessing. They ship. They scale. They panic when it breaks.

Now imagine asking an AI agent the same question — it guesses too.

---

## ⚡ How It Works

A **macro skill for AI agents** that makes real architectural decisions. Not opinions. Not theory.

When you ask an agent: *"We need to store 100M messages/day with <20ms latency"*

The agent:
```
1. Loads wiki/databases/index.md
2. Scans for "append-heavy, high-throughput"
3. Reads Cassandra's trade-offs + gotchas
4. Finds Discord's real case study
5. Returns: "Cassandra. Why: scales linearly for append-heavy.
   Watch: token rebalancing stalls at scale.
   Real example: Discord migrated MongoDB → Cassandra → handled trillions."
```

**Real knowledge. Real decisions. No guessing.**

---

## 📦 Install

```bash
npx skills add saitron/system-design-skill-wiki
```

Works with Claude, Cursor, or any agent environment.

---

## 🏗️ What's Inside

**31+ pages** across 7 domains:

| Domain | Content | Example |
|--------|---------|---------|
| **Databases** | 6 tools + 3 decisions | PostgreSQL, Cassandra, MongoDB — when each breaks |
| **Queues** | 2 tools + 1 decision | Kafka vs RabbitMQ — throughput vs simplicity |
| **Caching** | 3 tools | Redis strategies, Memcached, distributed patterns |
| **Deployment** | 4 tools + 1 pattern | Containers, Kubernetes, Serverless |
| **Patterns** | 2 pages | Event-driven, log-based architecture |
| **Case Studies** | 4 deep-dives | Discord, Notion, Uber, Netflix |

**Each page includes:**
- ✓ What it optimizes for (core identity)
- ✓ When it fits (concrete signals)
- ✓ When it fails (operational gotchas)
- ✓ Real case studies (how it actually scaled)
- ✓ Trade-offs (what you win vs sacrifice)

---

## 🚀 Quick Links

- **For agents**: [`SKILL.md`](SKILL.md) — Integration guide + workflows
- **For contributors**: [`docs/CONTRIBUTING.md`](docs/CONTRIBUTING.md) — How to add pages
- **For maintainers**: [`docs/MAINTAINERS.md`](docs/MAINTAINERS.md) — Development standards
- **Main wiki**: [`wiki/index.md`](wiki/index.md) — All 31+ pages

---

## 🤝 Contribute

See [`docs/CONTRIBUTING.md`](docs/CONTRIBUTING.md) for:
- How to add case studies
- How to add tool pages
- How to add decision pages
- Sourcing policy (no copy-paste)

---

## 📄 License

MIT — Use freely, contribute back.
