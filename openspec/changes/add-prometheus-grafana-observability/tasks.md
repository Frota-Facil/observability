# Tasks

## 1. Scaffold Observability Stack

- [x] Add `docker-compose.yml` with `prometheus` and `grafana` services.
- [x] Expose Prometheus on `9090`.
- [x] Expose Grafana on `3001`.
- [x] Add persistent volumes for Prometheus and Grafana data.
- [x] Add `extra_hosts: ["host.docker.internal:host-gateway"]` for Linux compatibility.

## 2. Configure Prometheus

- [x] Create `prometheus/prometheus.yml`.
- [x] Configure scrape job `core-service` targeting `host.docker.internal:3333`.
- [x] Configure placeholder scrape jobs for `tracking-service` and `notification-service`.
- [x] Optionally configure `ai-service` targeting `host.docker.internal:8001` when `/metrics` exists.
- [x] Add stable labels: `project`, `service`, and `environment`.

## 3. Add Alert Rules

- [x] Create `prometheus/rules/services.yml`.
- [x] Add alert for service unavailable.
- [x] Add alert for high p95 latency.
- [x] Add alert for elevated 5xx error rate.
- [x] Add alert for high process CPU.
- [x] Add alert for high resident memory.

## 4. Provision Grafana

- [x] Create Prometheus data source provisioning under `grafana/provisioning/datasources`.
- [x] Create dashboard provider provisioning under `grafana/provisioning/dashboards`.
- [x] Store all dashboard JSON files under `grafana/dashboards`.

## 5. Add Dashboards

- [x] Add `Overview` dashboard.
- [x] Add `Core Service` dashboard.
- [x] Add `Tracking Service` dashboard placeholder.
- [x] Add `Notification Service` dashboard placeholder.
- [x] Add optional `AI Service` dashboard only if metrics are exposed.

## 6. Document Usage

- [x] Add `README.md` with prerequisites.
- [x] Document required `/metrics` endpoint in monitored services.
- [x] Document expected local service ports.
- [x] Document how to start with `docker compose up -d`.
- [x] Document validation through Prometheus targets and Grafana dashboards.

## 7. Validate

- [x] Run `docker compose config`.
- [x] Start the stack.
- [x] Confirm Prometheus UI is reachable at `http://localhost:9090`.
- [x] Confirm Grafana UI is reachable at `http://localhost:3001`.
- [x] Confirm `core-service` target appears in Prometheus.
- [x] Confirm dashboards are provisioned automatically in Grafana.
