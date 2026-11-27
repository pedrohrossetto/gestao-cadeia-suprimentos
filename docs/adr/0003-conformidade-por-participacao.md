# ADR-003: Conformidade do lote por participação de origem × status

**Status:** Aceito
**Data:** 25/11/2026
**Decisores:** Pedro Henrique Carteli Rossetto

## Contexto

O status de conformidade de um lote é, para cada critério, a distribuição da sua massa entre conforme, inconforme e não definido, calculada por balanço de massa (RF-07.2, RN-05). Ela muda quando o status de uma origem muda (RF-07.3), precisa ser consultável em data passada (RF-07.4) e deve atender 10 s por avaliação (RNF-03) com até 5 milhões de lotes (RNF-04).

Duas coisas mudam em momentos diferentes:

- **A participação de cada origem na massa do lote** muda só quando o lote é movimentado (mistura, transformação, transferência, baixa).
- **O status de uma origem** (embargo, SICAR, lista suja) muda sem que o lote seja movimentado. Ele não altera as participações, apenas como cada uma é classificada.

## Decisão

- Cada lote guarda suas **participações por origem final**: uma linha por (lote, propriedade, fornecedor) com a massa em kg. Participações de lotes intermediários são achatadas até as propriedades e fornecedores de origem, para que a leitura não percorra a cadeia de misturas.
- As participações são recalculadas **na mesma transação** da movimentação. É um cálculo local: as participações das entradas, ponderadas pelas massas e pelo rendimento (RN-02).
- O status fica **uma única vez na origem** (propriedade ou fornecedor, conforme o nível de aplicação, RN-17). Status nunca é copiado para os lotes.
- A conformidade do lote é **derivada por junção**: participações × status vigente de cada origem, por critério. Não definido permanece separado e não aplicável fica fora (RN-18).
- **Consulta em data passada** usa a mesma junção, com o status vigente naquela data. O histórico append-only (RN-16) torna isso possível sem armazenamento extra.
- Para listas e painéis com milhões de lotes, mantém-se um **agregado em cache** atualizado pela outbox (ADR-002). O cache pode estar desatualizado e é sinalizado como tal na interface.
- **A venda não usa o cache (RN-19).** Cada origem tem um **contador de versão**, incrementado a cada mudança de status em qualquer critério. Cada linha de participação guarda a versão da origem no momento do cálculo (RF-05.6). Na venda, compara-se essa versão com a atual; divergência indica dado desatualizado, e a junção é recalculada antes de prosseguir. O custo é proporcional às origens daquele lote apenas.

## Alternativas consideradas

| Opção | Leitura | Escrita | Escala (RNF-04) |
|---|---|---|---|
| **Participação × status (escolhida)** | Junção pelas origens do lote | Local, na movimentação | Boa: nenhuma propagação para corrigir status |
| Calcular na leitura (CTE recursiva) | Percorre o grafo a cada consulta | Nenhuma | Ruim com cadeias longas |
| Materializar distribuição e propagar | Imediata | Propaga a todos os lotes a jusante a cada mudança de status | Bloqueia muitas linhas; latência alta |

## Consequências

- A mudança de status de uma origem custa um único registro; nenhum lote precisa ser reescrito para ficar correto.
- Lotes com muitas origens (milhares de propriedades após várias misturas) acumulam muitas linhas de participação. Se isso pesar, linhas da mesma origem podem ser consolidadas.
- A notificação de lotes que deixaram de ser validados (RF-07.3) ainda precisa alcançar todos os lotes afetados, mas é assíncrona e não afeta a correção.
- Massa armazenada em quilogramas inteiros (`bigint`) evita erro de arredondamento no balanço (RN-01, RN-02, RNF-19).
