---
name: Tag registry
type: schema
domain: schema
aliases: []
last_updated: 2026-04-11
sources: []
---

# Tag registry

## How to use
Tags live in a page’s frontmatter under `tags:` and must match one of the canonical tags in the table below *exactly* (case- and hyphen-sensitive). Use one canonical form per concept (e.g., prefer `mongodb` over `mongo`), and for technologies prefer the official entity slug (typically the filename under `wiki/…/`). If you need a new tag, add it here first (with category + meaning) before using it in any page.

## Tag table

| Tag | Category | Meaning / when to use |
| --- | --- | --- |
| <tag> | meta | Template placeholder from `schema/entry-template.md`. Replace with a real tag; do not use in actual pages. |
| caching | domain | Anything primarily about caching strategies, cache layers, and cache trade-offs. |
| case-summary | page-type | Page summarizes a real-world system/decision (a company case study). |
| containerization | technology | Docker and container-related content (isolation, images, registries). |
| conventions | meta | Repo writing/schema conventions and guidance pages. |
| databases | domain | Anything primarily about databases, storage engines, or data persistence trade-offs. |
| deployment | domain | Infrastructure deployment, release strategies, and operational patterns. |
| discord | meta | Case-study subject: Discord (use on Discord-related pages). |
| distributed-systems | domain | Topics involving multi-node coordination, replication, partitions, consistency, etc. |
| index | page-type | Map-of-content / navigation pages (pointers rather than deep content). |
| in-memory | concept | In-memory storage and data structures; use for ephemeral and RAM-based systems. |
| message-storage | concept | Storage and retrieval of message/event streams (append-heavy, time-ordered reads). |
| migrations | concept | Data migrations, cutovers, dual-writes, backfills, and verification patterns. |
| ml | domain | Machine learning systems design case studies and supporting material. |
| memcached | technology | Memcached-specific content (tool page, decision triggers, or case evidence involving Memcached). |
| mongodb | technology | MongoDB-specific content (tool page, decision triggers, or case evidence involving MongoDB). |
| notion | meta | Case-study subject: Notion (use on Notion-related pages). |
| performance | concept | Tail latency, throughput, bottlenecks, and performance engineering trade-offs. |
| postgres | technology | Legacy shorthand tag for PostgreSQL seen in existing pages. Prefer `postgresql` for new content. |
| postgresql | technology | PostgreSQL (official technology/entity slug). Use for Postgres-specific content. |
| raw-sources | page-type | Curated reading lists / pointers to external sources (minimal original writing). |
| redis | technology | Redis-specific content (tool page, decision triggers, or case evidence involving Redis). |
| relational | concept | Relational data modeling and SQL-centric trade-offs (constraints, joins, transactions). |
| scaling | concept | Horizontal/vertical scaling triggers, capacity limits, sharding, and hotspots. |
| schema | page-type | Schema/template/spec pages that define how to write and structure wiki content. |
| sharding | concept | Horizontal partitioning across shards: routing, rebalancing, and cross-shard constraints. |
| sql | concept | SQL query semantics and trade-offs; use when SQL is central to the topic. |
| systems-design | domain | General system design / architecture material not limited to a single domain. |
| wide-column | technology | Wide-column / Bigtable-family data model (e.g., Cassandra/Scylla) and its trade-offs. |
| wiki | meta | Wiki-level organisation/navigation (used on wiki home / index pages). |
