# Requisitos: O que o sistema deve fazer

Versão revisada do [requisitos.md](requisitos.md). Os IDs originais foram mantidos para não quebrar referências; itens divididos usam subitens (ex.: RF-01.1). Os requisitos adicionais estão em [requisitos-complementares.md](requisitos-complementares.md).

## Convenções

- **Prioridade (MoSCoW):** **M** = deve, **S** = deveria, **C** = poderia.
- **Verificação:** **T** = teste automatizado, **D** = demonstração, **I** = inspeção, **A** = análise.
- **Origem:** requisito que o item complementa; `—` quando é independente.

## Resumo das mudanças em relação ao original

| Mudança | Motivo |
|---|---|
| RF-01 dividido em RF-01.1 a RF-01.3; a importação de arquivos geoespaciais virou RF-06 | O RF-01 reunia várias exigências; o RF-06 estava vago na numeração |
| O enriquecimento com embargos saiu do RF-01 e ficou no RF-08 | Evitar duplicidade com RF-07 e RF-08 |
| RF-02 dividido em RF-02.1 a RF-02.4 | Itens atômicos e testáveis |
| RF-07 reescrito, sem repetir conformidade geral | Sobrepunha RF-01 e RF-08 |
| Integração com ERP/telemetria virou RF-12; RNF-01 trata só da robustez dela | A integração em si é funcional |
| RN-01 e RN-02 ganharam prioridade, verificação e critério mais objetivo | Estavam sem ambos |


## Requisitos Funcionais

### Fornecedores e propriedades

| ID | Requisito | Prior. | Verif. |
|---|---|---|---|
| RF-01.1 | O sistema deve permitir cadastrar fornecedores com informações básicas (nome, CPF/CNPJ, contato). | M | T |
| RF-01.2 | O sistema deve permitir cadastrar, para cada fornecedor, uma ou mais propriedades com informações básicas e localização geográfica. | M | T |
| RF-01.3 | Fornecedores e propriedades devem ser exibidos com a localização cadastrada e os dados de conformidade associados (RF-07, RF-08). | M | D |
| RF-06 | O sistema deve importar a geometria das propriedades a partir de arquivos .shp, .geojson e .kml, informando ao usuário os erros de cada arquivo. | M | T |
| RF-07 | As propriedades devem manter atualizados a situação do CAR e demais dados cadastrais relevantes (ex.: área, cultura, data da última atualização), com alerta quando houver mudança. | M | T |
| RF-10 | O sistema deve processar relações de parentesco e grupos econômicos (vários CNPJs/CPFs ligados ao mesmo produtor) para unificar o risco reputacional do grupo. | S | T |

### Lotes e rastreabilidade

| ID | Requisito | Prior. | Verif. |
|---|---|---|---|
| RF-02.1 | O sistema deve permitir cadastrar lotes com tonelagem, critérios de qualidade e critérios de conformidade ESG. | M | T |
| RF-02.2 | O sistema deve modelar o fluxo de lotes por balanço de massa e Grafo Acíclico Direcionado (DAG), relacionando fornecedores, lotes, misturas e transformações. | M | T |
| RF-02.3 | O sistema deve gerar para cada lote um passaporte digital que registre o caminho do lote desde as propriedades de origem até o destino final. | M | D |
| RF-02.4 | O sistema deve permitir dar baixa no lote ao chegar ao destino final. | M | T |
| RF-03 | O sistema deve validar a base de fornecedores e lotes, avaliando por onde os lotes passaram e as misturas ocorridas, segundo critérios de conformidade definidos pelo usuário. | M | T |
| RF-12 | O sistema deve atualizar as informações de lotes a partir de API de ERP ou sistemas de telemetria da operação, com o mínimo de entrada manual. | S | D |

### Conformidade, risco e declarações

| ID | Requisito | Prior. | Verif. |
|---|---|---|---|
| RF-08 | O sistema deve consultar automaticamente, via API ou base baixada, as listas de embargos do Ibama e do ICMBio e a lista suja do trabalho análogo à escravidão, e enriquecer fornecedores e propriedades com os resultados. | M | T |
| RF-11 | O sistema deve calcular o Score de Risco ESG para cada nível (Região, Área, Produtor, Propriedade e Lote), com base em pesos parametrizáveis. | M | T |
| RF-09 | O sistema deve emitir automaticamente a Declaração de Devida Diligência (DDS), enviando-a ao TRACES da União Europeia quando possível, ou ao sistema do comprador; e deve guardar cópia local ancorada criptograficamente em blockchain. | M | D |

### Visualização

| ID | Requisito | Prior. | Verif. |
|---|---|---|---|
| RF-04 | O usuário deve poder ver imagens de satélite sobre fornecedores e propriedades, obtidas de diferentes satélites via API, incluindo alertas de desmatamento, para validar a análise de conformidade. | S | D |
| RF-05 | Fornecedores, propriedades e lotes em trânsito devem aparecer em mapa interativo, com filtro e busca por localização. | M | D |


## Requisitos Não Funcionais

| ID | Requisito | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RNF-01 | As integrações com ERP e telemetria devem ser idempotentes e reprocessáveis, de modo que uma falha ou mensagem repetida não duplique nem perca dados de lotes. | RF-12 | M | T |

Os demais requisitos não funcionais estão em [requisitos-complementares.md](requisitos-complementares.md).


## Regras de Negócio

| ID | Regra | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RN-01 | Uma mesma quantidade de grãos não pode ser contabilizada mais de uma vez: a soma do que sai de um lote não pode ultrapassar seu saldo, impedindo recebimento duplo de crédito ou similares. | RF-02.2 | M | T |
| RN-02 | Áreas inconformes não podem repassar lotes para áreas conformes; um lote cuja origem inclua propriedade inconforme mantém essa condição ao longo do grafo, a menos que haja segregação comprovada por balanço de massa. | RF-02.2, RF-03 | M | T |

Regras de negócio adicionais estão em [requisitos-complementares.md](requisitos-complementares.md).
