# Requisitos Essenciais

Seleção dos requisitos mais relevantes de [requisitos.md](requisitos.md), no formato de `modelo-requisitos.docx`: 23 requisitos funcionais, 12 não funcionais e 13 regras de negócio. Os IDs foram renumerados; a coluna "Depende de" usa os IDs deste documento.

## 1. Requisitos Funcionais

| ID | Descrição | Prioridade | Depende de |
|---|---|---|---|
| RF01 | O sistema deve permitir cadastrar fornecedores com nome, CPF/CNPJ e contato. | Alta | — |
| RF02 | O sistema deve permitir cadastrar, para cada fornecedor, uma ou mais propriedades com nome, município/UF, área em hectares, cultura e localização geográfica (ponto ou polígono). | Alta | RF01 |
| RF03 | O sistema deve importar a geometria de propriedades a partir de arquivos .shp, .geojson e .kml e informar, para cada arquivo, os erros encontrados (feição e motivo). | Alta | RF02 |
| RF04 | O sistema deve fornecer os critérios do sistema Embargo ambiental (propriedade, bloqueante, eliminatório), Trabalho análogo à escravidão (fornecedor, bloqueante, eliminatório) e Situação do CAR (propriedade). | Alta | — |
| RF05 | O sistema deve registrar, para cada propriedade e fornecedor, o status de cada critério aplicável, com data de avaliação, fonte e versão do critério, e exibi-lo nas telas de detalhe. | Alta | RF04 |
| RF06 | O sistema deve consultar a situação do CAR de cada propriedade na base do SICAR ao menos uma vez por dia, registrar o status do critério Situação do CAR e alertar o usuário na interface quando a situação mudar. | Alta | RF02, RF05 |
| RF07 | O sistema deve consultar as listas de embargos do Ibama e do ICMBio ao menos uma vez por dia. | Alta | — |
| RF08 | O sistema deve consultar a lista suja do trabalho análogo à escravidão ao menos uma vez por dia. | Alta | — |
| RF09 | O sistema deve cruzar o resultado das consultas das listas oficiais com os CPF/CNPJ cadastrados e registrar o status dos critérios Embargo ambiental e Trabalho análogo à escravidão (inconforme se encontrado, conforme se a consulta teve sucesso e não encontrou, não definido se ainda não houve consulta). | Alta | RF01, RF05, RF07, RF08 |
| RF10 | O sistema deve exibir fornecedores, propriedades e lotes em trânsito em mapa interativo, com filtro por status de um critério selecionado, cultura e UF e busca por município ou coordenadas. | Alta | RF02 |
| RF11 | O sistema deve exibir imagens de satélite de propriedades, obtidas via API de ao menos duas fontes selecionáveis pelo usuário, com os alertas de desmatamento sobrepostos. | Alta | RF02 |
| RF12 | O sistema deve permitir cadastrar lotes com identificação, tonelagem, cultura, data e propriedade(s) de origem. | Alta | RF02 |
| RF13 | O sistema deve registrar o fluxo de lotes como Grafo Acíclico Direcionado (DAG), ligando fornecedores, lotes, misturas e transformações. | Alta | RF12 |
| RF14 | O sistema deve manter o balanço de massa de cada lote, calculando seu saldo disponível a cada entrada, saída ou baixa. | Alta | RF13 |
| RF15 | O sistema deve gerar para cada lote um passaporte digital, exibível na tela e exportável em PDF e JSON, que liste em ordem as etapas do lote desde as propriedades de origem até o destino final. | Alta | RF13 |
| RF16 | O sistema deve calcular e exibir, para cada lote e cada critério, a distribuição da massa entre conforme, inconforme e não definido, percorrendo o grafo de origem por balanço de massa, e recalculá-la quando o status de uma origem mudar. | Alta | RF05, RF14 |
| RF17 | O sistema deve permitir ao usuário definir o perfil de validação: para cada critério do catálogo, ativá-lo e informar a tolerância máxima de massa inconforme e não definida (padrão 0%). | Alta | RF04 |
| RF18 | O sistema deve avaliar cada lote comparando a distribuição de massa de cada critério com o perfil de validação e registrar o resultado "validado" ou "não validado" com os critérios que falharam. | Alta | RF16, RF17 |
| RF19 | O sistema deve calcular o Score de Risco ESG (0 a 100) para Região, Área, Produtor, Propriedade e Lote, com pesos dos critérios configuráveis pelo usuário. | Alta | RF05 |
| RF20 | O sistema deve emitir automaticamente a Declaração de Devida Diligência (DDS) quando um lote validado for marcado como expedido ao comprador e transmiti-la ao sistema do comprador, registrando o protocolo de recebimento. | Alta | RF18 |
| RF21 | O sistema deve criar e atualizar lotes (tonelagem, data e origem) a partir de API de ERP ou de sistemas de telemetria, sem intervenção manual, com ao menos uma sincronização por hora. | Alta | RF12 |
| RF22 | O sistema deve permitir a um Analista de Conformidade ou superior criar critérios do usuário, escolhendo o tipo de avaliação, o nível de aplicação, a fonte (inclusive lista própria em CSV ou GeoJSON), os parâmetros e as indicações de bloqueante e eliminatório. | Alta | RF04 |
| RF23 | O sistema deve permitir a um Analista de Conformidade ou superior editar, desativar e arquivar critérios do usuário. | Alta | RF22 |

## 2. Requisitos Não Funcionais

| ID | Descrição | Categoria | Prioridade | Depende de |
|---|---|---|---|---|
| RNF01 | O acesso deve exigir autenticação, e as senhas devem ser armazenadas apenas como hash com salt (bcrypt ou Argon2). | Segurança | Alta | — |
| RNF02 | O acesso às funcionalidades deve ser controlado por perfil (RBAC), com ao menos Administrador, Analista de Conformidade, Operador de Lotes e Auditor (somente leitura). | Segurança | Alta | RNF01 |
| RNF03 | Toda comunicação deve usar TLS 1.2 ou superior. | Segurança | Alta | — |
| RNF04 | Os registros da trilha de auditoria (usuário, data/hora, ação, valor anterior e novo) de fornecedores, propriedades, lotes, critérios, pesos de score e declarações não podem ser editados nem excluídos por nenhum perfil, inclusive Administrador. | Segurança | Alta | RNF02 |
| RNF05 | A trilha de auditoria deve ser retida somente pelo período necessário à sua finalidade; mediante solicitação do titular ou fim da finalidade, os dados pessoais devem ser anonimizados, preservando o registro da ação, e a anonimização deve ser registrada. | Privacidade (LGPD) | Alta | RNF04 |
| RNF06 | O sistema deve permitir atender solicitações de titulares de dados pessoais (acesso, correção, eliminação ou anonimização) em até 15 dias. | Privacidade (LGPD) | Alta | RF01 |
| RNF07 | A visão inicial do mapa deve carregar em até 3 s (percentil 95) com 10.000 propriedades na base. | Desempenho | Média | RF10 |
| RNF08 | A avaliação de um lote deve concluir em até 10 s (percentil 95) para lotes com até 5 níveis de mistura. | Desempenho | Média | RF18 |
| RNF09 | Se uma API externa (satélite, Ibama, ICMBio, lista suja, SICAR) estiver indisponível, o sistema deve usar a última resposta obtida com sucesso e exibir a data dela. | Confiabilidade | Alta | RF06, RF07, RF08, RF11 |
| RNF10 | O sistema deve ter disponibilidade mensal de 99,5%, excluídas janelas de manutenção comunicadas com antecedência. | Disponibilidade | Baixa | — |
| RNF11 | A interface deve ser web e responsiva, utilizável de 360 px de largura até telas de desktop, em português do Brasil. | Usabilidade | Alta | — |
| RNF12 | As regras de negócio devem ter cobertura de testes automatizados de ao menos 70%, executados em integração contínua a cada alteração. | Manutenibilidade | Média | — |

## 3. Regras de Negócio

| ID | Descrição | Prioridade | Depende de |
|---|---|---|---|
| RN01 | A soma das quantidades que saem de um lote (transferências, misturas e baixa) não pode ultrapassar seu saldo disponível, evitando que a mesma quantidade seja contabilizada duas vezes. | Alta | RF14 |
| RN02 | O status de cada critério de um lote é calculado apenas por balanço de massa: toda massa de saída de uma mistura ou transformação mantém a distribuição proporcional de status das massas de entrada. Massa inconforme ou não definida não pode se tornar conforme por mistura ou repasse. | Alta | RF16 |
| RN03 | A massa com status não definido nunca é tratada como conforme: permanece como parcela separada na distribuição do lote. Status não aplicável não entra na distribuição. | Alta | RF16 |
| RN04 | Cada critério é avaliado no seu nível de aplicação (propriedade ou fornecedor), de modo que a mesma massa não seja contada duas vezes no mesmo critério. | Alta | RF16 |
| RN05 | Um status de critério nunca é sobrescrito: cada nova avaliação gera um novo registro com data e versão do critério, preservando os anteriores. | Alta | RF05 |
| RN06 | Propriedade ou fornecedor com status inconforme em critério marcado como bloqueante não pode ter novos lotes de entrada vinculados até a regularização comprovada. | Alta | RF09 |
| RN07 | A alteração manual do status de um critério só pode ser feita por Analista de Conformidade ou superior, com justificativa e documento anexo, e é registrada em auditoria. | Alta | RF05, RNF04 |
| RN08 | Status inconforme em critério marcado como eliminatório, ou qualquer massa inconforme nele em um lote, resulta em Score de Risco ESG 100, independentemente dos pesos. | Alta | RF19 |
| RN09 | A DDS só pode ser emitida para lote validado segundo o perfil de validação vigente, contendo a geolocalização de todas as propriedades de origem. | Alta | RF20 |
| RN10 | DDS emitida não pode ser editada nem excluída e preserva o status de conformidade do lote vigente na emissão; correções são feitas por retificação ou cancelamento, vinculados à declaração original. | Alta | RF20 |
| RN11 | Critérios do sistema não podem ser editados, desativados nem excluídos por usuários; só a equipe técnica os altera, por atualização do sistema. | Alta | RF04 |
| RN12 | Critérios do usuário só podem ser criados e editados por Analista de Conformidade ou superior, e cada criação ou edição é registrada em auditoria. | Alta | RF22, RNF04 |
| RN13 | Toda edição de critério gera uma nova versão e preserva as anteriores; critério com avaliações só pode ser desativado ou arquivado. Após a edição, as entidades afetadas são reavaliadas pela nova versão, sem alterar as avaliações anteriores. | Alta | RF23, RN05 |

## Glossário

| Termo | Definição |
|---|---|
| Critério de conformidade | Verificação do catálogo, composta por nome, nível de aplicação (propriedade ou fornecedor), tipo de avaliação com seus parâmetros, fonte de dados, indicação de bloqueante e eliminatório, e versão. Pode ser **do sistema** (protegido) ou **do usuário** (RF22). Cada critério é avaliado de forma independente dos demais. |
| Tipo de avaliação | Modelo de regra do sistema, parametrizável sem programação: consulta em lista por chave, mapa de valores de um campo, limiar numérico sobre um campo cadastral e sobreposição geométrica com uma camada. |
| Status de conformidade (propriedade ou fornecedor) | Resultado da avaliação de **um** critério sobre a entidade: *conforme*, *inconforme*, *não definido* (sem avaliação ou sem dado) ou *não aplicável*. Cada status guarda a data de avaliação. Não existe status único da entidade, apenas um status por critério. |
| Status de conformidade (lote) | Para cada critério, a distribuição da massa do lote entre conforme, inconforme e não definido, calculada por balanço de massa a partir do status das propriedades e fornecedores de origem, cada critério considerado no seu nível de aplicação. É recalculado quando o status de uma origem muda. |
| Perfil de validação | Conjunto de regras definido pelo usuário (RF17); cada regra indica um critério e a tolerância máxima de massa inconforme e não definida. Não existe agregação implícita entre critérios. |
| Lote validado | Lote que atende todas as regras do perfil de validação vigente na data da avaliação (RF18). |
| Score de Risco ESG | Valor de 0 a 100, em que 0 é o menor risco e 100 o maior. |
