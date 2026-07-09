## ADDED Requirements

### Requirement: Observability stack runs with Docker Compose
The observability repository SHALL provide a Docker Compose stack containing Prometheus and Grafana only.

#### Scenario: Start observability stack
- **WHEN** an operator runs `docker compose up -d`
- **THEN** Prometheus starts on host port `9090`
- **AND** Grafana starts on host port `3001`

#### Scenario: Avoid admin app port conflict
- **WHEN** Grafana is started locally
- **THEN** it does not bind to port `3000`, which is used by `admin-app`

### Requirement: Prometheus scrapes SIF services through host.docker.internal
Prometheus SHALL scrape SIF microservices through host-published ports using `host.docker.internal`.

#### Scenario: Core service scrape target
- **WHEN** `core-service` is running on the host at port `3333`
- **THEN** Prometheus scrapes `http://host.docker.internal:3333/metrics` under job `core-service`

#### Scenario: Linux host gateway compatibility
- **WHEN** the stack runs on a Linux Docker engine
- **THEN** the Prometheus container can resolve `host.docker.internal` through Docker's host gateway mapping

#### Scenario: Future service target
- **WHEN** `tracking-service` or `notification-service` receives a host port and `/metrics` endpoint
- **THEN** the service can be added using the same scrape job pattern and labels

### Requirement: Prometheus targets use stable labels
Each service scrape target SHALL include stable labels for filtering and dashboard queries.

#### Scenario: Service labels are attached
- **WHEN** Prometheus scrapes a SIF service
- **THEN** scraped series include `project="sif"`, a `service` label, and an `environment` label

### Requirement: Grafana is provisioned declaratively
Grafana SHALL load data sources and dashboards from versioned provisioning files.

#### Scenario: Prometheus data source is provisioned
- **WHEN** Grafana starts
- **THEN** it has a default Prometheus data source pointing to `http://prometheus:9090`

#### Scenario: Dashboards are provisioned
- **WHEN** Grafana starts
- **THEN** dashboards from `grafana/dashboards` are available without manual creation

### Requirement: Overview dashboard shows service health
The Overview dashboard SHALL summarize availability and HTTP performance for SIF APIs.

#### Scenario: Operator opens Overview
- **WHEN** an operator opens the Overview dashboard
- **THEN** they can see API availability, request rate, latency, and error rate by service

### Requirement: Alert rules cover minimum service degradation cases
Prometheus SHALL include alert rules for service availability and performance degradation.

#### Scenario: API unavailable alert
- **WHEN** Prometheus cannot scrape a configured service for the configured duration
- **THEN** an API unavailable alert becomes active for that service

#### Scenario: Latency degradation alert
- **WHEN** p95 request latency remains above the configured threshold
- **THEN** a high latency alert becomes active for that service

#### Scenario: Error rate degradation alert
- **WHEN** the 5xx request ratio remains above the configured threshold
- **THEN** a high error rate alert becomes active for that service

#### Scenario: Runtime resource alert
- **WHEN** process CPU or resident memory remains above the configured threshold
- **THEN** a resource usage alert becomes active for that service
