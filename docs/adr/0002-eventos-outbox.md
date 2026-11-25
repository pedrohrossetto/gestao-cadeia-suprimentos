# ADR-002: Arquitetura orientada a eventos com outbox transacional

**Status:** Aceito
**Data:** 25/11/2026
**Decisores:** Pedro Henrique Carteli Rossetto

## Contexto

Várias funções do sistema são reações a fatos já registrados: a consulta diária às fontes gera status de critério (RF-03.1), a mudança da situação do CAR gera alerta (RF-03.2), toda alteração gera registro de auditoria (RNF-10.1) e a mudança de status de uma origem afeta a validação de lotes (RF-07.3). Essas reações não podem se perder se o processo cair, nem ser publicadas se a alteração original falhar.

## Decisão

- O sistema roda em **dois processos** do mesmo código: `api` (requisições HTTP) e `worker` (eventos e tarefas agendadas).
- Todo evento de domínio é gravado em uma tabela **`outbox`**, na **mesma transação** da alteração que o originou.
- O `worker` lê a outbox com `SELECT … FOR UPDATE SKIP LOCKED`, processa e marca o evento como concluído. Falhas são repetidas com backoff (RNF-07.2).
- Os consumidores são **idempotentes**: processar o mesmo evento duas vezes não muda o resultado.
- Não se adota *event sourcing*. O histórico exigido (RN-16, RNF-10.2, RNF-12) é garantido por tabelas append-only.

### Eventos iniciais

| Evento | Origem | Consumidores |
|---|---|---|
| `StatusCriterioRegistrado` | Consulta a fonte, alteração manual (RN-08) | Atualizar agregado do lote (ADR-003), alerta de CAR, notificação de lotes que deixaram de ser validados |
| `LoteMovimentado` | Cadastro, mistura, transformação, transferência, baixa | Gerar passaporte (ADR-005), atualizar agregado |
| `FonteConsultada` | Tarefa agendada | Monitorar saúde da fonte (RNF-17) |
| `DocumentoEnviado` | Upload | Auditoria |

## Alternativas consideradas

- **Pub/Sub desde o início:** escala melhor, mas sem transação comum com o banco exige outbox de qualquer forma, além de mais infraestrutura. Fica como evolução: o `worker` pode passar a encaminhar a outbox ao Pub/Sub.
- **Chamadas síncronas:** simples, mas acopla módulos e faz a requisição do usuário esperar por trabalho que não precisa ser imediato.

## Consequências

- Nenhum evento é perdido nem publicado sem a alteração correspondente (RNF-22).
- Dados derivados por eventos (agregados, notificações) têm consistência eventual. Por isso, decisões que não podem usar dado desatualizado (venda de lote, RN-19) não dependem deles (ver ADR-003).
- A outbox precisa de limpeza periódica e de métricas de fila (RNF-16.2).
