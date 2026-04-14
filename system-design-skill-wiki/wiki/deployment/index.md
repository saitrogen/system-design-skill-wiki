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
last_updated: 2026-04-12
sources: []
---

# Deployment decisions

**Quick reference:** Containers vs VMs? Kubernetes vs managed services? Single-region or multi-region?

This index links to all deployment strategies, tools, and patterns in the `deployment/` domain.

## Core technologies

- [[containers|Containers]] — Docker/containerization, when it fits vs VMs/serverless
- [[kubernetes|Kubernetes]] — container orchestration, when to adopt, operational complexity
- [[serverless|Serverless]] — FaaS platforms (Lambda, Cloud Functions), cost model, vendor lock-in
- [[deployment-patterns|Deployment patterns]] — blue-green, canary, rolling, shadow, feature flags

## Topics to add

- Single-region vs multi-region
- Load balancing strategies
- DNS + failover
- Infrastructure-as-Code tools (Terraform, CloudFormation)

## Related domains

- [[databases/index|Databases]] — state management across regions
- [[patterns/index|Patterns]] — bulkheads, circuit breakers
- [[queues/index|Queues]] — async jobs, work queues
