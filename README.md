# ToggleMaster — Ambiente de Desenvolvimento Local

Ambiente de desenvolvimento local do projeto **ToggleMaster** (Tech Challenge
FIAP/POSTECH), via Docker Compose. Permite rodar e testar os 5 microsserviços
com telemetria completa (traces via OpenTelemetry + Jaeger) localmente, sem
depender do cluster EKS na nuvem.

Este repositório é útil para desenvolvimento e testes rápidos de instrumentação
antes de subir qualquer mudança para o [`togglemaster-gitops`](https://github.com/leonardofmoraes/togglemaster-gitops).

## Serviços

| Serviço | Linguagem | Porta | Dependências |
|---|---|---|---|
| `auth-service` | Go | `8001` | `postgres-auth` |
| `flag-service` | Python (Flask) | `8002` | `postgres-flag`, `auth-service` |
| `targeting-service` | Python (Flask) | `8003` | `postgres-targeting`, `auth-service` |
| `evaluation-service` | Go | `8004` | `redis`, `flag-service`, `targeting-service`, AWS SQS |
| `analytics-service` | Python (Flask) | `8005` | AWS SQS, AWS DynamoDB |

### Infraestrutura de apoio (local)

| Serviço | Porta | Descrição |
|---|---|---|
| `postgres-auth` | `5433` | Banco do auth-service (`auth_db`) |
| `postgres-flag` | `5434` | Banco do flag-service (`flags_db`) |
| `postgres-targeting` | `5435` | Banco do targeting-service (`targeting_db`) |
| `redis` | `6379` | Cache usado pelo evaluation-service |
| `otel-collector` | `4317` (gRPC) / `4318` (HTTP) | Recebe e roteia telemetria (OTLP) |
| `jaeger` | `16686` | UI para visualizar traces distribuídos localmente |

## Instrumentação OpenTelemetry

Os 5 microsserviços exportam traces via OTLP gRPC para o `otel-collector`, que
roteia os dados para o **Jaeger** (neste ambiente local — em produção, para o
Prometheus/Loki/New Relic, conforme configurado no `togglemaster-gitops`).

A abordagem de instrumentação varia
