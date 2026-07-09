# Design

## Runtime Topology

The observability stack runs separately from SIF application containers:

- `prometheus` listens on host port `9090`.
- `grafana` listens on host port `3001` to avoid conflicting with `admin-app` on `3000`.
- Prometheus scrapes host-published service ports through `host.docker.internal`.

On Linux Docker engines, Compose should include:

```yaml
extra_hosts:
  - "host.docker.internal:host-gateway"
```

This keeps the same target host name working across Docker Desktop and Linux.

## Prometheus Configuration

Use one scrape job per service:

- `core-service`
- `tracking-service`
- `notification-service`
- `ai-service` when enabled

Each job scrapes `/metrics` and adds stable labels:

- `service`
- `environment`
- `project="sif"`

Initial targets:

```yaml
- job_name: core-service
  metrics_path: /metrics
  static_configs:
    - targets: ["host.docker.internal:3333"]
      labels:
        project: sif
        service: core-service
        environment: local
```

Tracking and notification targets should use the same shape with documented placeholder ports or environment-substituted files if the team wants per-machine overrides.

## Metrics Contract

The stack should expect standard Prometheus HTTP metrics from each API:

- `up`
- request count metric such as `http_requests_total`
- request duration histogram such as `http_request_duration_seconds`
- process CPU metric such as `process_cpu_seconds_total`
- process memory metric such as `process_resident_memory_bytes`

Because metric names can differ by framework, dashboards should prefer common Prometheus patterns and keep service-specific panels isolated when exact metric names are not shared across services.

## Grafana Provisioning

Provision Prometheus as the default data source:

- name: `Prometheus`
- type: `prometheus`
- url: `http://prometheus:9090`
- access: `proxy`
- default: `true`

Provision dashboards from `grafana/dashboards`.

Recommended dashboards:

- `Overview`: service availability, request rate, latency p95, error rate.
- `Core Service`: core-service specific request and runtime panels.
- `Tracking Service`: placeholder dashboard following the same conventions.
- `Notification Service`: placeholder dashboard following the same conventions.
- `AI Service`: optional dashboard if the service is instrumented.

## Alerting

Prometheus alert rules should cover:

- API unavailable: `up == 0`.
- High latency: p95 above a defined threshold for several minutes.
- High error rate: 5xx ratio above a threshold.
- High CPU: sustained CPU increase by process.
- High memory: resident memory above a threshold.

Alert thresholds should be conservative for local development and easy to tune later. Alert delivery channels are out of scope for the first version unless Grafana contact points are explicitly requested later.

## Implementation Order

1. Add Compose files and static Prometheus config.
2. Add Prometheus alert rules.
3. Add Grafana data source and dashboard provisioning.
4. Add initial dashboard JSON files.
5. Add README with startup and validation commands.
6. Validate with `docker compose config`.
7. Run the stack and check Prometheus targets.

## Risks

- If a service does not expose `/metrics`, Prometheus will show the target as down or scrape errors.
- If services are not published to host ports, `host.docker.internal` cannot reach them.
- Dashboard queries may need adjustment after seeing the real metric names emitted by Fastify and FastAPI instrumentation libraries.
- Grafana port `3001` avoids the current admin app conflict, but it should be documented clearly.
