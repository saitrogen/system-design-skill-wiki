---
name: Kubernetes
type: tool
domain: deployment
tags:
  - deployment
  - orchestration
  - kubernetes
  - infrastructure
aliases:
  - K8s
  - Orchestration
last_updated: 2026-04-15
sources:
  - https://kubernetes.io/docs/
  - https://kubernetes.io/blog/
---

# Kubernetes

## What it is / what it optimizes for

Kubernetes (K8s) is a container orchestration platform that automates deployment, scaling, and lifecycle management of containerized applications across clusters of machines. It optimizes for high availability, resource efficiency, and declarative infrastructure-as-code workflows at the cost of operational complexity.

## When it fits

- Multi-team organization where teams own services and need independent deployment cadences
- Workloads that span multiple nodes and require automatic failover (high availability is non-negotiable)
- You already run dozens or hundreds of services and manual orchestration becomes untenable
- You need declarative infrastructure: define desired state, let K8s reconcile reality
- You want to avoid vendor lock-in (K8s runs on any cloud: AWS/GCP/Azure/on-prem)

## When it doesn't fit

- Fewer than 5–10 services; overhead exceeds benefit (use Docker Swarm or manual orchestration instead)
- Your workload is "run once, exit" batch jobs (Kubernetes is designed for long-lived services)
- You can't afford the operational team to manage cluster health (etcd, kubelet, control plane)
- Strict compliance or security auditing requirements (Kubernetes' RBAC/network policies are complex to get right)
- Your organization is small and doesn't need the features; cloud-managed services (ECS, App Engine) are simpler

## Operational gotchas

- **Learning curve:** Deep abstraction layer (pods, services, deployments, stateful sets, ingress, RBAC). Takes months to operate confidently
- **Cluster health:** etcd quorum, kubelet crashes, apiserver overload. Node failure requires debugging
- **Resource requests/limits:** Poorly tuned requests → node starvation; too-loose limits → OOMKills in production
- **Networking:** Service discovery, ingress routing, and cross-namespace communication can be opaque
- **Upgrades:** Cluster version upgrades are non-trivial; managed services (GKE, EKS, AKS) are much safer than self-hosted
- **Cost:** Running a cluster with enough redundancy (3+ master nodes, 10+ worker nodes) is expensive before you ship a single service

## Trade-offs

- **Pro:** Declarative, reproducible, and portable; same YAML runs on GCP/AWS/on-prem
- **Pro:** Automatic restart, horizontal scaling, and rolling updates reduce operational toil once you get it right
- **Con:** Complexity tax for small teams; cognitive load is very high
- **Con:** Managed K8s is expensive (~$70–150/month per cluster); self-hosted requires on-call ops team
- **Con:** Debugging failures requires deep knowledge of K8s internals; errors can be cryptic

## Comparisons

| Compared to | Kubernetes tends to win when… | Kubernetes tends to lose when… |
| --- | --- | --- |
| [[containers\|Docker only]] | You need automated scaling and high availability | You have < 5 services and manual orchestration is fine |
| [[serverless\|Serverless]] | You need precise control over runtime or have long-running processes | You have bursty traffic and want to pay only for execution time |

## Real-world example

- **Kubernetes adoption at scale:** Most companies starting with Docker Swarm or manual orchestration migrate to Kubernetes once they hit 50+ services. Google/Netflix/Airbnb all run production K8s at massive scale; the learning curve pays off.

## Related

- [[containers\|Containers]]
- [[serverless\|Serverless]]
- [[deployment-patterns\|Deployment patterns]]

## Sources

- https://kubernetes.io/docs/ — Kubernetes official documentation
- https://kubernetes.io/blog/ — Release notes and best practices
