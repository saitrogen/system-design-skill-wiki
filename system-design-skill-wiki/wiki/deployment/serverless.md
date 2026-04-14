---
name: Serverless
type: tool
domain: deployment
tags:
  - deployment
  - serverless
  - faas
  - infrastructure
aliases:
  - FaaS
  - AWS Lambda
  - Cloud Functions
last_updated: 2026-04-15
sources:
  - https://aws.amazon.com/lambda/
  - https://cloud.google.com/functions/docs/
  - https://azure.microsoft.com/en-us/services/functions/
---

# Serverless

## What it is / what it optimizes for

Serverless platforms (AWS Lambda, Google Cloud Functions, Azure Functions) automatically scale and bill-per-invocation, eliminating the need to manage servers or pre-allocate capacity. They optimize for simplicity and cost-efficiency on bursty workloads while accepting cold starts and vendor lock-in.

## When it fits

- Event-driven workloads: webhooks, scheduled jobs, message processing from queues
- Bursty traffic where you'd otherwise pay for idle capacity (HTTP APIs with unpredictable demand)
- Proof-of-concepts and rapid prototyping where operational overhead matters
- Short-lived functions (< 15 min; AWS Lambda timeout limit)
- Teams that want to ship without managing infrastructure (startups, small teams)

## When it doesn't fit

- Always-on services that need sub-second cold-start performance
- Long-running processes (batch jobs > 15 min) — Lambda times out; Step Functions becomes expensive
- Functions that need persistent local state or large temporary files
- Strict cost predictability (serverless bills spike with invocation volume; containers offer more predictable costs at scale)
- Deep integration with legacy on-prem systems (vendor lock-in risk is high)

## Operational gotchas

- **Cold starts:** First invocation after idle period suffers 100–500ms+ latency (languages like Java are worse)
- **Concurrency limits:** AWS Lambda has per-account concurrency caps; auto-scaling has limits
- **Debugging:** Logs scattered across vendor's logging service; stack traces in CloudWatch, not your monitoring
- **Dependencies:** Large dependencies bloat deployment package; bundling and layer management becomes tedious
- **Costs at scale:** Per-invocation billing + data transfer charges become expensive (e.g., 1M API calls/day can cost $1000+)
- **Vendor lock-in:** Each cloud has different function model, triggers, IAM; migration to another cloud is expensive

## Trade-offs

- **Pro:** Zero infrastructure management; pay only for CPU-seconds used (fine-grained billing)
- **Pro:** Automatic scaling; handles traffic spikes without manual intervention
- **Con:** Cold starts introduce tail latency; unpredictable for latency-sensitive use cases
- **Con:** Vendor lock-in; moving to another cloud requires rewriting and re-architecting
- **Con:** At high invocation volume, cost becomes comparable to containers but with less control

## Comparisons

| Compared to | Serverless tends to win when… | Serverless tends to lose when… |
| --- | --- | --- |
| [[containers|Containers]] | Bursty traffic, no need for always-on services, simple stateless functions | Always-on traffic, need for fine-grained control, complex multi-process workloads |
| [[kubernetes|Kubernetes]] | You want zero ops overhead and simple functions | You need control over runtime or long-running processes (> 15 min) |

## Real-world example

- **Stripe webhooks:** Serverless is ideal for webhook processing (event-driven, bursty, stateless). Stripe + AWS Lambda is a canonical example.
- **Data pipelines (heavy usage):** Netflix initially used Lambda for some data pipelines but moved to containers as volume grew (cost and concurrency limits became prohibitive).

## Related

- [[containers|Containers]]
- [[kubernetes|Kubernetes]]
- [[deployment-patterns|Deployment patterns]]

## Sources

- https://aws.amazon.com/lambda/ — AWS Lambda documentation and pricing
- https://cloud.google.com/functions/docs/ — Google Cloud Functions documentation
- https://azure.microsoft.com/en-us/services/functions/ — Azure Functions documentation
