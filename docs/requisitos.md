# Requisitos

Documento único de requisitos funcionais, não funcionais e regras de negócio. Cada item descreve uma única exigência verificável. Os IDs são únicos; subitens (ex.: RF-04.1) derivam da divisão de um requisito composto. RNF-17 e RN-23 não são utilizados (retirados do escopo).

## Convenções

- **Origem:** requisito ou regra que o item complementa; `—` quando é independente. Referência a `RF-02` vale para todos os subitens RF-02.x.
- **Prioridade (MoSCoW):** **M** = deve, **S** = deveria, **C** = poderia.
- Valores numéricos são metas iniciais, a validar com o professor/usuário.

## Glossário

| Termo | Definição |
|---|---|
| Produtor / fornecedor | Pessoa física ou jurídica (CPF/CNPJ) que fornece grãos. No sistema, "fornecedor" é o cadastro do produtor. |
| Propriedade | Imóvel rural de um fornecedor, com localização e geometria. |
| Região | Divisão administrativa (município ou UF) derivada da localização da propriedade. |
| Área | Agrupamento de propriedades definido pelo usuário (RF-01.5). |
| Critério de conformidade | Verificação do catálogo, composta por nome, nível de aplicação (propriedade ou fornecedor), tipo de avaliação com seus parâmetros, fonte de dados, indicação de se é bloqueante e se é eliminatório, e versão. Pode ser **do sistema** (protegido, só a equipe técnica altera) ou **do usuário** (criado e editado por Analista de Conformidade ou superior). Cada critério é avaliado de forma independente dos demais. |
| Tipo de avaliação | Modelo de regra oferecido pelo sistema, parametrizável sem programação: consulta em lista por chave (CPF/CNPJ ou geometria), mapa de valores de um campo (ex.: situação do CAR), limiar numérico sobre um campo cadastral e sobreposição geométrica com uma camada. Um critério é uma instância de um tipo, com seus parâmetros. |
| Status de conformidade (propriedade ou fornecedor) | Resultado da avaliação de **um** critério sobre a entidade: *conforme*, *inconforme*, *não definido* (sem avaliação ou sem dado) ou *não aplicável* (o critério não se aplica à entidade). Cada status guarda a data de avaliação. Não existe status único da entidade, apenas um status por critério. |
| Status de conformidade (lote) | Para cada critério, a distribuição da massa do lote entre conforme, inconforme e não definido, calculada por balanço de massa a partir do status das propriedades e fornecedores de origem, cada critério considerado no seu nível de aplicação. É recalculado quando o status de uma origem muda e pode ser consultado para uma data passada. |
| Perfil de validação | Conjunto de regras definido pelo usuário (RF-03.1); cada regra indica um critério do catálogo e a tolerância máxima de massa inconforme e não definida (padrão: 0%). Não existe agregação implícita entre critérios: a decisão sim/não sempre se refere ao perfil. |
| Lote validado | Lote que atende todas as regras do perfil de validação vigente na data da avaliação (RF-03.2) e não sofreu alteração de origem desde então. |
| Score de Risco ESG | Valor de 0 a 100, em que 0 é o menor risco e 100 o maior. |

## Requisitos Funcionais

### Fornecedores e propriedades

| ID | Requisito | Origem | Prior. |
|---|---|---|---|
| RF-01.1 | O sistema deve permitir cadastrar fornecedores com nome, CPF/CNPJ e contato. | — | M |
| RF-01.2 | O sistema deve permitir cadastrar, para cada fornecedor, uma ou mais propriedades com nome, município/UF, área em hectares, cultura e localização geográfica (ponto ou polígono). | RF-01.1 | M |
| RF-01.3 | O sistema deve exibir a localização cadastrada nas telas de detalhe de fornecedor e de propriedade. | RF-01.2 | M |
| RF-01.4 | O sistema deve exibir, nas telas de detalhe de fornecedor e de propriedade, o status de cada critério aplicável (conforme, inconforme, não definido ou não aplicável), com a data de avaliação e a fonte. | RF-13.2 | M |
| RF-01.5 | O sistema deve permitir ao usuário criar Áreas e associar propriedades a elas. | RF-01.2 | M |
| RF-06.1 | O sistema deve importar a geometria de propriedades a partir de arquivos .shp, .geojson e .kml. | RF-01.2 | M |
| RF-06.2 | O sistema deve informar ao usuário, para cada arquivo importado, os erros encontrados (feição e motivo). | RF-06.1 | M |
| RF-07.1 | O sistema deve consultar a situação do CAR de cada propriedade na base do SICAR ao menos uma vez por dia e registrar o status do critério Situação do CAR (RN-14). | RF-01.2, RF-13.2 | M |
| RF-07.2 | O sistema deve alertar o usuário (notificação na interface) quando a situação do CAR de uma propriedade mudar. | RF-07.1 | M |
| RF-10.1 | O sistema deve permitir registrar vínculos de grupo econômico e de parentesco entre fornecedores (CPF/CNPJ). | RF-01.1 | S |
| RF-10.2 | O sistema deve estender o status de cada critério entre os integrantes de um grupo econômico, conforme RN-11. | RF-10.1, RF-13.2 | S |

### Lotes e rastreabilidade

| ID | Requisito | Origem | Prior. |
|---|---|---|---|
| RF-02.1 | O sistema deve permitir cadastrar lotes com identificação, tonelagem, cultura, data e propriedade(s) de origem, além de parâmetros de qualidade configuráveis pelo usuário. | RF-01.2 | M |
| RF-02.2 | O sistema deve registrar o fluxo de lotes como Grafo Acíclico Direcionado (DAG), ligando fornecedores, lotes, misturas e transformações. | RF-02.1 | M |
| RF-02.3 | O sistema deve manter o balanço de massa de cada lote, calculando seu saldo disponível a cada entrada, saída ou baixa. | RF-02.2 | M |
| RF-02.4 | O sistema deve permitir dar baixa em um lote ao chegar ao destino final. | RF-02.3 | M |
| RF-02.5 | O sistema deve gerar para cada lote um passaporte digital, exibível na tela e exportável em PDF e JSON, que liste em ordem as etapas do lote desde as propriedades de origem até o destino final. | RF-02.2 | M |
| RF-03.1 | O sistema deve permitir ao usuário definir o perfil de validação: para cada critério do catálogo, ativá-lo e informar a tolerância máxima de massa inconforme e não definida (padrão 0%). | RF-13.1 | M |
| RF-03.2 | O sistema deve avaliar cada lote comparando a distribuição de massa de cada critério (RF-13.3) com o perfil de validação, e registrar o resultado "validado" ou "não validado" com os critérios que falharam. | RF-03.1, RF-13.3 | M |
| RF-12 | O sistema deve criar e atualizar lotes (tonelagem, data e origem) a partir de API de ERP ou de sistemas de telemetria, sem intervenção manual, com ao menos uma sincronização por hora. | RF-02.1 | S |

### Conformidade, risco e declarações

| ID | Requisito | Origem | Prior. |
|---|---|---|---|
| RF-08.1 | O sistema deve consultar as listas de embargos do Ibama e do ICMBio ao menos uma vez por dia. | — | M |
| RF-08.2 | O sistema deve consultar a lista suja do trabalho análogo à escravidão ao menos uma vez por dia. | — | M |
| RF-08.3 | O sistema deve cruzar o resultado das consultas de RF-08.1 e RF-08.2 com os CPF/CNPJ cadastrados e registrar o status dos critérios Embargo ambiental e Trabalho análogo à escravidão: inconforme se encontrado; conforme se a consulta foi bem-sucedida e não encontrou; não definido se ainda não houve consulta. | RF-08.1, RF-08.2, RF-13.2 | M |
| RF-13.1 | O sistema deve fornecer os critérios do sistema Embargo ambiental (propriedade, bloqueante, eliminatório), Trabalho análogo à escravidão (fornecedor, bloqueante, eliminatório) e Situação do CAR (propriedade). | — | M |
| RF-13.2 | O sistema deve registrar, para cada propriedade e fornecedor, o status de cada critério aplicável, com data de avaliação, fonte e versão do critério. | RF-13.1 | M |
| RF-13.3 | O sistema deve calcular, para cada lote e cada critério, a distribuição da massa entre conforme, inconforme e não definido, percorrendo o grafo de origem por balanço de massa. | RF-02.3, RF-13.2 | M |
| RF-13.4 | O sistema deve recalcular o status dos lotes afetados quando o status de um critério de uma origem mudar. | RF-13.3 | M |
| RF-13.5 | O sistema deve permitir consultar o status de propriedades, fornecedores e lotes em uma data passada. | RN-24 | S |
| RF-13.6 | O sistema deve exibir, na tela de detalhe do lote, a distribuição de massa de cada critério. | RF-13.3 | M |
| RF-13.7 | O sistema deve permitir a um Analista de Conformidade ou superior criar critérios do usuário, escolhendo o tipo de avaliação, o nível de aplicação, a fonte, os parâmetros e as indicações de bloqueante e eliminatório. | RF-13.1, RNF-12 | M |
| RF-13.8 | O sistema deve permitir a um Analista de Conformidade ou superior editar, desativar e arquivar critérios do usuário. | RF-13.7 | M |
| RF-13.9 | O sistema deve permitir simular um critério do usuário sobre as entidades existentes, exibindo o resultado, antes de publicá-lo. | RF-13.7 | S |
| RF-11.1 | O sistema deve calcular o Score de Risco ESG (0 a 100) para cada nível: Região, Área, Produtor, Propriedade e Lote. | RF-01.5 | C |
| RF-11.2 | O sistema deve permitir ao usuário configurar os pesos dos critérios do score. | RF-11.1 | C |
| RF-09.1 | O sistema deve emitir automaticamente a Declaração de Devida Diligência (DDS) quando um lote validado for marcado como expedido ao comprador. | RF-03.2 | M |
| RF-09.2 | O sistema deve transmitir a DDS emitida ao sistema do comprador por integração configurável e registrar o protocolo de recebimento retornado. | RF-09.1 | M |

### Visualização

| ID | Requisito | Origem | Prior. |
|---|---|---|---|
| RF-04.1 | O sistema deve exibir imagens de satélite de propriedades, obtidas via API de ao menos duas fontes de satélite selecionáveis pelo usuário. | RF-01.2 | M |
| RF-04.2 | O sistema deve exibir sobre a imagem da propriedade os alertas de desmatamento obtidos via API. | RF-04.1 | M |
| RF-05.1 | O sistema deve exibir fornecedores, propriedades e lotes em trânsito em mapa interativo. | RF-01.2 | S |
| RF-05.2 | O mapa deve permitir filtrar por status de um critério selecionado, cultura e UF. | RF-05.1 | S |
| RF-05.3 | O mapa deve permitir buscar por nome de município ou por coordenadas. | RF-05.1 | S |

## Requisitos Não Funcionais

### Integrações

| ID | Requisito | Origem | Prior. |
|---|---|---|---|
| RNF-01.1 | A integração com ERP e telemetria deve ser idempotente: a mesma mensagem recebida mais de uma vez não pode duplicar dados de lotes. | RF-12 | M |
| RNF-01.2 | Uma integração que falhar deve poder ser reprocessada sem perda de dados de lotes. | RF-12 | M |

### Desempenho (ISO 25010: eficiência de desempenho)

| ID | Requisito | Origem | Prior. |
|---|---|---|---|
| RNF-02.1 | A visão inicial do mapa deve carregar em até 3 s (percentil 95) com 10.000 propriedades na base. | RF-05.1 | S |
| RNF-02.2 | Filtros e buscas do mapa devem responder em até 2 s (percentil 95) com 10.000 propriedades na base. | RF-05.2, RF-05.3 | S |
| RNF-03 | A avaliação de RF-03.2 de um lote deve concluir em até 10 s (percentil 95) para lotes com até 5 níveis de mistura. | RF-03.2 | S |
| RNF-04 | O recálculo do Score de Risco ESG após mudança de pesos deve ser assíncrono e concluir em até 5 min para uma base de 200.000 propriedades. | RF-11.2 | C |

### Escalabilidade

| ID | Requisito | Origem | Prior. |
|---|---|---|---|
| RNF-05 | O sistema deve suportar 50.000 fornecedores, 200.000 propriedades e 5 milhões de lotes, mantendo as metas de RNF-02.1, RNF-02.2 e RNF-03. | — | S |
| RNF-06 | A importação de arquivos de até 100 MB deve ser processada de forma assíncrona, mantendo a interface responsiva e exibindo o andamento. | RF-06.1 | S |

### Disponibilidade e confiabilidade

| ID | Requisito | Origem | Prior. |
|---|---|---|---|
| RNF-07 | O sistema deve ter disponibilidade mensal de 99,5%, excluídas janelas de manutenção comunicadas com antecedência. | — | C |
| RNF-08.1 | O sistema deve executar backup automático diário, retido por 30 dias. | — | S |
| RNF-08.2 | Os backups devem permitir recuperação com RPO de 24 h e RTO de 4 h. | RNF-08.1 | S |
| RNF-08.3 | A restauração de backup deve ser testada ao menos uma vez antes da entrega. | RNF-08.1 | S |
| RNF-09.1 | Se uma API externa (satélite, Ibama, ICMBio, lista suja, SICAR) estiver indisponível, o sistema deve usar a última resposta obtida com sucesso e exibir a data dela. | RF-04.1, RF-07.1, RF-08.1, RF-08.2 | M |
| RNF-09.2 | Após falha de consulta a API externa, o sistema deve repetir a consulta com intervalo crescente (backoff exponencial iniciando em 1 min, dobrando a cada tentativa, até o máximo de 1 h). | RNF-09.1 | M |

### Segurança

| ID | Requisito | Origem | Prior. |
|---|---|---|---|
| RNF-10 | O acesso deve exigir autenticação; senhas devem ser armazenadas apenas como hash com salt (bcrypt ou Argon2). | — | M |
| RNF-11 | O sistema deve oferecer autenticação em dois fatores, ao menos para os perfis Administrador e Analista de Conformidade. | RNF-10 | C |
| RNF-12 | O acesso às funcionalidades deve ser controlado por perfil (RBAC), com ao menos: Administrador, Analista de Conformidade, Operador de Lotes e Auditor (somente leitura). | — | M |
| RNF-13 | Toda comunicação deve usar TLS 1.2 ou superior. | — | M |
| RNF-14 | CPF, CNPJ e coordenadas de propriedades devem ser criptografados em repouso (AES-256 ou equivalente). | RNF-16.2 | M |
| RNF-15.1 | O sistema deve registrar em trilha de auditoria (usuário, data/hora, ação, valor anterior e novo) a criação, alteração e exclusão de fornecedores, propriedades, lotes, critérios, pesos de score e declarações. | RN-01, RN-02 | M |
| RNF-15.2 | A trilha de auditoria deve ser append-only: nenhum perfil, inclusive Administrador, pode editar ou excluir registros. | RNF-15.1 | M |
| RNF-15.3 | A trilha deve ser retida somente pelo período necessário à sua finalidade, definido em política de retenção. Mediante solicitação do titular ou fim da finalidade, os dados pessoais da trilha devem ser anonimizados, preservando o registro da ação, e a própria anonimização deve ser registrada. | RNF-15.2, RNF-16.3 | M |

### Conformidade legal (LGPD)

| ID | Requisito | Origem | Prior. |
|---|---|---|---|
| RNF-16.1 | Cada categoria de dado pessoal tratada (CPF, parentesco, localização) deve ter finalidade e base legal declaradas em registro de tratamento. | RF-01.1, RF-10.1 | M |
| RNF-16.2 | O sistema deve coletar apenas os dados pessoais necessários à finalidade declarada (minimização). | RNF-16.1 | M |
| RNF-16.3 | O sistema deve permitir atender solicitações de titulares (acesso, correção, eliminação ou anonimização) em até 15 dias. | RNF-16.1 | M |

### Integridade e rastreabilidade

| ID | Requisito | Origem | Prior. |
|---|---|---|---|
| RNF-18 | Registros de lotes e DDS devem ter hash armazenado, de modo que qualquer alteração posterior seja detectável. Alterações por anonimização (RNF-15.3) geram novo hash, registrado na trilha. | RF-02.1, RF-09.1 | M |
| RNF-19 | Todo dado importado de fonte externa deve guardar fonte, data de obtenção e versão, para reproduzir a análise feita em qualquer data passada. | RF-06.1, RF-08.1 | M |

### Interoperabilidade

| ID | Requisito | Origem | Prior. |
|---|---|---|---|
| RNF-20 | O sistema deve expor API REST documentada em OpenAPI. | RF-12 | S |
| RNF-21 | Importação e exportação devem usar formatos abertos (CSV, GeoJSON, JSON). | — | S |
| RNF-22.1 | Geometrias devem ser armazenadas em WGS 84 (EPSG:4326), por compatibilidade com GeoJSON e APIs de satélite. | RF-01.2 | M |
| RNF-22.2 | Geometrias importadas em outro sistema de referência (ex.: SIRGAS 2000) devem ser convertidas para EPSG:4326 na importação. | RF-06.1, RNF-22.1 | M |

### Usabilidade e portabilidade

| ID | Requisito | Origem | Prior. |
|---|---|---|---|
| RNF-23.1 | A interface deve ser web e responsiva, utilizável de 360 px de largura até telas de desktop. | — | M |
| RNF-23.2 | A interface deve estar em português do Brasil. | — | M |
| RNF-23.3 | A interface deve funcionar nas duas últimas versões estáveis de Chrome, Firefox e Edge | — | M |
| RNF-24 | A interface deve atender ao nível AA das WCAG 2.1. | RNF-23.1 | C |


### Manutenibilidade e observabilidade

| ID | Requisito | Origem | Prior. |
|---|---|---|---|
| RNF-29 | Os critérios do sistema devem ser definidos em arquivos versionados no repositório, validados por schema e sincronizados ao banco na inicialização, que deve falhar se algum arquivo for inválido. | RF-13.1 | M |
| RNF-30 | Cada tipo de avaliação deve ter testes automatizados com casos de entrada e status esperado. | RF-13.7 | S |
| RNF-26 | As regras de negócio devem ter cobertura de testes automatizados de ao menos 70%, executados em integração contínua a cada alteração. | — | S |
| RNF-27.1 | O sistema deve gerar logs estruturados em JSON. | — | S |
| RNF-27.2 | O sistema deve expor métricas de saúde de consultas externas, filas e erros. | — | S |
| RNF-28 | O sistema deve alertar o administrador quando uma fonte de dados de conformidade (listas de embargo, lista suja, SICAR) ficar mais de 24 h sem consulta bem-sucedida. | RF-07.1, RF-08.1, RF-08.2 | S |

## Regras de Negócio

### Lotes e balanço de massa

| ID | Regra | Origem | Prior. |
|---|---|---|---|
| RN-01 | A soma das quantidades que saem de um lote (transferências, misturas e baixa) não pode ultrapassar seu saldo disponível, evitando que a mesma quantidade seja contabilizada duas vezes. | RF-02.3 | M |
| RN-03 | Em toda mistura ou transformação, a massa de saída não pode exceder a massa de entrada multiplicada pelo rendimento declarado do processo; o rendimento deve estar dentro de limites configuráveis por tipo de processo. | RF-02.3 | M |
| RN-04 | O grafo de lotes deve ser acíclico: um lote não pode ser origem de si mesmo, direta ou indiretamente. | RF-02.2 | M |
| RN-05 | Cada quantidade de um lote só pode receber baixa no destino final uma vez. | RF-02.4, RN-01 | M |
| RN-06 | Todo lote deve ser rastreável até ao menos uma propriedade; lote sem origem identificada fica bloqueado para saída até a regularização. | RF-02.1 | M |
| RN-07 | Alteração de tonelagem, data ou origem de um lote após a emissão de DDS deve gerar nova versão do lote, preservando a anterior. | RF-02.1, RF-09.1 | M |

### Conformidade

| ID | Regra | Origem | Prior. |
|---|---|---|---|
| RN-02 | O status de cada critério de um lote é calculado apenas por balanço de massa: toda massa de saída de uma mistura ou transformação mantém a distribuição proporcional de status das massas de entrada. Massa inconforme ou não definida não pode se tornar conforme por mistura ou repasse. | RF-02.3, RF-13.3 | M |
| RN-09 | Propriedade ou fornecedor com status inconforme em critério marcado como bloqueante não pode ter novos lotes de entrada vinculados até a regularização comprovada. | RF-01.4, RF-08.3 | M |
| RN-10 | Em até 1 hora após a mudança do status de um critério de uma propriedade ou fornecedor, o status dos lotes afetados, em estoque ou em trânsito, deve ser recalculado e suas validações reavaliadas. | RF-13.4 | M |
| RN-11 | O status de cada critério de um integrante de grupo econômico estende-se aos demais integrantes, critério a critério, prevalecendo o pior status (inconforme, depois não definido, depois conforme). A extensão não cria critério novo. | RF-10.1 | M |
| RN-12 | A alteração manual do status de um critério só pode ser feita por Analista de Conformidade ou superior, com justificativa e documento anexo, e é registrada em auditoria. | RF-01.4, RNF-15.1 | M |
| RN-13 | Geometrias de propriedades devem ser válidas; sobreposição com propriedade de outro fornecedor acima de 5% da área da menor das duas (limite configurável) gera alerta de conflito para análise. | RF-01.2, RF-06.1 | S |
| RN-14 | No critério Situação do CAR, o status é inconforme se o CAR estiver cancelado, suspenso ou pendente; conforme se estiver ativo; não definido se ainda não houve consulta. | RF-07.1 | M |

### Dupla contagem e lavagem de grãos

| ID | Regra | Origem | Prior. |
|---|---|---|---|
| RN-15 | O sistema deve alertar quando o volume recebido de uma propriedade no ano exceder a produtividade máxima por hectare da cultura (tabela configurável pelo usuário) multiplicada pela área da propriedade. | RN-02 | S |
| RN-16 | Uma mesma nota fiscal ou documento de transporte não pode ser vinculado a mais de um lote de entrada. | RN-01 | M |
| RN-17 | Toda transferência de lote entre fornecedores deve ter documento fiscal anexo e não pode alterar a propriedade de origem registrada no lote. | RN-02 | M |

### Score de risco

| ID | Regra | Origem | Prior. |
|---|---|---|---|
| RN-18 | Os pesos do score devem somar 100%; cada alteração gera nova versão da configuração, mantendo o histórico para reproduzir scores já calculados. | RF-11.2 | M |
| RN-19 | O score de um nível superior (Região, Área) é agregado a partir dos níveis inferiores (Produtor, Propriedade, Lote). Status inconforme em critério marcado como eliminatório, ou qualquer massa inconforme nele em um lote, resulta em score 100, independentemente dos pesos. | RF-11.1 | M |
| RN-20 | Alterações no perfil de validação valem para lotes novos e em estoque, sem alterar retroativamente DDS já emitidas. | RF-03.1, RN-22 | M |

### Declaração de devida diligência (DDS)

| ID | Regra | Origem | Prior. |
|---|---|---|---|
| RN-21 | A DDS só pode ser emitida para lote validado segundo o perfil de validação vigente, contendo a geolocalização de todas as propriedades de origem. | RF-09.1 | M |
| RN-22 | DDS emitida não pode ser editada nem excluída e preserva o status de conformidade do lote vigente na emissão; correções são feitas por retificação ou cancelamento, vinculados à declaração original. | RF-09.1 | M |


### Status de conformidade

| ID | Regra | Origem | Prior. |
|---|---|---|---|
| RN-24 | Um status de critério nunca é sobrescrito: cada nova avaliação gera um novo registro com data e versão do critério, preservando os anteriores. | RF-13.2 | M |
| RN-25 | Cada critério é avaliado no seu nível de aplicação (propriedade ou fornecedor), de modo que a mesma massa não seja contada duas vezes no mesmo critério. | RF-13.3 | M |
| RN-26 | A massa com status não definido nunca é tratada como conforme: permanece como parcela separada na distribuição do lote. Status não aplicável não entra na distribuição. | RF-13.3 | M |
| RN-27 | Critérios do sistema não podem ser editados, desativados nem excluídos por usuários; só a equipe técnica os altera, por atualização do sistema. | RF-13.1 | M |
| RN-28 | Critérios do usuário só podem ser criados e editados por Analista de Conformidade ou superior, e cada criação ou edição é registrada em auditoria. | RF-13.7, RNF-15.1 | M |
| RN-29 | Toda edição de critério gera uma nova versão e preserva as anteriores; critério que já tenha avaliações só pode ser desativado ou arquivado, nunca excluído. Após a edição, as entidades afetadas são reavaliadas pela nova versão, sem alterar as avaliações anteriores. | RF-13.8, RN-24 | M |
| RN-30 | Um critério do usuário só pode usar tipos de avaliação e fontes catalogados, com parâmetros validados; critério com parâmetros inválidos não pode ser publicado. | RF-13.7 | M |
