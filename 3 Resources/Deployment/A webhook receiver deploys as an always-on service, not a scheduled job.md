---
title: "A webhook receiver deploys as an always-on service, not a scheduled job"
created: 2026-06-26
type: lesson
status: seedling
source: "session 2026-06-26 feat/appsflyer-push-layer; DESIGN_PUSH.md"
tags: [deployment, webhook, kubernetes, high-availability, appsflyer, leo-cdp]
---

# A webhook receiver deploys as an always-on service, not a scheduled job

When choosing how to deploy a webhook **receiver** (e.g. the AppsFlyer Push API service), the deciding factor is its **lifecycle and direction**, not its size. A receiver is **inbound + always-on + stateless**, which is the opposite of a poller/ETL job (outbound + scheduled + run-then-exit).

Consequence: every scheduled deploy primitive — cron, Kubernetes **CronJob**, Airflow — is the WRONG model for a receiver, even though they're the right model for the matching Pull/ETL job in the same project. A receiver must run as a **long-running service** with:
- a stable **public HTTPS endpoint** (TLS termination at an ingress/reverse proxy),
- **≥2 replicas** for HA (so rolling deploys / node drains don't bounce requests — webhooks like AppsFlyer are at-least-once and retry on non-2xx, but every bounce delays data and burns retry budget),
- **autoscaling** for bursty real-time traffic,
- per-event **idempotency** (dedupe key) since at-least-once + multiple replicas guarantee duplicates.

Recommended target: a managed-Kubernetes **Deployment** (not CronJob) + Service + Ingress(LB)+TLS + HPA + Secret. Fallback for low volume / pre-prod: a single VM + auto-TLS reverse proxy (e.g. Caddy) + container restart=always — simple but a single point of failure.

Gotcha when picking serverless/FaaS for a receiver: it fits sporadic webhooks, but cold starts add latency under at-least-once retries, and sustained streaming traffic suits a long-running service better than per-invocation billing; also a stdlib http.server app needs a handler/WSGI adapter first.

See [[AppsFlyer Push API is the inverse of the Pull API]] and [[AppsFlyer Push layer appends per-event while Pull replaces the day]].

## Related

- [[AppsFlyer Push API is the inverse of the Pull API]]
- [[AppsFlyer Push layer appends per-event while Pull replaces the day]]
