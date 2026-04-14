---
name: Containers
type: tool
domain: deployment
tags:
  - deployment
  - containerization
  - docker
  - infrastructure
aliases:
  - Docker
  - Containerization
last_updated: 2026-04-15
sources:
  - https://docs.docker.com/
  - https://www.kernel.org/doc/html/latest/admin-guide/namespaces/index.html
---

# Containers

## What it is / what it optimizes for

Docker/containers package application code, dependencies, and runtime into a self-contained image that runs the same on a developer's laptop or in production. They optimize for consistency, reproducibility, and fast startup time by isolating processes without the overhead of full virtual machines.

## When it fits

- You want "write once, run anywhere" with reproducible environments across dev/test/prod
- Fast iteration cycles where container startup is measured in seconds
- Microservices where teams own individual services and deploy independently
- Cloud-native stacks (Kubernetes, managed container platforms) as your deployment target
- You need to scale horizontally: spin up more container instances as load increases

## When it doesn't fit

- Your workload requires low-level hardware access or custom kernel modules
- You have hard real-time requirements where process isolation overhead matters (millisecond-level precision)
- Your organization lacks container orchestration (manually managing containers at scale becomes operational hell)
- Legacy monoliths with deep OS-level dependencies that don't play well with containerization

## Operational gotchas

- **Image bloat:** Layers accumulate; easy to ship 1GB+ images. Requires discipline (multi-stage builds, layer awareness)
- **Persistent state:** Containers are ephemeral by design; managing stateful services adds complexity (volumes, mount concerns)
- **Resource limits:** Containers without CPU/memory limits can starve neighbors; must be explicit about resource requests
- **Security scanning:** Container images can ship vulnerabilities; requires automated scanning and image update discipline
- **Log management:** Logs from many container instances need aggregation; can't just tail a file on one server

## Trade-offs

- **Pro:** Reproducibility and consistency eliminate "works on my machine" problems
- **Pro:** Fast startup (seconds vs minutes for VMs) unlocks rapid horizontal scaling
- **Con:** Process isolation overhead (~5-10% CPU) compared to bare metal
- **Con:** Operational complexity if orchestration framework is undersized (Docker Swarm OK for 10 services; Kubernetes needed at scale)
- **Con:** Container registry management, image updates, and vulnerability scanning become mandatory toil

## Comparisons

| Compared to | Containers tend to win when… | Containers tend to lose when… |
| --- | --- | --- |
| [[virtual-machines\|VMs]] | You need fast startup and high-density packing | You need strong isolation or hardware-level virtualization |
| [[serverless\|Serverless]] | You need fine-grained control over environment or long-running processes | You have bursty traffic and want to pay only for idle time |

## Real-world example

- **Netflix:** [[netflix-container-adoption\|Adopted Docker for micro-services fleet]] — reduced deployment complexity and enabled rapid team iteration; moved orchestration to Kubernetes as fleet grew.

## Related

- [[kubernetes\|Kubernetes]]
- [[serverless\|Serverless]]
- [[deployment-patterns\|Deployment patterns]]

## Sources

- https://docs.docker.com/ — Docker official documentation
- https://www.kernel.org/doc/html/latest/admin-guide/namespaces/index.html — Linux namespaces (isolation primitives)
