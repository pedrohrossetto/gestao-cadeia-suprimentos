# ADR-001: Monólito modular, MVC no backend e SPA com API OpenAPI

**Status:** Aceito
**Data:** 25/11/2026
**Decisores:** Pedro Henrique Carteli Rossetto

## Contexto

O sistema é desenvolvido por uma pessoa, como trabalho de APS, mas deve poder evoluir a produto. A interface é muito interativa (mapa com milhares de propriedades, painéis, grafo de lotes) e a API deve ser documentada em OpenAPI (RNF-13). O domínio tem quatro áreas coesas: Cadastro e território, Rastreabilidade, Conformidade, Acesso e auditoria.

## Decisão

- **Monólito modular:** um único código-fonte, com um módulo por área de domínio. Os módulos se comunicam por interfaces internas e por eventos (ADR-002), nunca acessando as tabelas uns dos outros diretamente.
- **MVC no backend:** Controller = rotas FastAPI; Model = serviços de aplicação e regras de domínio; View = contrato JSON definido pelos modelos Pydantic e publicado em OpenAPI.
- **SPA no frontend:** React ou Vue com MapLibre, consumindo apenas a API. Os tipos TypeScript são gerados do OpenAPI.
- **Acesso a dados sem ORM:** psycopg com SQL PostGIS escrito à mão, organizado em repositórios por módulo.
- **API-first:** RNF-13 passa de Pós-piloto para Demo (requisitos v3).

## Alternativas consideradas

| Opção | Complexidade | Interatividade | Custo operacional |
|---|---|---|---|
| **Monólito modular + SPA (escolhida)** | Média | Alta | Baixo |
| MVC renderizado no servidor (Django/templates) | Baixa | Baixa para mapa e painéis | Baixo |
| Microsserviços | Alta | Alta | Alto para uma pessoa |

**Sem ORM:** as consultas centrais (geometria, grafo de lotes, junções temporais) são SQL específico de PostGIS e de CTEs recursivas, onde um ORM ajuda pouco. Em troca, migrações e mapeamento ficam por conta do projeto.

## Consequências

- Uma única implantação e um único banco simplificam desenvolvimento e testes.
- Os limites entre módulos dependem de disciplina; um módulo pode ser extraído depois, se necessário.
- O contrato OpenAPI é a fonte da verdade entre frontend e backend; mudança de contrato quebra o build do frontend, o que é desejado.
- Migrações SQL precisam de um executor próprio (ver ADR-004).
