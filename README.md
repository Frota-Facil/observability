# Observability - SIF

Stack local de observabilidade do SIF com Prometheus e Grafana.

## Pre-requisitos

- Docker
- Docker Compose
- APIs monitoradas expondo metricas Prometheus em `/metrics`

## Servicos monitorados

| Servico | Job Prometheus | Porta local esperada |
| --- | --- | --- |
| Core Service | `core-service` | `3333` |
| Tracking Service | `tracking-service` | `3334` |
| Notification Service | `notification-service` | `3335` |

O `ai-service` pode ser adicionado em `host.docker.internal:8001` quando expor `/metrics`.

## Como iniciar

```bash
docker compose up -d
```

Prometheus fica disponivel em:

```text
http://localhost:9090
```

Grafana fica disponivel em:

```text
http://localhost:3001
```

Credenciais locais do Grafana:

```text
admin / admin
```

## Validacao

1. Acesse `http://localhost:9090/targets`.
2. Confira os targets `core-service`, `tracking-service` e `notification-service`.
3. Acesse `http://localhost:3001`.
4. Abra a pasta `SIF` e confira os dashboards `Overview`, `Core Service`, `Tracking Service` e `Notification Service`.

Se uma API ainda nao existir ou nao expuser `/metrics`, o target correspondente aparecera como indisponivel.
