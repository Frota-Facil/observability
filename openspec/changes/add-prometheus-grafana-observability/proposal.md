# Add Prometheus Grafana Observability

## Summary

Add a Docker Compose based observability stack for SIF using Prometheus and Grafana. Prometheus will scrape metrics from SIF microservices through `host.docker.internal`, and Grafana will provision the Prometheus data source, dashboards, and alerting resources from versioned files.

## Problem

SIF services currently run independently and do not have a centralized, reproducible monitoring setup. Operators need a local stack that can answer basic runtime questions:

- Which APIs are online?
- What is the request rate?
- What is the latency distribution?
- What is the error rate?
- Are CPU or memory metrics showing degradation?

## Proposed Change

Create the expected observability repository structure:

- `docker-compose.yml`
- `prometheus/prometheus.yml`
- `prometheus/rules/*.yml`
- `grafana/provisioning/datasources/*.yml`
- `grafana/provisioning/dashboards/*.yml`
- `grafana/dashboards/*.json`
- `README.md`

Prometheus will run in its own container and scrape:

- `core-service` at `host.docker.internal:3333`
- `tracking-service` at a configurable host port
- `notification-service` at a configurable host port
- optionally `ai-service` at `host.docker.internal:8001` if it exposes `/metrics`

Grafana will run in its own container, read Prometheus as the default data source, and provision dashboards automatically.

## Assumptions

- Each API exposes Prometheus metrics at `/metrics`.
- `core-service` currently listens on port `3333`.
- `ai-service` appears to be a FastAPI service intended for port `8001`, but it does not currently expose `/metrics`.
- Separate `tracking-service` and `notification-service` repositories were not present in the adjacent folder during planning, so their ports should be configurable placeholders until those services exist or are identified.
- The observability container reaches host-published service ports through `host.docker.internal`.

## Non-Goals

- Do not instrument application code in this repository.
- Do not add Loki, Tempo, Jaeger, OpenTelemetry Collector, or other observability components.
- Do not manage application databases, RabbitMQ, MinIO, or frontend containers.
- Do not rely on manually created Grafana dashboards as permanent configuration.

## Impact

- Adds a reproducible local monitoring stack.
- Makes service health and performance visible from Grafana.
- Establishes Prometheus job naming and alerting conventions for future SIF services.
- Requires monitored services to expose `/metrics`; any missing instrumentation must be handled in the service repositories.
