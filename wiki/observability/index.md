---
name: Observability decisions
type: index
domain: observability
tags:
  - index
  - observability
aliases:
  - Observability guide
  - Logging, metrics, tracing
last_updated: 2026-04-12
sources: []
---

# Observability decisions

**Quick reference:** Metrics vs logs vs traces? Which observability platform? How much logging is too much?

*(In progress; contribute a page!)*

## Topics to add

- Metrics (Prometheus, Datadog) vs logs (ELK, Splunk) vs traces (Jaeger, DataDog APM)
- Cardinality explosion (metrics + high-cardinality labels)
- Sampling strategies
- Alerting (threshold-based vs anomaly-based)
- On-call burden + observability
- Cost optimization (storage, sampling)

## Key questions

**Q: Logs or metrics?**
- Logs: specific events, debugging, rare events
- Metrics: trends, aggregation, cost-effective at scale

**Q: How much logging?**
- Too much: overwhelming storage costs, hard to find signal
- Too little: can't debug when things break
- Right amount: log errors, latency outliers, unusual events

**Q: Sampling?**
- Log everything: infeasible at extreme scale
- Random sampling: bias toward fast requests
- Intelligent sampling: prioritize error traces, slow requests

## Related domains

- [[patterns/index|Patterns]] — bulkheads, circuit breakers (observability enables these)
- [[deployment/index|Deployment]] — observability needed for safe deployments
