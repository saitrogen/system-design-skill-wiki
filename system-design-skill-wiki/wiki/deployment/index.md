---
name: Deployment decisions
type: index
domain: deployment
tags:
  - index
  - deployment
aliases:
  - Deployment guide
  - Infrastructure decisions
last_updated: 2026-04-15
sources: []
---

# Deployment decisions

**Quick reference:** How do I deploy code safely? Should I use containers? Kubernetes or serverless? What's the right deployment strategy?

This index links to all deployment strategies, tools, and patterns in the `deployment/` domain.

## Core technologies

- [[containers|Containers]] — Docker/containerization, when it fits vs VMs/serverless
- [[kubernetes|Kubernetes]] — container orchestration, when to adopt, operational complexity
- [[serverless|Serverless]] — FaaS platforms (Lambda, Cloud Functions), cost model, vendor lock-in
- [[deployment-patterns|Deployment patterns]] — blue-green, canary, rolling, shadow, feature flags

## Decision flows

### By scale and team size

**Just shipped your MVP (< 5 services, < 5 team members)**
- → [[containers|Containers]] + docker-compose (simplicity first)
- Skip Kubernetes for now; orchestration overhead exceeds benefit

**Growing service ecosystem (5–20 services, 5–20 people)**
- → [[containers|Containers]] + manual orchestration or [[kubernetes|Kubernetes]] (if you have DevOps expertise)
- If teams are independent: Kubernetes enables declarative deployments
- If you're bootstrapping: Managed services (ECS, App Engine) reduce operational burden

**Large platform (50+ services, dedicated platform team)**
- → [[kubernetes|Kubernetes]] is standard
- Declarative, portable, reduces toil for hundred-engineer organizations

### By workload pattern

**Always-on, predictable traffic (e.g., API servers, web services)**
- → [[containers|Containers]] + [[kubernetes|Kubernetes]]
- Cost is predictable; you pay for allocated resources

**Bursty, event-driven (e.g., webhooks, scheduled jobs, message processing)**
- → [[serverless|Serverless]] (AWS Lambda, Google Cloud Functions)
- Pay only for execution; scales from zero automatically

**Long-running, resource-intensive (> 15 min batch jobs, data pipelines)**
- → [[containers|Containers]] on dedicated infrastructure
- Serverless has hard timeouts; Kubernetes is overkill; run containers directly

### By deployment risk tolerance

**High risk, frequent deploys (multiple times per day)**
- → [[deployment-patterns|Canary or feature flags]]
- Catch regressions early; small blast radius

**Critical, zero-downtime requirement (financial systems, payment processing)**
- → [[deployment-patterns|Blue-green deployments]]
- Instant rollback if anything breaks

**Steady state, infrequent deploys**
- → [[deployment-patterns|Rolling deployments]]
- Low cost; simplicity over speed

## Trade-off comparisons

| Criterion | Containers | Kubernetes | Serverless |
|-----------|-----------|-----------|-----------|
| **Infrastructure overhead** | Low (dev laptop → prod) | High (must run 3+ master nodes) | Zero (managed by vendor) |
| **Operational complexity** | Low | Very high | Low |
| **Cost (always-on)** | Predictable | High (cluster minimum) | Expensive (pay per-invocation) |
| **Cost (bursty)** | Wastes capacity | Wasted capacity | Very efficient |
| **Scaling** | Manual or basic orchestration | Automatic (declarative) | Automatic (from zero) |
| **Latency** | Milliseconds | Seconds (scheduling) | Hundreds of ms (cold starts) |
| **Long-running workloads** | ✓ | ✓ | ✗ (15 min timeout) |
| **Stateful services** | ✓ (volumes) | Tricky (operators needed) | ✗ (ephemeral) |
| **Vendor lock-in** | ✗ | ✗ (runs anywhere) | ✓ (Lambda != GCF) |

## Operational gotchas (summary)

**Containers:** Image bloat, persistent state complexity, resource limits matter, registry/scanning overhead

**Kubernetes:** Learning cliff, cluster health critical, resource requests/limits poorly tuned, networking opacity, expensive baseline cost

**Serverless:** Cold starts, concurrency limits, vendor lock-in, logging/debugging fragmented, expensive at high invocation volume

**Deployment patterns:** Infrastructure overhead (blue-green, shadow), rollout timing decisions (canary), database migrations must be backward-compatible

## Sizing heuristics

- **MVP (< $100/month):** Containers on single host (Docker + manual deploy)
- **Scale to 10+ services ($500–1K/month):** Containers + basic orchestration (docker-compose) or managed services (ECS, App Engine)
- **Scale to 50+ services ($5K+/month):** Kubernetes or large managed services
- **Bursty workloads (unpredictable):** Serverless + containers for baseline (hybrid approach)

## Related domains

- [[databases/index|Databases]] — state management, replication across regions
- [[patterns/index|Patterns]] — bulkheads, circuit breakers, CQRS
- [[queues/index|Queues]] — async job processing, event-driven architectures

## Topics to explore next

- Single-region vs multi-region (replication, failover)
- Load balancing strategies (Layer 4, Layer 7, service mesh)
- DNS + failover + health checks
- Infrastructure-as-Code tools (Terraform, CloudFormation, Pulumi)
- Observability at scale (tracing, metrics aggregation, log aggregation)

## Raw sources (external primers)

- [[raw-sources/deployment|Deployment reading list]]

## When to ask for help

If you're deciding on a deployment strategy but don't see your problem here:
1. Check the **Related** links above
2. Look at `raw-sources/deployment` for external primers
3. Create a new page (start from `schema/tool-template.md`) + add to this index
