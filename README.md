# ToggleMaster — Ambiente de Desenvolvimento Local

Ambiente de desenvolvimento local do projeto **ToggleMaster** (Tech Challenge
FIAP/POSTECH), via Docker Compose. Permite rodar e testar os 5 microsserviços
com telemetria (métricas, logs e traces) localmente, sem depender do cluster
EKS na nuvem.

Este repositório é útil para desenvolvimento e testes rápidos de instrumentação
antes de subir qualquer mudança para o [`togglemaster-gitops`](https://github.com/leonardofmoraes/togglemaster-gitops).

## O que sobe com o docker-compose

| Serviço | Linguagem | Descrição |
|---|---|---|
| `auth-service` | Go | Autenticação |
| `flag-service` | Python (Flask) | Gerenciamento de feature flags |
| `targeting-service` | Python (Flask) | Regras de segmentação/targeting |
| `analytics-service` | Python (Flask) | Coleta de eventos e analytics |
| `evaluation-service` | Go | Avaliação de flags em tempo real |
| `otel-collector` | — | Recebe e roteia telemetria (métricas, logs, traces) |
| `jaeger` | — | Backend de tracing para visualização local dos traces |

## Pré-requisitos

- Docker Desktop
- Docker Compose
- Portas livres localmente (verifique conflitos antes de subir, especialmente se
  já tiver outros serviços rodando nas mesmas portas)

⚠️ Se estiver rodando este projeto a partir de uma pasta sincronizada com OneDrive
(ou outro serviço de nuvem), alguns problemas de montagem de volume podem ocorrer
no Docker Desktop no Windows. Se isso acontecer, tente mover o projeto para uma
pasta fora do OneDrive ou pausar a sincronização durante o desenvolvimento.

## Como rodar

```bash
docker-compose up --build
```

Para subir em background:

```bash
docker-compose up --build -d
```

Para derrubar tudo:

```bash
docker-compose down
```

## Testando a telemetria localmente

1. Suba o ambiente com `docker-compose up`
2. Faça uma requisição de teste em algum dos microsserviços (ex.: `evaluation-service`)
3. Acesse o Jaeger UI em `http://localhost:16686` para visualizar os traces distribuídos gerados
4. Confirme que os 5 microsserviços aparecem gerando spans corretamente

## Variáveis de ambiente

Copie o arquivo de exemplo e preencha os valores necessários antes de subir o ambiente:

```bash
cp .env.example .env
```

⚠️ O `.env` nunca deve ser commitado com valores reais — apenas o `.env.example` é versionado.

## Estrutura

- `auth-service/` — código-fonte, Dockerfile e instrumentação OTel do serviço de autenticação
- `flag-service/` — código-fonte, Dockerfile e instrumentação OTel do serviço de flags
- `targeting-service/` — código-fonte, Dockerfile e instrumentação OTel do serviço de targeting
- `analytics-service/` — código-fonte, Dockerfile e instrumentação OTel do serviço de analytics
- `evaluation-service/` — código-fonte, Dockerfile e instrumentação OTel do serviço de avaliação
- `docker-compose.yml` — orquestração de todos os serviços + OTel Collector + Jaeger
- `otel-collector-config.yaml` — configuração do OpenTelemetry Collector (recebimento e roteamento de telemetria)

## Repositórios relacionados

- [`togglemaster-gitops`](https://github.com/leonardofmoraes/togglemaster-gitops) — manifests Kubernetes, ArgoCD e stack de observabilidade
- [`togglemaster-infra`](https://github.com/leonardofmoraes/togglemaster-infra) — Terraform: VPC, EKS, RDS, Redis
