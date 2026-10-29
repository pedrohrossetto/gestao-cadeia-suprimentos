# Requisitos Complementares: Não Funcionais e Regras de Negócio

Complementa o [requisitos.md](requisitos.md), mantendo a numeração existente (continua de RNF-01 e RN-02). Os RF foram reorganizados em [requisitos-revisado.md](requisitos-revisado.md); as referências `RF-02` aqui valem para o conjunto RF-02.1 a RF-02.4.

## Convenções

- **Origem:** requisito funcional ou regra que o item complementa; `—` quando é transversal.
- **Prioridade (MoSCoW):** **M** = deve (essencial), **S** = deveria, **C** = poderia.
- **Verificação:** **T** = teste automatizado, **D** = demonstração, **I** = inspeção (código, configuração, documento), **A** = análise (medição, teste de carga).
- Valores numéricos são metas iniciais, a validar com o professor/usuário.


## Requisitos Não Funcionais

### Desempenho (ISO 25010: eficiência de desempenho)

| ID | Requisito | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RNF-02 | A visão inicial do mapa deve carregar em até 3 s e filtros/buscas devem responder em até 2 s (percentil 95) com 10.000 propriedades na base, usando clusterização e tiles vetoriais. | RF-05 | S | A |
| RNF-03 | A validação de conformidade de um lote, incluindo a travessia do grafo de origem, deve concluir em até 10 s (percentil 95) para lotes com até 5 níveis de mistura. | RF-03 | S | A |
| RNF-04 | O recálculo do Score de Risco ESG após mudança de pesos deve ser assíncrono e concluir em até 5 min para uma base de 200.000 propriedades. | RF-11 | C | A |

### Escalabilidade

| ID | Requisito | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RNF-05 | O sistema deve suportar 50.000 fornecedores, 200.000 propriedades e 5 milhões de lotes mantendo as metas de RNF-02 e RNF-03. | — | C | A |
| RNF-06 | A importação de arquivos geoespaciais (.shp, .geojson, .kml) de até 100 MB deve ser processada em fila, sem bloquear a interface, informando o andamento e os erros por arquivo. | RF-01 | S | D |

### Disponibilidade e confiabilidade

| ID | Requisito | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RNF-07 | O sistema deve ter disponibilidade mensal de 99,5%, excluídas janelas de manutenção comunicadas com antecedência. | — | C | A |
| RNF-08 | Deve haver backup automático diário, retido por 30 dias, com RPO de 24 h e RTO de 4 h, e restauração testada ao menos uma vez antes da entrega. | — | S | D |
| RNF-09 | Se uma API externa (satélite, Ibama, ICMBio, lista suja) estiver indisponível, o sistema deve usar a última cópia válida, exibir a data dessa cópia e repetir a consulta com intervalo crescente (backoff). | RF-04, RF-08 | M | T |

### Segurança

| ID | Requisito | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RNF-10 | O acesso deve exigir autenticação; senhas devem ser armazenadas apenas como hash com salt (bcrypt ou Argon2). | — | M | I |
| RNF-11 | O sistema deve oferecer autenticação em dois fatores, ao menos para perfis Administrador e Analista de Conformidade. | RNF-10 | C | D |
| RNF-12 | O acesso às funcionalidades deve ser controlado por perfil (RBAC), com ao menos: Administrador, Analista de Conformidade, Operador de Lotes e Auditor (somente leitura). | — | M | T |
| RNF-13 | Toda comunicação deve usar TLS 1.2 ou superior. | — | M | I |
| RNF-14 | CPF, CNPJ e coordenadas de propriedades devem ser criptografados em repouso. | RNF-16 | S | I |
| RNF-15 | O sistema deve manter trilha de auditoria append-only (usuário, data/hora, ação, valor anterior e novo) para criação, alteração e exclusão de fornecedores, propriedades, lotes, pesos de score e declarações, retida por no mínimo 5 anos. | RN-01, RN-02 | M | T |

### Conformidade legal

| ID | Requisito | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RNF-16 | O tratamento de dados pessoais (CPF, parentesco, localização) deve seguir a LGPD: finalidade declarada, minimização e meio de atender solicitações de titulares. | RF-01, RF-10 | M | I |
| RNF-17 | As DDS devem seguir o formato do Regulamento (UE) 2023/1115 (EUDR) e a especificação de integração vigente do TRACES. | RF-09 | M | I |

### Integridade e rastreabilidade

| ID | Requisito | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RNF-18 | Registros de lotes e DDS devem ter hash armazenado, de modo que qualquer alteração posterior seja detectável. | RF-02, RF-09 | M | T |
| RNF-19 | Todo dado importado de fonte externa deve guardar fonte, data de obtenção e versão, para reproduzir a análise feita em qualquer data passada. | RF-01, RF-08 | M | T |

### Interoperabilidade

| ID | Requisito | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RNF-20 | O sistema deve expor API REST documentada em OpenAPI. | RF-12 | S | I |
| RNF-21 | Importação e exportação devem usar formatos abertos (CSV, GeoJSON, JSON). | — | S | T |
| RNF-22 | Geometrias devem ser armazenadas em um único sistema de referência (WGS 84, EPSG:4326, por compatibilidade com GeoJSON e APIs de satélite); geometrias importadas em outro sistema (ex.: SIRGAS 2000) devem ser convertidas na importação. | RF-01 | M | T |

### Usabilidade e portabilidade

| ID | Requisito | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RNF-23 | A interface deve ser web, responsiva, em português do Brasil, compatível com as duas últimas versões estáveis de Chrome, Firefox, Edge e Safari. | — | M | D |
| RNF-24 | A interface deve atender ao nível AA das WCAG 2.1. | RNF-23 | C | A |
| RNF-25 | O sistema deve poder ser implantado em contêineres, com configuração por variáveis de ambiente. | — | S | I |

### Manutenibilidade e observabilidade

| ID | Requisito | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RNF-26 | As regras de negócio devem ter cobertura de testes automatizados de ao menos 70%, executados em integração contínua a cada alteração. | — | S | A |
| RNF-27 | O sistema deve gerar logs estruturados (JSON) e métricas de saúde de consultas externas, filas e erros. | — | S | I |
| RNF-28 | O sistema deve alertar o administrador quando uma fonte de dados de conformidade ficar mais de 24 h sem atualização. | RF-08 | S | T |


## Regras de Negócio

### Lotes e balanço de massa

| ID | Regra | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RN-03 | Em toda mistura ou transformação, a massa de saída não pode exceder a massa de entrada multiplicada pelo rendimento declarado do processo; o rendimento deve estar dentro de limites configuráveis por tipo de processo. | RF-02 | M | T |
| RN-04 | O grafo de lotes deve ser acíclico: um lote não pode ser origem de si mesmo, direta ou indiretamente. | RF-02 | M | T |
| RN-05 | A soma das saídas de um lote não pode ultrapassar seu saldo disponível; a baixa no destino final só pode ocorrer uma vez por quantidade. | RF-02, RN-01 | M | T |
| RN-06 | Todo lote deve ser rastreável até ao menos uma propriedade; lote sem origem identificada fica bloqueado para saída até a regularização. | RF-02 | M | T |
| RN-07 | Alteração de tonelagem, data ou origem de um lote após a emissão de DDS deve gerar nova versão do lote, preservando a anterior. | RF-02, RF-09 | M | T |

### Conformidade

| ID | Regra | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RN-08 | Lote misturado herda o pior status de conformidade entre suas origens, salvo se o usuário configurar segregação comprovada por balanço de massa. | RF-03 | M | T |
| RN-09 | Propriedade ou fornecedor presente em lista de embargo ou na lista suja é marcado como inconforme e fica impedido de receber novas entradas de lotes até a regularização comprovada. | RF-01, RF-08 | M | T |
| RN-10 | A cada atualização das listas oficiais, os lotes de fornecedores afetados, ainda em estoque ou em trânsito, devem ser reavaliados. | RF-08, RN-02 | M | T |
| RN-11 | A inconformidade de um CPF ou CNPJ estende-se aos demais integrantes do grupo econômico, no score e no bloqueio. | RF-10 | M | T |
| RN-12 | A remoção de uma inconformidade só pode ser feita por Analista de Conformidade ou superior, com justificativa e documento anexo, e é registrada em auditoria. | RF-01, RNF-15 | M | T |
| RN-13 | Geometrias de propriedades devem ser válidas; sobreposição com propriedade de outro fornecedor acima de um limite configurável gera alerta de conflito para análise. | RF-01 | S | T |
| RN-14 | CAR cancelado, suspenso ou pendente torna a propriedade inconforme; a mudança de status do CAR deve gerar alerta. | RF-07 | M | T |

### Dupla contagem e lavagem de grãos

| ID | Regra | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RN-15 | O sistema deve sinalizar volume recebido de uma propriedade acima da produtividade máxima plausível para sua área e cultura, indicando possível lavagem. | RN-02 | S | T |
| RN-16 | Uma mesma nota fiscal ou documento de transporte não pode ser vinculado a mais de um lote de entrada. | RN-01 | M | T |
| RN-17 | Toda transferência de lote entre fornecedores deve ter documento fiscal anexo e preservar a propriedade de origem no registro do lote, sem permitir troca de origem. | RN-02 | M | T |

### Score de risco

| ID | Regra | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RN-18 | Os pesos do score devem somar 100%; cada alteração gera nova versão da configuração, mantendo o histórico para reproduzir scores já calculados. | RF-11 | M | T |
| RN-19 | O score de um nível superior (Região, Área) é agregado a partir dos níveis inferiores (Produtor, Propriedade, Lote); a presença de critério eliminatório (embargo, trabalho análogo à escravidão) resulta em risco máximo, independentemente dos pesos. | RF-11 | M | T |
| RN-20 | Critérios de conformidade definidos pelo usuário valem para lotes novos e em estoque, sem alterar retroativamente DDS já emitidas. | RF-03, RN-22 | M | T |

### Declaração de devida diligência (DDS)

| ID | Regra | Origem | Prior. | Verif. |
|---|---|---|---|---|
| RN-21 | A DDS só pode ser emitida para lote validado, sem origem inconforme, contendo a geolocalização de todas as propriedades de origem. | RF-09 | M | T |
| RN-22 | DDS emitida não pode ser editada nem excluída; correções são feitas por retificação ou cancelamento, vinculados à declaração original. | RF-09 | M | T |
| RN-23 | Alerta de desmatamento posterior à data de corte (31/12/2020, EUDR) torna a propriedade inconforme até decisão de um Analista de Conformidade. | RF-04 | M | T |
