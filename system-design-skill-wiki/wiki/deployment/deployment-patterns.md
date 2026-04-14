---
name: Deployment patterns
type: tool
domain: deployment
tags:
  - deployment
  - release-strategy
  - patterns
  - reliability
aliases:
  - Blue-green deployments
  - Canary releases
  - Rolling deployments
last_updated: 2026-04-15
sources:
  - https://martinfowler.com/bliki/BlueGreenDeployment.html
  - https://martinfowler.com/bliki/CanaryRelease.html
---

# Deployment patterns

## What it is / what it optimizes for

Deployment patterns define strategies for rolling out new versions of services with minimal downtime and risk. They optimize for reliability (detecting regressions early), speed (fast rollout), and rollback capability while accepting trade-offs in complexity and infrastructure cost.

## When it fits

- Any production environment where downtime costs money or customer trust
- Teams that deploy multiple times per day and need confidence in each release
- Services handling critical traffic where bad deployments cause cascading failures
- Organizations that want to catch bugs early (before they reach all users)
- Microservice systems where independent service deployments are the norm

## When it doesn't fit

- Single, monolithic application with infrequent deploys (daily or less) and low traffic
- When infrastructure doesn't support running multiple versions simultaneously
- Legacy systems without monitoring or automated rollback capability
- Extremely cost-constrained environments (some patterns require 2x infrastructure)

## Operational gotchas

- **Infrastructure overhead:** Blue-green and shadow deployments require running 2+ versions in parallel (expensive)
- **Data migration:** Stateful services (databases) complicate deployments; schema migrations must be backward-compatible
- **Metrics fragmentation:** Canary traffic goes to new version; need separate metrics/dashboards to detect canary-specific issues
- **Rollback timing:** How long do you watch the canary before promoting? Too short = miss slow regressions; too long = blocks other teams
- **Traffic shifting:** Complex routing logic (by user ID, geography, request type) adds latency and debugging difficulty
- **Partial failures:** If canary gets 5% traffic but fails silently, you might not detect it; aggressive monitoring is mandatory

## Trade-offs

- **Blue-green:** **Pro** — instant full rollback (switch traffic back to blue). **Con** — requires 2x resources, database migrations tricky
- **Canary:** **Pro** — low-risk validation on small % of traffic. **Con** — slow (watch metrics for 30 min+), requires sophisticated observability
- **Rolling:** **Pro** — low infrastructure cost, no duplicate resources. **Con** — harder to rollback, brief period of mixed old/new code
- **Shadow:** **Pro** — validate on real traffic without affecting users. **Con** — expensive (duplicate infrastructure + traffic), hidden state changes leak
- **Feature flags:** **Pro** — decouples deployment from release; easy rollback. **Con** — adds code complexity, flag management debt

## Comparisons

| Pattern | Best for | Cost | Rollback speed | Complexity |
| --- | --- | --- | --- | --- |
| [[deployment-patterns|Blue-green]] | Low-risk, critical services | High (2x infra) | Instant | Medium |
| [[deployment-patterns|Canary]] | High-traffic services, tight monitoring | Medium (1–2x infra) | Slow (10+ min) | High |
| [[deployment-patterns|Rolling]] | Cost-sensitive teams, stateless services | Low | Slow (10–30 min) | Low |
| [[deployment-patterns|Shadow]] | High-risk migrations, validation only | High (2x traffic) | N/A (no traffic switched) | High |
| [[deployment-patterns|Feature flags]] | Rapid iteration, frequent deploys | Low (no extra infra) | Very fast | Medium (code complexity) |

## Real-world example

- **Netflix:** Blue-green deployments at extreme scale; runs two complete data-center regions, switches traffic between them for zero-downtime deploys
- **Google:** Canary deployments are standard; every change hits 1% of traffic first, watches error rates/latency for 1–2 hours before promoting to 100%

## Related

- [[containers|Containers]]
- [[kubernetes|Kubernetes]]
- [[serverless|Serverless]]

## Sources

- https://martinfowler.com/bliki/BlueGreenDeployment.html — Martin Fowler on blue-green deployments
- https://martinfowler.com/bliki/CanaryRelease.html — Martin Fowler on canary releases
