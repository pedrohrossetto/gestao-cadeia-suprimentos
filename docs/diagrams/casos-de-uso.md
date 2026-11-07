# Casos de Uso

Diagramas de casos de uso do sistema, em Mermaid. O Mermaid não tem diagrama de casos de uso nativo, então usa-se `flowchart`:

- **Atores:** retângulos de cantos arredondados fora da fronteira do sistema.
- **Casos de uso:** elipses (`([ ])`) dentro do subgrafo "Sistema".
- **Associação:** seta contínua. **Include/extend:** seta tracejada com estereótipo. A seta de `«extend»` sai do caso que estende e aponta para o caso-base.

## Atores

| Ator | Tipo | Descrição |
|---|---|---|
| Administrador | Humano | Gerencia usuários, perfis e configuração do sistema (RNF-12). |
| Analista de Conformidade | Humano | Gerencia critérios, perfil de validação, status manual, score e DDS (RF-13.7, RN-12). Em "Analista ou superior", o Administrador herda estas permissões. |
| Operador de Lotes | Humano | Cadastra fornecedores, propriedades e lotes e registra movimentações (RNF-12). |
| Auditor | Humano | Acesso somente leitura, incluindo a trilha de auditoria (RNF-12). |
| Temporizador | Sistema | Dispara consultas diárias e reprocessamentos (RF-07.1, RF-08.1, RF-08.2, RNF-09.2). |
| SICAR · Ibama/ICMBio · Lista suja | Externo | Fontes de conformidade (RF-07.1, RF-08.x). |
| APIs de satélite e desmatamento | Externo | Imagens e alertas (RF-04.x). |
| ERP / Telemetria | Externo | Origem de lotes automáticos (RF-12). |
| Sistema do comprador | Externo | Destino da DDS (RF-09.2). |

Todos os casos de uso humanos pressupõem **Autenticar** (RNF-10). Não é desenhado em cada diagrama para não poluí-los; aparece no diagrama 6.

---

## 0. Visão geral

Cada caso de uso representa um módulo, detalhado nos diagramas seguintes.

```mermaid
flowchart LR
    adm([Administrador])
    ana([Analista de Conformidade])
    ope([Operador de Lotes])
    aud([Auditor])
    tmp([Temporizador])
    ext1([SICAR / Ibama / ICMBio / Lista suja])
    ext2([APIs de satélite e desmatamento])
    ext3([ERP / Telemetria])
    ext4([Sistema do comprador])

    subgraph SIS["Sistema de Gestão da Cadeia de Suprimentos"]
        m1([Gerir fornecedores e propriedades])
        m2([Gerir lotes e rastreabilidade])
        m3([Gerir conformidade])
        m4([Gerir risco e DDS])
        m5([Visualizar dados geoespaciais])
        m6([Administrar e auditar])
    end

    ope --- m1
    ana --- m1
    ope --- m2
    ana --- m3
    ana --- m4
    ope --- m4
    ope --- m5
    ana --- m5
    aud --- m5
    adm --- m6
    aud --- m6
    tmp --- m3
    ext1 --- m3
    ext2 --- m5
    ext3 --- m2
    ext4 --- m4
```

---

## 1. Fornecedores e propriedades

```mermaid
flowchart LR
    ope([Operador de Lotes])
    ana([Analista de Conformidade])
    aud([Auditor])

    subgraph SIS["Fornecedores e propriedades"]
        uc1([UC01 Cadastrar fornecedor])
        uc2([UC02 Cadastrar propriedade])
        uc3([UC03 Importar geometria de propriedades])
        uc3a([Validar geometria e converter para EPSG:4326])
        uc3b([Informar erros da importação])
        uc4([UC04 Gerenciar Áreas])
        uc5([UC05 Registrar grupo econômico e parentesco])
        uc6([UC06 Consultar detalhe de fornecedor/propriedade])
        uc6a([Exibir status dos critérios])
    end

    ope --- uc1
    ope --- uc2
    ope --- uc3
    ope --- uc4
    ana --- uc5
    ope --- uc6
    ana --- uc6
    aud --- uc6

    uc2 -.->|«include»| uc3a
    uc3 -.->|«include»| uc3a
    uc3b -.->|«extend»| uc3
    uc6 -.->|«include»| uc6a
```

Cobre: RF-01.1 a RF-01.5, RF-06.1, RF-06.2, RF-10.1, RNF-22.2, RN-13.

---

## 2. Lotes e rastreabilidade

```mermaid
flowchart LR
    ope([Operador de Lotes])
    aud([Auditor])
    erp([ERP / Telemetria])

    subgraph SIS["Lotes e rastreabilidade"]
        uc7([UC07 Cadastrar lote])
        uc8([UC08 Registrar mistura ou transformação])
        uc9([UC09 Transferir lote entre fornecedores])
        uc10([UC10 Dar baixa em lote no destino final])
        uc11([UC11 Gerar passaporte digital])
        uc11a([Exportar em PDF ou JSON])
        uc12([UC12 Sincronizar lotes com ERP/telemetria])
        uc13([UC13 Consultar balanço de massa e origem do lote])
        chk([Verificar saldo e regras de massa])
    end

    ope --- uc7
    ope --- uc8
    ope --- uc9
    ope --- uc10
    ope --- uc11
    ope --- uc13
    aud --- uc11
    aud --- uc13
    erp --- uc12

    uc7 -.->|«include»| chk
    uc8 -.->|«include»| chk
    uc9 -.->|«include»| chk
    uc10 -.->|«include»| chk
    uc12 -.->|«include»| uc7
    uc11a -.->|«extend»| uc11
```

Cobre: RF-02.1 a RF-02.5, RF-12, RN-01, RN-03 a RN-07, RN-16, RN-17, RNF-01.x. "Verificar saldo e regras de massa" é a verificação compartilhada de RN-01, RN-03, RN-04 e RN-06.

---

## 3. Conformidade

```mermaid
flowchart LR
    ana([Analista de Conformidade])
    aud([Auditor])
    tmp([Temporizador])
    src([SICAR / Ibama / ICMBio / Lista suja])

    subgraph SIS["Conformidade"]
        uc14([UC14 Consultar fontes de conformidade])
        uc14a([Registrar status do critério])
        uc14b([Usar última resposta em caso de falha])
        uc14c([Notificar mudança de situação do CAR])
        uc15([UC15 Gerenciar critérios do usuário])
        uc15a([Criar critério])
        uc15b([Editar, desativar ou arquivar critério])
        uc15c([Simular critério])
        uc16([UC16 Definir perfil de validação])
        uc17([UC17 Validar lote])
        uc18([UC18 Alterar status de critério manualmente])
        uc18a([Anexar justificativa e documento])
        uc19([UC19 Consultar status em data passada])
        uc20([UC20 Consultar distribuição de massa do lote])
        rec([Recalcular status de lotes afetados])
    end

    tmp --- uc14
    src --- uc14
    ana --- uc15
    ana --- uc16
    ana --- uc17
    ana --- uc18
    ana --- uc19
    aud --- uc19
    ana --- uc20
    aud --- uc20

    uc14 -.->|«include»| uc14a
    uc14b -.->|«extend»| uc14
    uc14c -.->|«extend»| uc14
    uc15a -.->|«extend»| uc15
    uc15b -.->|«extend»| uc15
    uc15c -.->|«extend»| uc15a
    uc14a -.->|«include»| rec
    uc18 -.->|«include»| uc18a
    uc18 -.->|«include»| uc14a
    uc17 -.->|«include»| uc20
    uc15b -.->|«include»| rec
```

Cobre: RF-07.1, RF-07.2, RF-08.1 a RF-08.3, RF-03.1, RF-03.2, RF-13.1 a RF-13.9, RN-09 a RN-12, RN-14, RN-24 a RN-30, RNF-09.x, RNF-28. O recálculo (RF-13.4, RN-10) é comportamento interno, detalhado em diagrama de atividade/sequência, não em caso de uso.

---

## 4. Risco e DDS

```mermaid
flowchart LR
    ana([Analista de Conformidade])
    ope([Operador de Lotes])
    aud([Auditor])
    comp([Sistema do comprador])

    subgraph SIS["Risco e DDS"]
        uc21([UC21 Consultar Score de Risco ESG])
        uc22([UC22 Configurar pesos do score])
        uc22a([Recalcular scores])
        uc23([UC23 Marcar lote como expedido])
        uc24([UC24 Emitir DDS])
        uc24a([Validar lote pelo perfil vigente])
        uc25([UC25 Transmitir DDS ao comprador])
        uc26([UC26 Retificar ou cancelar DDS])
        uc27([UC27 Consultar DDS emitida])
    end

    ana --- uc21
    ope --- uc21
    aud --- uc21
    ana --- uc22
    ope --- uc23
    ana --- uc26
    ana --- uc27
    aud --- uc27
    uc25 --- comp

    uc22 -.->|«include»| uc22a
    uc23 -.->|«include»| uc24
    uc24 -.->|«include»| uc24a
    uc24 -.->|«include»| uc25
    uc26 -.->|«include»| uc25
```

Cobre: RF-11.1, RF-11.2, RF-09.1, RF-09.2, RN-18 a RN-22, RNF-04, RNF-18. O sistema do comprador participa de UC25. UC23, UC26 e UC27 não têm RF próprio (ver lacunas).

---

## 5. Visualização geoespacial

```mermaid
flowchart LR
    ope([Operador de Lotes])
    ana([Analista de Conformidade])
    aud([Auditor])
    sat([APIs de satélite e desmatamento])

    subgraph SIS["Visualização"]
        uc28([UC28 Ver imagem de satélite da propriedade])
        uc28a([Escolher fonte de satélite])
        uc29([UC29 Ver alertas de desmatamento])
        uc30([UC30 Explorar mapa interativo])
        uc30a([Filtrar por critério, cultura e UF])
        uc30b([Buscar por município ou coordenadas])
    end

    ope --- uc28
    ana --- uc28
    aud --- uc28
    ana --- uc29
    aud --- uc29
    ope --- uc30
    ana --- uc30
    aud --- uc30
    uc28 --- sat
    uc29 --- sat

    uc28a -.->|«extend»| uc28
    uc29 -.->|«include»| uc28
    uc30a -.->|«extend»| uc30
    uc30b -.->|«extend»| uc30
```

Cobre: RF-04.1, RF-04.2, RF-05.1 a RF-05.3, RNF-02.x, RNF-09.1.

---

## 6. Administração e governança

```mermaid
flowchart LR
    adm([Administrador])
    aud([Auditor])
    usr([Qualquer usuário])

    subgraph SIS["Administração e governança"]
        uc31([UC31 Autenticar])
        uc31a([Verificar segundo fator])
        uc32([UC32 Gerenciar usuários e perfis])
        uc33([UC33 Consultar trilha de auditoria])
        uc34([UC34 Atender solicitação de titular LGPD])
        uc35([UC35 Manter registro de tratamento de dados])
        uc36([UC36 Monitorar saúde das fontes e integrações])
    end

    usr --- uc31
    adm --- uc32
    aud --- uc33
    adm --- uc33
    adm --- uc34
    adm --- uc35
    adm --- uc36

    uc31a -.->|«extend»| uc31
    uc32 -.->|«include»| uc31
    uc33 -.->|«include»| uc31
    uc34 -.->|«include»| uc31
```

Cobre: RNF-10 a RNF-12, RNF-15.x, RNF-16.x, RNF-27.2, RNF-28. Todos os demais casos de uso humanos também incluem UC31, omitido nos outros diagramas.

---

## Rastreabilidade (caso de uso → requisitos)

| UC | Caso de uso | Requisitos |
|---|---|---|
| UC01 | Cadastrar fornecedor | RF-01.1 |
| UC02 | Cadastrar propriedade | RF-01.2, RF-01.3, RNF-22.1 |
| UC03 | Importar geometria | RF-06.1, RF-06.2, RNF-06, RNF-22.2, RN-13 |
| UC04 | Gerenciar Áreas | RF-01.5 |
| UC05 | Registrar grupo econômico | RF-10.1, RF-10.2, RN-11 |
| UC06 | Consultar detalhe | RF-01.3, RF-01.4 |
| UC07 | Cadastrar lote | RF-02.1, RN-06, RN-07, RN-16 |
| UC08 | Registrar mistura/transformação | RF-02.2, RF-02.3, RN-01, RN-03, RN-04 |
| UC09 | Transferir lote entre fornecedores | RN-17, RN-02 |
| UC10 | Dar baixa | RF-02.4, RN-05 |
| UC11 | Gerar passaporte | RF-02.5 |
| UC12 | Sincronizar com ERP | RF-12, RNF-01.x |
| UC13 | Consultar balanço e origem | RF-02.3, RF-02.2 |
| UC14 | Consultar fontes | RF-07.1, RF-07.2, RF-08.1 a RF-08.3, RNF-09.x, RN-14 |
| UC15 | Gerenciar critérios do usuário | RF-13.7 a RF-13.9, RN-27 a RN-30 |
| UC16 | Definir perfil de validação | RF-03.1, RN-20 |
| UC17 | Validar lote | RF-03.2, RF-13.3, RNF-03 |
| UC18 | Alterar status manualmente | RN-12 |
| UC19 | Consultar status passado | RF-13.5, RN-24 |
| UC20 | Consultar distribuição de massa | RF-13.6 |
| UC21 | Consultar score | RF-11.1, RN-19 |
| UC22 | Configurar pesos | RF-11.2, RN-18, RNF-04 |
| UC23 | Marcar lote como expedido | gatilho de RF-09.1 |
| UC24 | Emitir DDS | RF-09.1, RN-21, RN-22 |
| UC25 | Transmitir DDS | RF-09.2 |
| UC26 | Retificar/cancelar DDS | RN-22 |
| UC27 | Consultar DDS | RN-22, RNF-18 |
| UC28 | Ver imagem de satélite | RF-04.1 |
| UC29 | Ver alertas de desmatamento | RF-04.2 |
| UC30 | Explorar mapa | RF-05.1 a RF-05.3 |
| UC31 | Autenticar | RNF-10, RNF-11 |
| UC32 | Gerenciar usuários e perfis | RNF-12 |
| UC33 | Consultar trilha de auditoria | RNF-15.1 a RNF-15.3 |
| UC34 | Atender solicitação de titular | RNF-16.3, RNF-15.3 |
| UC35 | Manter registro de tratamento | RNF-16.1, RNF-16.2 |
| UC36 | Monitorar fontes e integrações | RNF-27.2, RNF-28 |

## Lacunas de requisitos

Estes casos de uso não têm RF próprio, só regra de negócio ou RNF. Decidir se viram RFs ou se ficam como derivados:

- UC18 (RN-12), UC23 (gatilho de RF-09.1), UC26 (RN-22), UC27, UC32 (RNF-12), UC33 (RNF-15.1), UC34 (RNF-16.3), UC35 (RNF-16.1) e UC36 (RNF-28).
