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
- calcula um Score de Risco ESG (0 a 100) e valida lotes segundo um perfil definido pelo usuário;
- emite a Declaração de Devida Diligência (DDS) ao comprador, com trilha de auditoria e tratamento de dados conforme a LGPD.

## Requisitos

Os requisitos estão em [`docs/requisitos.docx`](docs/requisitos.docx), no modelo fornecido pelo professor ([`docs/modelo-requisitos.docx`](docs/modelo-requisitos.docx)). A versão em Markdown, [`docs/requisitos.md`](docs/requisitos.md), é a fonte editável.

| Tipo | Quantidade |
|---|---|
| Requisitos funcionais | 40 |
| Requisitos não funcionais | 38 |
| Regras de negócio | 28 |
| Requisitos suplementares | 3 |

O documento inclui um glossário (seção 1.4) com os termos centrais do domínio: critério de conformidade, status de conformidade, perfil de validação e lote validado.

## Decisões de projeto

- **Critérios modulares:** cada critério é uma instância de um tipo de avaliação parametrizável. Os critérios do sistema são protegidos (só a equipe técnica os altera); o Analista de Conformidade cria e edita os do usuário. Toda edição gera nova versão, e o histórico é preservado.
- **Status por critério, sem status único:** o status de uma propriedade ou fornecedor existe para cada critério (conforme, inconforme, não definido ou não aplicável). O do lote é a distribuição de massa entre esses status, calculada por balanço de massa.
- **Banco de dados:** PostgreSQL com PostGIS, com o grafo de lotes modelado em tabelas.
- **LGPD e auditoria:** a trilha de auditoria é append-only, mas retida só pelo período necessário e anonimizada mediante solicitação do titular.

## Diagrama de classes

<!-- TODO: exportar o diagrama gerado por você e salvar em docs/diagrama-de-classes.png -->
![Diagrama de classes](docs/diagrama-de-classes.png)

## Documentação do projeto

Os requisitos estão em [`docs/`](docs/) (ver seção "Requisitos"). <!-- TODO: o arquivo "Documentação do Projeto.md" não existe no repositório; criá-lo (casos de uso, diagramas, protótipo, considerações finais e referências) e linkar aqui -->


## Conclusão / Aprendizado

<!-- TODO: preencher ao final do trabalho -->

- **Dificuldades enfrentadas:**
- **Como foram resolvidas:**
- **Principais aprendizados:**

## Declaração de uso de IA

- **Ferramenta e modelo:** Claude Code (Claude Sonnet 5.5, Anthropic)
- **Partes do trabalho:** criação do `README.md` e do `.gitignore` do repositório; revisão e reescrita dos requisitos (atomicidade, clareza, testabilidade e consistência); e geração do `requisitos.docx` a partir do modelo do professor. As decisões de escopo e de regras de negócio foram tomadas por mim.
- **Aprendizado com o uso:** <!-- TODO: preencher -->
