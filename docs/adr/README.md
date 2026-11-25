# Registros de Decisão de Arquitetura (ADR)

Decisões macro do sistema, tomadas antes da revisão dos requisitos e do protótipo de interface. Cada ADR registra contexto, decisão, alternativas e consequências.

| ADR | Título | Status |
|---|---|---|
| [ADR-001](0001-estilo-arquitetural.md) | Monólito modular, MVC no backend e SPA com API OpenAPI | Aceito |
| [ADR-002](0002-eventos-outbox.md) | Arquitetura orientada a eventos com outbox transacional | Aceito |
| [ADR-003](0003-conformidade-por-participacao.md) | Conformidade do lote por participação de origem × status | Aceito |
| [ADR-004](0004-multiempresa.md) | Multiempresa com schema e papel de banco por empresa | Aceito |
| [ADR-005](0005-passaporte-cadeia-de-hash.md) | Passaporte digital por etapa com cadeia de hash | Aceito |
| [ADR-006](0006-dados-de-referencia.md) | Dados de referência compartilhados e versionados por data | Aceito |
| [ADR-007](0007-implantacao-gcp.md) | Implantação no Google Cloud | Aceito |
| [ADR-008](0008-papel-em-relacao-ao-erp.md) | Papel do sistema em relação ao ERP | Aceito |

## Resumo das escolhas

- **Escopo:** trabalho final de APS, com arquitetura pensada para evoluir a produto real.
- **Frontend:** SPA (React/Vue + MapLibre) consumindo API REST documentada em OpenAPI desde o estágio Demo.
- **Backend:** Python com FastAPI, psycopg e SQL PostGIS direto (sem ORM); modelos Pydantic; tipos TypeScript gerados do OpenAPI.
- **Banco:** PostgreSQL + PostGIS; massa armazenada em quilogramas inteiros (`bigint`).
- **Nuvem:** Google Cloud (Cloud Run, Cloud SQL, Cloud Storage, Cloud Scheduler).
- **ERP:** o sistema complementa o ERP da empresa; o ERP é a fonte das massas e do fiscal, e o sistema é a fonte de origem, conformidade e passaportes. Importação via API ou CSV no Pós-piloto (ADR-008).
- **Fora do escopo:** telemetria.
- **Requisitos:** as decisões estão refletidas em [`docs/v3/requisitos.md`](../v3/requisitos.md).
