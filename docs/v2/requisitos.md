---
titulo: Documento de Especificação de Requisitos
sistema: Gestão de Cadeia de Suprimentos
versao: '2.0'
data: 19/11/2026
responsavel: Pedro Henrique Carteli Rossetto
estagios:
- Demo
- Piloto
- Pós-piloto
fonte_estruturada: requisitos.yaml
---

# Documento de Especificação de Requisitos

_Análise e Projeto de Sistemas_

| Campo | Valor |
|---|---|
| Projeto / Sistema | Gestão de Cadeia de Suprimentos |
| Cliente / Solicitante | [a definir] |
| Responsável(is) | Pedro Henrique Carteli Rossetto |
| Data | 19/11/2026 |
| Versão | 2.0 |

## 1. Introdução

### 1.1 Sobre este documento

Este documento apresenta e organiza o conjunto completo de requisitos levantados para o sistema de gestão da cadeia de suprimentos, servindo de referência para as etapas de projeto, desenvolvimento, testes e validação.

IDs com ponto (ex.: RF-08.1) são subitens de um requisito dividido para manter cada item atômico; uma referência a RF-02 vale para todos os RF-02.x.

A prioridade indica o estágio de entrega: Demo (apresentação de demonstração ao cliente), Piloto (MVP em operação com o primeiro cliente) e Pós-piloto (evolução após o piloto). Um requisito nunca depende de outro de estágio posterior.

### 1.2 Descrição do Mini-Mundo

O sistema atua na cadeia de suprimentos de grãos: produtores rurais (fornecedores) entregam a produção de suas propriedades a armazéns, tradings e indústrias, que a agrupam em lotes, misturam e transformam antes de entregá-la a compradores.

Compradores e a regulação socioambiental exigem comprovar que os grãos não vêm de propriedades com desmatamento.

Atores: Administrador, Analista de Conformidade, Operador de Lotes e Auditor (somente leitura).

Entidades principais: fornecedor, propriedade, lote, mistura e transformação, critério de conformidade, status de conformidade, perfil de validação e DDS.

### 1.3 Propósito do Sistema

Rastrear os grãos desde as propriedades de origem até o destino final, avaliar a conformidade socioambiental de fornecedores, propriedades e lotes por critério e por balanço de massa e emitir a DDS ao comprador, com trilha de auditoria. Espera-se reduzir o esforço manual de verificação e permitir comprovar a origem de cada lote.

### 1.4 Glossário

- **Produtor / fornecedor** — Pessoa física ou jurídica (CPF/CNPJ) que fornece grãos. No sistema, "fornecedor" é o cadastro do produtor.
- **Propriedade** — Imóvel rural de um fornecedor, com localização e geometria.
- **Região** — Divisão administrativa (município ou UF) derivada da localização da propriedade.
- **Área** — Agrupamento de propriedades definido pelo usuário (RF-01.5).
- **Critério de conformidade** — Verificação do catálogo, composta por nome, nível de aplicação (propriedade ou fornecedor), tipo de avaliação com seus parâmetros, fonte de dados, indicação de se é bloqueante e se é eliminatório, e versão. Pode ser do sistema (protegido, só a equipe técnica altera) ou do usuário (criado e editado por Analista de Conformidade ou superior). Cada critério é avaliado de forma independente dos demais.
- **Tipo de avaliação** — Modelo de regra oferecido pelo sistema, parametrizável sem programação: consulta em lista por chave (CPF/CNPJ ou geometria), mapa de valores de um campo (ex.: situação do CAR), limiar numérico sobre um campo cadastral e sobreposição geométrica com uma camada. Um critério é uma instância de um tipo, com seus parâmetros.
- **Status de conformidade (propriedade ou fornecedor)** — Resultado da avaliação de um critério sobre a entidade: conforme, inconforme, não definido (sem avaliação ou sem dado) ou não aplicável (o critério não se aplica à entidade). Cada status guarda a data de avaliação. Não existe status único da entidade, apenas um status por critério.
- **Status de conformidade (lote)** — Para cada critério, a distribuição da massa do lote entre conforme, inconforme e não definido, calculada por balanço de massa a partir do status das propriedades e fornecedores de origem, cada critério considerado no seu nível de aplicação. É recalculado quando o status de uma origem muda e pode ser consultado para uma data passada.
- **Perfil de validação** — Conjunto de regras definido pelo usuário (RF-06.1); cada regra indica um critério do catálogo e a tolerância máxima de massa inconforme e não definida (padrão: 0%). Não existe agregação implícita entre critérios: a decisão sim/não sempre se refere ao perfil.
- **Lote validado** — Lote que atende todas as regras do perfil de validação vigente na data da avaliação (RF-06.2) e não sofreu alteração de origem desde então.

## 2. Requisitos Funcionais

O que o sistema deve fazer — as funcionalidades que ele deve oferecer.

| ID | Descrição | Estágio | Depende de |
|---|---|---|---|
| RF-01.1 | O sistema deve permitir cadastrar fornecedores com nome, CPF/CNPJ e contato. | Demo | — |
| RF-01.2 | O sistema deve permitir cadastrar, para cada fornecedor, uma ou mais propriedades com nome, município/UF, área em hectares, cultura e localização geográfica (ponto ou polígono). | Demo | RF-01.1 |
| RF-01.3 | O sistema deve exibir a localização cadastrada nas telas de detalhe de fornecedor e de propriedade. | Demo | RF-01.2 |
| RF-01.4 | O sistema deve exibir, nas telas de detalhe de fornecedor e de propriedade, o status de cada critério aplicável (conforme, inconforme, não definido ou não aplicável), com a data de avaliação e a fonte. | Demo | RF-07.1 |
| RF-01.5 | O sistema deve permitir ao usuário criar Áreas e associar propriedades a elas. | Pós-piloto | RF-01.2 |
| RF-02.1 | O sistema deve importar a geometria de propriedades a partir de arquivos .shp, .geojson e .kml. | Piloto | RF-01.2 |
| RF-02.2 | O sistema deve informar ao usuário, para cada arquivo importado, os erros encontrados (feição e motivo). | Piloto | RF-02.1 |
| RF-03.1 | O sistema deve consultar a situação do CAR de cada propriedade na base do SICAR ao menos uma vez por dia e registrar o status do critério Situação do CAR (RN-10). | Demo | RF-01.2, RF-07.1 |
| RF-03.2 | O sistema deve alertar o usuário (notificação na interface) quando a situação do CAR de uma propriedade mudar. | Demo | RF-03.1 |
| RF-04.1 | O sistema deve permitir registrar vínculos de grupo econômico e de parentesco entre fornecedores (CPF/CNPJ). | Pós-piloto | RF-01.1 |
| RF-04.2 | O sistema deve estender o status de cada critério entre os integrantes de um grupo econômico, conforme RN-07. | Pós-piloto | RF-04.1, RF-07.1 |
| RF-05.1 | O sistema deve permitir cadastrar lotes com identificação, tonelagem, cultura, data e propriedade(s) de origem, além de parâmetros de qualidade configuráveis pelo usuário. | Demo | RF-01.2 |
| RF-05.2 | O sistema deve registrar o fluxo de lotes como Grafo Acíclico Direcionado (DAG), ligando fornecedores, lotes, misturas e transformações. | Piloto | RF-05.1 |
| RF-05.3 | O sistema deve manter o balanço de massa de cada lote, calculando seu saldo disponível a cada entrada, saída ou baixa. | Piloto | RF-05.2 |
| RF-05.4 | O sistema deve permitir dar baixa em um lote ao chegar ao destino final. | Piloto | RF-05.3 |
| RF-05.5 | O sistema deve gerar para cada etapa do lote um passaporte digital, exibível na tela e exportável em PDF, JSON ou QR Code, que liste em ordem as etapas do lote desde as propriedades de origem até o destino final. | Piloto | RF-05.2 |
| RF-06.1 | O sistema deve permitir ao usuário definir o perfil de validação: para cada critério do catálogo, ativá-lo e informar a tolerância máxima de massa inconforme e não definida (padrão 0%). | Piloto | — |
| RF-06.2 | O sistema deve avaliar cada lote comparando a distribuição de massa de cada critério (RF-07.2) com o perfil de validação, e registrar o resultado "validado" ou "não validado" com os critérios que falharam. | Piloto | RF-06.1, RF-07.2 |
| RF-07.1 | O sistema deve registrar, para cada propriedade e fornecedor, o status de cada critério aplicável, com data de avaliação, fonte e versão do critério. | Demo | — |
| RF-07.2 | O sistema deve calcular, para cada lote e cada critério, a distribuição da massa entre conforme, inconforme e não definido, percorrendo o grafo de origem por balanço de massa. | Piloto | RF-05.3, RF-07.1 |
| RF-07.3 | O sistema deve recalcular a distribuição de massa e reavaliar a validação dos lotes afetados, em estoque ou em trânsito, quando o status de um critério de uma origem mudar. | Piloto | RF-06.2, RF-07.2 |
| RF-07.4 | O sistema deve permitir consultar o status de propriedades, fornecedores e lotes em uma data passada. | Pós-piloto | RF-07.1, RF-07.2 |
| RF-08.1 | O sistema deve permitir criar critérios do usuário a partir de um tipo de avaliação, preenchendo os atributos do critério (ver Glossário). | Demo | — |
| RF-08.2 | O sistema deve permitir editar, desativar e arquivar critérios do usuário. | Demo | RF-08.1 |
| RF-08.3 | O sistema deve permitir simular um critério do usuário sobre as entidades existentes antes de publicá-lo. | Pós-piloto | RF-08.1 |
| RF-09.1 | O sistema deve exibir imagens de satélite de propriedades, obtidas via API de ao menos duas fontes de satélite selecionáveis pelo usuário. | Demo | RF-01.2 |
| RF-09.2 | O sistema deve exibir sobre a imagem da propriedade os alertas de desmatamento obtidos via API. | Demo | RF-09.1 |
| RF-10.1 | O sistema deve exibir fornecedores, propriedades e lotes em trânsito em mapa interativo. | Demo | RF-01.2 |
| RF-10.2 | O mapa deve permitir filtrar por status de um critério selecionado, cultura e UF. | Piloto | RF-10.1 |
| RF-10.3 | O mapa deve permitir buscar por nome de município ou por coordenadas. | Piloto | RF-10.1 |
| RF-11.1 | O sistema deve exibir um painel (dashboard) de operação com a quantidade e a massa de lotes em estoque e em trânsito, a massa por status de conformidade (conforme, inconforme, não definido) de um critério selecionado e a quantidade de lotes validados e não validados. | Piloto | RF-05.1, RF-06.2, RF-07.2 |
| RF-11.2 | O sistema deve exibir um painel (dashboard) de fornecedores e propriedades com a quantidade por status de um critério selecionado, por cultura e por UF, e os alertas recentes de CAR e de desmatamento. | Demo | RF-01.4, RF-03.2, RF-09.2 |
| RF-11.3 | Os painéis devem permitir filtrar por período, cultura, UF, fornecedor e critério. | Piloto | RF-11.1, RF-11.2 |
| RF-12.1 | O sistema deve permitir anexar documentos (PDF ou imagem) a fornecedores, propriedades, lotes e transferências, informando o tipo do documento e registrando a data e o usuário do envio. | Demo | RF-01.1, RF-01.2, RF-05.1 |
| RF-12.2 | O sistema deve permitir consultar, filtrar por tipo, entidade e período, e baixar os documentos anexados. | Piloto | RF-12.1 |
| RF-12.3 | O sistema deve manter as versões de um documento: a substituição gera uma nova versão e preserva as anteriores. | Pós-piloto | RF-12.1 |
| RF-13.1 | O sistema deve permitir cadastrar compradores com nome, CPF/CNPJ e contato. | Piloto | — |
| RF-13.2 | O sistema deve permitir registrar a venda de um lote a um comprador, com data, tonelagem e documento fiscal. | Piloto | RF-05.4, RF-12.1, RF-13.1 |
| RF-13.3 | O sistema deve exibir, no detalhe de cada fornecedor, o histórico de compras (lotes recebidos) com data, cultura, tonelagem, propriedade de origem e o status de cada critério na data do recebimento. | Demo | RF-05.1, RF-07.1 |
| RF-13.4 | O sistema deve exibir, no detalhe de cada comprador, o histórico de vendas com os lotes entregues, data, tonelagem, resultado da validação e os critérios atendidos. | Piloto | RF-06.2, RF-13.2 |
| RF-13.5 | O sistema deve permitir filtrar os históricos de compras e de vendas por período e cultura e exportá-los em CSV. | Pós-piloto | RF-13.3, RF-13.4 |
| RF-14.1 | O sistema deve permitir ao Administrador cadastrar usuários com nome, e-mail e perfil. | Demo | — |
| RF-14.2 | O sistema deve permitir ao Administrador alterar o perfil de um usuário e desativá-lo, preservando os registros de auditoria associados a ele. | Piloto | RF-14.1 |
| RF-14.3 | O sistema deve oferecer dois perfis: Administrador, com acesso a todas as funcionalidades e à gestão de usuários, e Usuário, com acesso às demais funcionalidades, sem gerenciar usuários. | Demo | RF-14.1 |
| RF-14.4 | O sistema deve exigir login com e-mail e senha para acesso a qualquer funcionalidade e permitir encerrar a sessão. | Demo | RF-14.1 |
| RF-14.5 | O sistema deve permitir ao usuário redefinir a senha por link enviado ao e-mail cadastrado. | Piloto | RF-14.4 |
| RF-14.6 | O sistema deve substituir o perfil Usuário por perfis específicos: Auditor apenas consulta; Operador de Lotes cadastra fornecedores, propriedades, lotes e documentos; Analista de Conformidade também gerencia critérios do usuário, perfil de validação e alterações manuais de status. | Piloto | RF-14.3 |

## 3. Requisitos Não Funcionais

Restrições e condições de qualidade sobre os requisitos funcionais — desempenho, segurança, usabilidade, confiabilidade, interface, entre outras.

| ID | Descrição | Categoria | Estágio | Depende de |
|---|---|---|---|---|
| RNF-01 | A visão inicial do mapa deve carregar em até 3 s (percentil 95) com 10.000 propriedades na base. | Desempenho | Piloto | RF-10.1 |
| RNF-02 | Filtros e buscas do mapa devem responder em até 2 s (percentil 95) com 10.000 propriedades na base. | Desempenho | Piloto | RF-10.2, RF-10.3 |
| RNF-03 | A avaliação de RF-06.2 de um lote deve concluir em até 10 s (percentil 95) para lotes com até 5 níveis de mistura. | Desempenho | Piloto | RF-06.2 |
| RNF-04 | O sistema deve suportar 50.000 fornecedores, 200.000 propriedades e 5 milhões de lotes, mantendo as metas de RNF-01, RNF-02 e RNF-03. | Escalabilidade | Pós-piloto | — |
| RNF-05 | O sistema deve ter disponibilidade mensal de 99,5%, excluídas janelas de manutenção comunicadas com antecedência. | Confiabilidade | Pós-piloto | — |
| RNF-06 | O sistema deve executar backup automático diário, retido por 30 dias. | Confiabilidade | Piloto | — |
| RNF-07.1 | Se uma API externa (satélite, Ibama, ICMBio, lista suja, SICAR) estiver indisponível, o sistema deve usar a última resposta obtida com sucesso e exibir a data dela. | Confiabilidade | Piloto | RF-09.1, RF-03.1 |
| RNF-07.2 | Após falha de consulta a API externa, o sistema deve repetir a consulta com intervalo crescente (backoff exponencial iniciando em 1 min, dobrando a cada tentativa, até o máximo de 1 h). | Confiabilidade | Piloto | RNF-07.1 |
| RNF-08 | Senhas devem ser armazenadas apenas como hash com salt (bcrypt ou Argon2). | Segurança | Demo | RF-14.4 |
| RNF-09 | O sistema deve oferecer autenticação em dois fatores | Segurança | Pós-piloto | RF-14.4, RNF-08 |
| RNF-10.1 | O sistema deve registrar em trilha de auditoria (usuário, data/hora, ação, valor anterior e novo) a criação, alteração e exclusão de fornecedores, propriedades, lotes, critérios, perfis de validação, documentos e usuários, e as alterações manuais de status. | Segurança | Piloto | — |
| RNF-10.2 | A trilha de auditoria deve ser append-only: nenhum perfil, inclusive Administrador, pode editar ou excluir registros. | Segurança | Piloto | RNF-10.1 |
| RNF-11 | Registros de lotes e DDS devem ter hash armazenado, de modo que qualquer alteração posterior seja detectável. Alterações por anonimização (RS-03) geram novo hash, registrado na trilha. | Integridade | Pós-piloto | RF-05.1 |
| RNF-12 | Todo dado importado de fonte externa deve guardar fonte, data de obtenção e versão, para reproduzir a análise feita em qualquer data passada. | Integridade | Piloto | RF-02.1 |
| RNF-13 | O sistema deve expor API REST documentada em OpenAPI. | Interoperabilidade | Pós-piloto | — |
| RNF-14 | Importação e exportação devem usar formatos abertos (CSV, GeoJSON, JSON). | Interoperabilidade | Piloto | — |
| RNF-15.1 | A interface deve ser web e responsiva, utilizável de 360 px de largura até telas de desktop. | Usabilidade | Piloto | — |
| RNF-15.2 | A interface deve estar em português do Brasil. | Usabilidade | Demo | — |
| RNF-15.3 | A interface deve funcionar nas duas últimas versões estáveis de Chrome, Firefox e Edge | Usabilidade | Piloto | — |
| RNF-16.1 | O sistema deve gerar logs estruturados em JSON. | Manutenibilidade | Piloto | — |
| RNF-16.2 | O sistema deve expor métricas de saúde de consultas externas, filas e erros. | Manutenibilidade | Pós-piloto | — |
| RNF-17 | O sistema deve alertar o administrador quando uma fonte de dados de conformidade (listas de embargo, lista suja, SICAR) ficar mais de 24 h sem consulta bem-sucedida. | Manutenibilidade | Piloto | RF-03.1 |

## 4. Regras de Negócio

Políticas, normas e condições estabelecidas pela empresa que devem ser respeitadas na execução das funcionalidades.

| ID | Descrição | Estágio | Depende de |
|---|---|---|---|
| RN-01 | A soma das quantidades que saem de um lote (transferências, misturas e baixa) não pode ultrapassar seu saldo disponível, evitando que a mesma quantidade seja contabilizada duas vezes. | Piloto | RF-05.3 |
| RN-02 | Em toda mistura ou transformação, a massa de saída não pode exceder a massa de entrada multiplicada pelo rendimento declarado do processo; o rendimento deve estar dentro de limites configuráveis por tipo de processo. | Piloto | RF-05.3 |
| RN-03 | O grafo de lotes deve ser acíclico direcionado: um lote não pode ser origem de si mesmo, direta ou indiretamente. | Piloto | RF-05.2 |
| RN-04 | Todo lote deve ser rastreável até ao menos uma propriedade; lote sem origem identificada fica bloqueado para saída até a regularização. | Piloto | RF-05.1 |
| RN-05 | O status de cada critério de um lote é calculado apenas por balanço de massa: toda massa de saída de uma mistura ou transformação mantém a distribuição proporcional de status das massas de entrada. Massa inconforme ou não definida não pode se tornar conforme por mistura ou repasse. | Piloto | RF-05.3, RF-07.2 |
| RN-06 | Propriedade ou fornecedor com status inconforme em critério marcado como bloqueante não pode ter novos lotes de entrada vinculados até a regularização comprovada. | Piloto | RF-01.4 |
| RN-07 | O status de cada critério de um integrante de grupo econômico estende-se aos demais integrantes, critério a critério, prevalecendo o pior status (inconforme, depois não definido, depois conforme). A extensão não cria critério novo. | Pós-piloto | RF-04.1 |
| RN-08 | A alteração manual do status de um critério exige justificativa e documento anexo e só pode ser feita por usuário com permissão para isso (RF-14.6). | Piloto | RF-07.1, RF-12.1, RF-14.6 |
| RN-09 | Geometrias de propriedades devem ser válidas; sobreposição com propriedade de outro fornecedor acima de 5% da área da menor das duas (limite configurável) gera alerta de conflito para análise. | Pós-piloto | RF-01.2, RF-02.1 |
| RN-10 | No critério Situação do CAR, o status é inconforme se o CAR estiver cancelado, suspenso ou pendente; conforme se estiver ativo; não definido se ainda não houve consulta. | Demo | RF-03.1 |
| RN-11 | O sistema deve alertar quando o volume recebido de uma propriedade no ano exceder a produtividade máxima por hectare da cultura (tabela configurável pelo usuário) multiplicada pela área da propriedade. | Pós-piloto | RF-01.2, RF-05.1 |
| RN-12 | Uma mesma nota fiscal ou documento de transporte não pode ser vinculado a mais de um lote de entrada. | Piloto | RF-05.1 |
| RN-13 | Toda transferência de lote entre fornecedores deve ter documento fiscal anexo e não pode alterar a propriedade de origem registrada no lote. | Piloto | RF-05.2, RF-12.1 |
| RN-14 | Alterações no perfil de validação valem para lotes novos e em estoque, sem alterar retroativamente DDS já emitidas. | Pós-piloto | RF-06.1, RN-15 |
| RN-15 | DDS emitida não pode ser editada nem excluída e preserva o status de conformidade do lote vigente na emissão; correções são feitas por retificação ou cancelamento, vinculados à declaração original. | Pós-piloto | — |
| RN-16 | Um status de critério nunca é sobrescrito: cada nova avaliação gera um novo registro, preservando os anteriores. | Demo | RF-07.1 |
| RN-17 | Cada critério é avaliado no seu nível de aplicação (propriedade ou fornecedor), de modo que a mesma massa não seja contada duas vezes no mesmo critério. | Piloto | RF-07.2 |
| RN-18 | A massa com status não definido nunca é tratada como conforme: permanece como parcela separada na distribuição do lote. Status não aplicável não entra na distribuição. | Piloto | RF-07.2 |

## 5. Requisitos Suplementares

Exigências que não descrevem uma função nem a qualidade de uma função — legais, de documentação, de manutenção/suporte, de instalação ou de internacionalização.

| ID | Descrição | Categoria | Estágio | Depende de |
|---|---|---|---|---|
| RS-01 | Cada categoria de dado pessoal tratada (CPF, parentesco, localização) deve ter finalidade e base legal declaradas em registro de tratamento. | Legal (LGPD) | Piloto | RF-01.1, RF-14.1 |
| RS-02 | O sistema deve coletar apenas os dados pessoais necessários à finalidade declarada (minimização). | Legal (LGPD) | Piloto | RS-01 |
| RS-03 | O sistema deve permitir atender solicitações de titulares (acesso, correção, eliminação ou anonimização) em até 15 dias. | Legal (LGPD) | Piloto | RS-01 |

## Apêndice A — Grafo de dependências

Seta de A para B: B depende de A. Verde = Demo, amarelo = Piloto, cinza = Pós-piloto. Gerado da coluna "Depende de".

```mermaid
flowchart LR
  RF_01_1["RF-01.1"]
  RF_01_2["RF-01.2"]
  RF_01_3["RF-01.3"]
  RF_01_4["RF-01.4"]
  RF_01_5["RF-01.5"]
  RF_02_1["RF-02.1"]
  RF_02_2["RF-02.2"]
  RF_03_1["RF-03.1"]
  RF_03_2["RF-03.2"]
  RF_04_1["RF-04.1"]
  RF_04_2["RF-04.2"]
  RF_05_1["RF-05.1"]
  RF_05_2["RF-05.2"]
  RF_05_3["RF-05.3"]
  RF_05_4["RF-05.4"]
  RF_05_5["RF-05.5"]
  RF_06_1["RF-06.1"]
  RF_06_2["RF-06.2"]
  RF_07_1["RF-07.1"]
  RF_07_2["RF-07.2"]
  RF_07_3["RF-07.3"]
  RF_07_4["RF-07.4"]
  RF_08_1["RF-08.1"]
  RF_08_2["RF-08.2"]
  RF_08_3["RF-08.3"]
  RF_09_1["RF-09.1"]
  RF_09_2["RF-09.2"]
  RF_10_1["RF-10.1"]
  RF_10_2["RF-10.2"]
  RF_10_3["RF-10.3"]
  RF_11_1["RF-11.1"]
  RF_11_2["RF-11.2"]
  RF_11_3["RF-11.3"]
  RF_12_1["RF-12.1"]
  RF_12_2["RF-12.2"]
  RF_12_3["RF-12.3"]
  RF_13_1["RF-13.1"]
  RF_13_2["RF-13.2"]
  RF_13_3["RF-13.3"]
  RF_13_4["RF-13.4"]
  RF_13_5["RF-13.5"]
  RF_14_1["RF-14.1"]
  RF_14_2["RF-14.2"]
  RF_14_3["RF-14.3"]
  RF_14_4["RF-14.4"]
  RF_14_5["RF-14.5"]
  RF_14_6["RF-14.6"]
  RNF_01["RNF-01"]
  RNF_02["RNF-02"]
  RNF_03["RNF-03"]
  RNF_04["RNF-04"]
  RNF_05["RNF-05"]
  RNF_06["RNF-06"]
  RNF_07_1["RNF-07.1"]
  RNF_07_2["RNF-07.2"]
  RNF_08["RNF-08"]
  RNF_09["RNF-09"]
  RNF_10_1["RNF-10.1"]
  RNF_10_2["RNF-10.2"]
  RNF_11["RNF-11"]
  RNF_12["RNF-12"]
  RNF_13["RNF-13"]
  RNF_14["RNF-14"]
  RNF_15_1["RNF-15.1"]
  RNF_15_2["RNF-15.2"]
  RNF_15_3["RNF-15.3"]
  RNF_16_1["RNF-16.1"]
  RNF_16_2["RNF-16.2"]
  RNF_17["RNF-17"]
  RN_01["RN-01"]
  RN_02["RN-02"]
  RN_03["RN-03"]
  RN_04["RN-04"]
  RN_05["RN-05"]
  RN_06["RN-06"]
  RN_07["RN-07"]
  RN_08["RN-08"]
  RN_09["RN-09"]
  RN_10["RN-10"]
  RN_11["RN-11"]
  RN_12["RN-12"]
  RN_13["RN-13"]
  RN_14["RN-14"]
  RN_15["RN-15"]
  RN_16["RN-16"]
  RN_17["RN-17"]
  RN_18["RN-18"]
  RS_01["RS-01"]
  RS_02["RS-02"]
  RS_03["RS-03"]
  RF_01_1 --> RF_01_2
  RF_01_2 --> RF_01_3
  RF_07_1 --> RF_01_4
  RF_01_2 --> RF_01_5
  RF_01_2 --> RF_02_1
  RF_02_1 --> RF_02_2
  RF_01_2 --> RF_03_1
  RF_07_1 --> RF_03_1
  RF_03_1 --> RF_03_2
  RF_01_1 --> RF_04_1
  RF_04_1 --> RF_04_2
  RF_07_1 --> RF_04_2
  RF_01_2 --> RF_05_1
  RF_05_1 --> RF_05_2
  RF_05_2 --> RF_05_3
  RF_05_3 --> RF_05_4
  RF_05_2 --> RF_05_5
  RF_06_1 --> RF_06_2
  RF_07_2 --> RF_06_2
  RF_05_3 --> RF_07_2
  RF_07_1 --> RF_07_2
  RF_06_2 --> RF_07_3
  RF_07_2 --> RF_07_3
  RF_07_1 --> RF_07_4
  RF_07_2 --> RF_07_4
  RF_08_1 --> RF_08_2
  RF_08_1 --> RF_08_3
  RF_01_2 --> RF_09_1
  RF_09_1 --> RF_09_2
  RF_01_2 --> RF_10_1
  RF_10_1 --> RF_10_2
  RF_10_1 --> RF_10_3
  RF_05_1 --> RF_11_1
  RF_06_2 --> RF_11_1
  RF_07_2 --> RF_11_1
  RF_01_4 --> RF_11_2
  RF_03_2 --> RF_11_2
  RF_09_2 --> RF_11_2
  RF_11_1 --> RF_11_3
  RF_11_2 --> RF_11_3
  RF_01_1 --> RF_12_1
  RF_01_2 --> RF_12_1
  RF_05_1 --> RF_12_1
  RF_12_1 --> RF_12_2
  RF_12_1 --> RF_12_3
  RF_05_4 --> RF_13_2
  RF_12_1 --> RF_13_2
  RF_13_1 --> RF_13_2
  RF_05_1 --> RF_13_3
  RF_07_1 --> RF_13_3
  RF_06_2 --> RF_13_4
  RF_13_2 --> RF_13_4
  RF_13_3 --> RF_13_5
  RF_13_4 --> RF_13_5
  RF_14_1 --> RF_14_2
  RF_14_1 --> RF_14_3
  RF_14_1 --> RF_14_4
  RF_14_4 --> RF_14_5
  RF_14_3 --> RF_14_6
  RF_10_1 --> RNF_01
  RF_10_2 --> RNF_02
  RF_10_3 --> RNF_02
  RF_06_2 --> RNF_03
  RF_09_1 --> RNF_07_1
  RF_03_1 --> RNF_07_1
  RNF_07_1 --> RNF_07_2
  RF_14_4 --> RNF_08
  RF_14_4 --> RNF_09
  RNF_08 --> RNF_09
  RNF_10_1 --> RNF_10_2
  RF_05_1 --> RNF_11
  RF_02_1 --> RNF_12
  RF_03_1 --> RNF_17
  RF_05_3 --> RN_01
  RF_05_3 --> RN_02
  RF_05_2 --> RN_03
  RF_05_1 --> RN_04
  RF_05_3 --> RN_05
  RF_07_2 --> RN_05
  RF_01_4 --> RN_06
  RF_04_1 --> RN_07
  RF_07_1 --> RN_08
  RF_12_1 --> RN_08
  RF_14_6 --> RN_08
  RF_01_2 --> RN_09
  RF_02_1 --> RN_09
  RF_03_1 --> RN_10
  RF_01_2 --> RN_11
  RF_05_1 --> RN_11
  RF_05_1 --> RN_12
  RF_05_2 --> RN_13
  RF_12_1 --> RN_13
  RF_06_1 --> RN_14
  RN_15 --> RN_14
  RF_07_1 --> RN_16
  RF_07_2 --> RN_17
  RF_07_2 --> RN_18
  RF_01_1 --> RS_01
  RF_14_1 --> RS_01
  RS_01 --> RS_02
  RS_01 --> RS_03
  classDef Demo fill:#d4edda,stroke:#2e7d32;
  classDef Piloto fill:#fff3cd,stroke:#b8860b;
  classDef Posp fill:#e2e3e5,stroke:#555;
  class RF_01_1 Demo;
  class RF_01_2 Demo;
  class RF_01_3 Demo;
  class RF_01_4 Demo;
  class RF_01_5 Posp;
  class RF_02_1 Piloto;
  class RF_02_2 Piloto;
  class RF_03_1 Demo;
  class RF_03_2 Demo;
  class RF_04_1 Posp;
  class RF_04_2 Posp;
  class RF_05_1 Demo;
  class RF_05_2 Piloto;
  class RF_05_3 Piloto;
  class RF_05_4 Piloto;
  class RF_05_5 Piloto;
  class RF_06_1 Piloto;
  class RF_06_2 Piloto;
  class RF_07_1 Demo;
  class RF_07_2 Piloto;
  class RF_07_3 Piloto;
  class RF_07_4 Posp;
  class RF_08_1 Demo;
  class RF_08_2 Demo;
  class RF_08_3 Posp;
  class RF_09_1 Demo;
  class RF_09_2 Demo;
  class RF_10_1 Demo;
  class RF_10_2 Piloto;
  class RF_10_3 Piloto;
  class RF_11_1 Piloto;
  class RF_11_2 Demo;
  class RF_11_3 Piloto;
  class RF_12_1 Demo;
  class RF_12_2 Piloto;
  class RF_12_3 Posp;
  class RF_13_1 Piloto;
  class RF_13_2 Piloto;
  class RF_13_3 Demo;
  class RF_13_4 Piloto;
  class RF_13_5 Posp;
  class RF_14_1 Demo;
  class RF_14_2 Piloto;
  class RF_14_3 Demo;
  class RF_14_4 Demo;
  class RF_14_5 Piloto;
  class RF_14_6 Piloto;
  class RNF_01 Piloto;
  class RNF_02 Piloto;
  class RNF_03 Piloto;
  class RNF_04 Posp;
  class RNF_05 Posp;
  class RNF_06 Piloto;
  class RNF_07_1 Piloto;
  class RNF_07_2 Piloto;
  class RNF_08 Demo;
  class RNF_09 Posp;
  class RNF_10_1 Piloto;
  class RNF_10_2 Piloto;
  class RNF_11 Posp;
  class RNF_12 Piloto;
  class RNF_13 Posp;
  class RNF_14 Piloto;
  class RNF_15_1 Piloto;
  class RNF_15_2 Demo;
  class RNF_15_3 Piloto;
  class RNF_16_1 Piloto;
  class RNF_16_2 Posp;
  class RNF_17 Piloto;
  class RN_01 Piloto;
  class RN_02 Piloto;
  class RN_03 Piloto;
  class RN_04 Piloto;
  class RN_05 Piloto;
  class RN_06 Piloto;
  class RN_07 Posp;
  class RN_08 Piloto;
  class RN_09 Posp;
  class RN_10 Demo;
  class RN_11 Posp;
  class RN_12 Piloto;
  class RN_13 Piloto;
  class RN_14 Posp;
  class RN_15 Posp;
  class RN_16 Demo;
  class RN_17 Piloto;
  class RN_18 Piloto;
  class RS_01 Piloto;
  class RS_02 Piloto;
  class RS_03 Piloto;
```

## Apêndice B — Como usar com agentes

- A fonte estruturada é `requisitos.yaml`: cada requisito tem `id`, `tipo`, `estagio`, `depende_de` e `requerido_por` (relação inversa).
- Atores e entidades estão em `mini_mundo`; definições de termos em `glossario`.
- Para diagramas (casos de uso, classes, sequência), derive atores e entidades de `mini_mundo` e as funções de `requisitos` com `tipo: funcional`.
- Para planejar entregas, filtre por `estagio`; nenhum requisito depende de outro de estágio posterior.
- Os grupos (RF-01, RF-02…) não têm nome no documento original; o campo `grupo` traz só o prefixo do ID.
