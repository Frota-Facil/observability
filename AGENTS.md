# AGENTS.md

# Observability - SIF

Este repositório é responsável pelo monitoramento dos microserviços do Sistema Integrado de Frotas (SIF).

Seu objetivo é coletar, armazenar e visualizar métricas das APIs desenvolvidas no projeto.

---

# Objetivos

A stack de observabilidade deve fornecer:

- Monitoramento em tempo real das APIs
- Métricas de desempenho
- Métricas de disponibilidade
- Dashboards para acompanhamento dos serviços
- Alertas para falhas e degradação de desempenho

Este repositório não contém regras de negócio do sistema.

---

# Escopo

Os seguintes serviços são monitorados:

- Core Service
- Tracking Service
- Notification Service

Novos microserviços podem ser adicionados seguindo os mesmos padrões.

---

# Stack

A infraestrutura é composta por:

- Prometheus
- Grafana

Todos os componentes são executados via Docker Compose.

---

# Responsabilidades

Este projeto é responsável por:

- Configuração do Prometheus
- Configuração do Grafana
- Provisionamento de dashboards
- Provisionamento de Data Sources
- Configuração de alertas
- Versionamento das configurações

Não é responsabilidade deste projeto:

- Desenvolvimento das APIs
- Instrumentação das APIs
- Banco de dados
- RabbitMQ
- Frontend
- Aplicação Mobile

---

# Estrutura esperada

```
.
├── docker-compose.yml
├── prometheus/
│   ├── prometheus.yml
│   └── rules/
├── grafana/
│   ├── provisioning/
│   │   ├── dashboards/
│   │   └── datasources/
│   └── dashboards/
└── README.md
```

---

# Convenções

## Prometheus

Cada API deve disponibilizar um endpoint:

```
/metrics
```

As métricas devem seguir o padrão Prometheus.

Exemplos:

- http_requests_total
- http_request_duration_seconds
- process_cpu_seconds_total

---

## Grafana

Todos os dashboards devem ser provisionados automaticamente.

Não criar dashboards manualmente como configuração permanente.

Todo dashboard deve ser versionado neste repositório.

---

# Dashboards

## Overview

Deve apresentar:

- APIs online
- Requests por segundo
- Latência
- Taxa de erro

---

## Core Service

Exibir métricas específicas da API.

---

## Tracking Service

Exibir métricas específicas da API.

---

## Notification Service

Exibir métricas específicas da API.

---

# Alertas mínimos

Devem existir alertas para:

- API indisponível
- Tempo de resposta elevado
- Taxa de erro elevada
- Alto consumo de CPU
- Alto consumo de memória

---

# Boas práticas

- Manter toda configuração declarativa.
- Versionar qualquer alteração.
- Evitar duplicação de dashboards.
- Utilizar nomes consistentes para os jobs do Prometheus.
- Utilizar labels para facilitar consultas.

---

# Padrão de nomenclatura

Jobs:

```
core-service
tracking-service
notification-service
```

Dashboards:

```
Overview
Core Service
Tracking Service
Notification Service
```

---

# Diretrizes para agentes de IA

Ao modificar este repositório, priorize:

1. Simplicidade.
2. Configuração declarativa.
3. Reprodutibilidade.
4. Compatibilidade com Docker Compose.
5. Uso exclusivo de Prometheus e Grafana.
6. Baixa complexidade operacional.

Não adicionar novas ferramentas de observabilidade (como Loki, Tempo, Jaeger ou OpenTelemetry) sem uma justificativa técnica clara.