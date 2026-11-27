# Gestão de Cadeia de Suprimentos

**Disciplina:** Análise e Projeto de Sistemas (APS) — Bacharelado em Ciência da Computação
**Professor:** Mateus de Freitas Invernisi
**Período:** 2026/2
**Aluno:** Pedro Henrique Carteli Rossetto
**Trabalho:** Trabalho Final (individual)

## Descrição do sistema

Sistema para acompanhar e gerir a cadeia de suprimentos agrícolas: fornecedores, lotes, entradas e saídas de material, do campo até o cliente final. Permite saber quem fornece, o que fornece e para onde cada lote vai, rastreando a origem do material e dando base para relatórios e controles de conformidade.

Na prática, o sistema:

- avalia a conformidade socioambiental de fornecedores, propriedades e lotes **por critério** (embargos ambientais, lista suja do trabalho análogo à escravidão, situação do CAR), com critérios do sistema e critérios criados pelo próprio usuário;
- calcula o status de conformidade de cada lote por **balanço de massa**, acompanhando misturas e transformações ao longo da cadeia;
- valida lotes segundo um perfil de validação definido pelo usuário;
- registra cada etapa do lote em um **passaporte digital** encadeado por hash, que comprova a origem e a conformidade do lote até a venda;
- Funciona como serviço na nuvem para várias empresas clientes: cada empresa acessa apenas os próprios dados, e as listas públicas de conformidade (embargos, lista suja, SICAR) são compartilhadas entre todas.
- Mantém trilha de auditoria e tratamento de dados conforme a LGPD.

## Requisitos

A versão vigente dos requisitos é a v3: [`docs/v3/requisitos.docx`](docs/v3/requisitos.docx), no modelo fornecido pelo professor ([`docs/modelo-requisitos.docx`](docs/modelo-requisitos.docx)), com versões em Markdown ([`docs/v3/requisitos.md`](docs/v3/requisitos.md)) e YAML estruturado ([`docs/v3/requisitos.yaml`](docs/v3/requisitos.yaml)). As versões anteriores e a organização das pastas estão descritas em [`docs/README.md`](docs/README.md).

| Tipo | Quantidade |
|---|---|
| Requisitos funcionais | 59 |
| Requisitos não funcionais | 28 |
| Regras de negócio | 24 |
| Requisitos suplementares | 4 |

O documento inclui um glossário (seção 1.4) com os termos centrais do domínio, como critério de conformidade, status de conformidade, participação de origem, passaporte digital, perfil de validação e lote validado. O Apêndice C lista as alterações em relação à v2.

## Decisões de projeto

- **Critérios modulares:** cada critério é uma instância de um tipo de avaliação parametrizável. Os critérios do sistema são protegidos (só a equipe técnica os altera); o Analista de Conformidade cria e edita os do usuário. Toda edição gera nova versão, e o histórico é preservado.
- **Status por critério, sem status único:** o status de uma propriedade ou fornecedor existe para cada critério (conforme, inconforme, não definido ou não aplicável). O do lote é a distribuição de massa entre esses status, derivada da participação de cada origem na massa do lote e do status vigente de cada origem.
- **LGPD e auditoria:** a trilha de auditoria é append-only, mas retida só pelo período necessário e anonimizada mediante solicitação do titular.

As decisões de arquitetura estão registradas em [`docs/adr/`](docs/adr/):

| ADR | Decisão |
|---|---|
| [001](docs/adr/0001-estilo-arquitetural.md) | Monólito modular com MVC no backend (FastAPI, psycopg e SQL PostGIS, sem ORM) e SPA consumindo API OpenAPI |
| [002](docs/adr/0002-eventos-outbox.md) | Arquitetura orientada a eventos com outbox transacional no PostgreSQL |
| [003](docs/adr/0003-conformidade-por-participacao.md) | Conformidade do lote por participação de origem × status, sem propagar status aos lotes |
| [004](docs/adr/0004-multiempresa.md) | Multiempresa com schema e papel de banco por empresa |
| [005](docs/adr/0005-passaporte-cadeia-de-hash.md) | Passaporte digital por etapa, encadeado por hash (RFC 8785) |
| [006](docs/adr/0006-dados-de-referencia.md) | Dados de referência públicos compartilhados e versionados por data |
| [007](docs/adr/0007-implantacao-gcp.md) | Implantação no Google Cloud (Cloud Run, Cloud SQL com PostGIS, Cloud Storage) |
| [008](docs/adr/0008-papel-em-relacao-ao-erp.md) | O sistema complementa o ERP: importa dele as movimentações e é a fonte de origem, conformidade e passaportes |

## Casos de uso e diagramas

Os casos de uso da v3 estão especificados em [`docs/v3/casos-de-uso/`](docs/v3/casos-de-uso/), cada um com seu diagrama de casos de uso: Registrar lotes, Validar conformidade, Vender lote, Armazenar lote e Transformar lote. O [cenário do ciclo de vida do lote](docs/v3/casos-de-uso/cenario-ciclo-de-vida-do-lote.md) encadeia esses casos em um exemplo de ponta a ponta, com diagrama de atividades.

<!-- TODO: diagrama de classes da v3. Os diagramas de classes existentes são os da v2 (docs/v2/diagrams/plantuml/) e não incluem as mudanças da v3. -->

## Documentação do projeto

Toda a documentação está em [`docs/`](docs/), organizada por versão (ver [`docs/README.md`](docs/README.md)). <!-- TODO: o arquivo "Documentação do Projeto.md" não existe no repositório; criá-lo (casos de uso, diagramas, protótipo, considerações finais e referências) e linkar aqui -->


## Conclusão / Aprendizado

<!-- TODO: preencher ao final do trabalho -->

- **Dificuldades enfrentadas:**
- **Como foram resolvidas:**
- **Principais aprendizados:**

## Declaração de uso de IA

- **Ferramenta e modelo:** Claude Code (Claude Sonnet 5.5 e Claude Opus 5.5, Anthropic)
- **Partes do trabalho:** criação do `README.md` e do `.gitignore` do repositório; revisão e reescrita dos requisitos (atomicidade, clareza, testabilidade e consistência); geração dos `requisitos.docx` a partir do modelo do professor; revisão da documentação e discussão das alternativas de arquitetura; redação dos ADRs a partir das decisões tomadas; geração dos requisitos v3 e dos respectivos diagramas de casos de uso e de classes; e reorganização das pastas por versão. As decisões de escopo, de arquitetura e de regras de negócio foram tomadas por mim.
- **Aprendizado com o uso:** <!-- TODO: preencher -->
