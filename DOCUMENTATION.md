# Plataforma Fluxium — Documentação de Apresentação

## Visão Geral
- O que é: Plataforma modular de microserviços para orquestração de fluxos de negócio, exposição de APIs e gestão administrativa.
- Objetivo: Permitir integração de serviços, automação de decisões e gestão de entidades (empresas, produtos, workspaces) com observabilidade e deploy industriais.
- Público-alvo: Engenheiros, DevOps, Product Managers e stakeholders técnicos.

## Multitenancy, Multi-produto e Workspaces personalizados
- **Multitenancy:** A plataforma foi projetada para suportar múltiplos tenants (clientes) isolados; cada tenant pode ter suas próprias políticas, dados e configurações, garantindo segregação lógica e controles de acesso por tenant.
- **Multi-produto:** Cada tenant pode gerir vários produtos independentes — cada produto possui configurações, fluxos, integrações e pipelines próprios, permitindo oferecer diferentes linhas de produto sobre a mesma infraestrutura.
- **Workspaces personalizados:** Workspaces são definíveis por tenant e por produto para agrupar times, ambientes e recursos. Cada workspace pode ter permissões, integrações e parâmetros customizados (ex.: conexões, limites, templates de fluxo).
- **Impacto operacional:** Recomenda-se planejar namespaces/tenants no orquestrador (Kubernetes) e segregação de configurações em `fluxium-admin`; observabilidade e métricas devem incluir labels por tenant/product/workspace para permitir alertas e dashboards dedicados.


## Principais componentes
- `fluxium-admin`: API e ferramentas administrativas para gestão de entidades, usuários e configurações.
- `fluxium-gateway`: API Gateway com plugins (autenticação, rate-limit, cache, forward, etc.), roteamento e políticas.
- `fluxium-engine`: Motor central de orquestração e coordenação de execuções.
- `decision-service`: Serviço dedicado a decisões de negócio e regras.
- `light-api-service`: API leve para integrações e fachada simplificada.
- `workflow-service`: Execução e coordenação de grafos/workflows.
- `fluxium-deploy`: Artefatos de deploy, Docker Compose, e configurações de observabilidade.
- `fluxium-ui`: Frontend comercial e painel administrativo.

## Arquitetura (resumo)
- Fluxo: Requisições externas → `fluxium-gateway` (validação/autenticação) → roteamento para serviços internos (`fluxium-admin`, `light-api-service`, `workflow-service`, etc.).
- `fluxium-engine` coordena execuções que envolvem `decision-service` e `workflow-service` conforme necessidades do negócio.
- Comunicação entre serviços via HTTP/REST; persistência e caches definidos por serviço.
- Observabilidade e infra centralizada por meio dos manifests em `fluxium-deploy`.

### Modos de operação
Abaixo está o diagrama de modos de operação da plataforma:

![Modos de operação](modos.png)

## Observabilidade e Infra
- Métricas: Prometheus (configs em `fluxium-deploy`).
- Dashboards: Grafana com datasources e dashboards prontos (configs em `fluxium-deploy/grafana`).
- Tracing: Tempo (Tempo/TempoDB) — configurações presentes em `fluxium-deploy`.
- Logs: Coleta via Promtail → Loki.
- Contêineres: Suporte via Docker Compose para ambientes locais; recomenda-se Kubernetes para produção.

## Quickstart (local, mínimo)
### Pré-requisitos
- `git`, `docker`, `docker compose` (ou `docker-compose`), `poetry`/`pip` para execução local de serviços em Python, se preferir não usar containers.

### Passos rápidos
1. Clonar o repositório:

```bash
git clone <repo-url>
cd fluxium-plataform
```

2. Subir a infraestrutura mínima (exemplo com Compose):

```bash
cd fluxium-deploy/docker
docker compose up -d
```

3. Executar serviços individualmente (opcional): cada serviço tem README e `pyproject.toml` — por exemplo em `fluxium-gateway`:

```bash
cd fluxium-gateway
poetry install
poetry run python -m app.main
```

Observação: também é possível executar serviços via imagens Docker geradas pelo pipeline de CI/CD.

## Deployment
- Artefatos de deploy e overrides estão em `fluxium-deploy/` (compose, overrides, configs de observabilidade).
- Pipelines: ver `workflows/` e `scripts/ci/` para referência de publicação e build.
- Recomendações para produção: orquestrador (Kubernetes), gerenciamento de secrets, volumes persistentes, e limites de recursos por serviço.

## APIs, Testes e Qualidade
- O gateway possui suíte de testes em `fluxium-gateway/tests/` cobrindo plugins e comportamento (ex.: autenticação, cache, forward).
- Documentação de API: `fluxium-gateway/openapi.json` serve como fonte para geração de documentação OpenAPI.

## Resumo Executivo (para apresentação)
- Valor: Integração rápida, controle centralizado e capacidade de orquestração de fluxos de negócio.
- Diferenciais: Gateway extensível por plugins; motor de workflows desacoplado; empacotamento para deploy com observabilidade integrada.
- Próximos passos sugeridos: demo end-to-end com um fluxo real, validação de SLAs e plano de rollout para produção.

---

## Contatos e Recursos
- README principais por serviço: ver pastas `fluxium-admin`, `fluxium-gateway`, `fluxium-engine`, `workflow-service`.
- Arquivos de deploy: `fluxium-deploy/`.
- Para dúvidas ou ajuda com execução local, solicite que eu gere passos específicos ou slides de apresentação.
