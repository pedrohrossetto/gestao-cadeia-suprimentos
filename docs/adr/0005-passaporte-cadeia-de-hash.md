# ADR-005: Passaporte digital por etapa com cadeia de hash

**Status:** Aceito
**Data:** 25/11/2026
**Decisores:** Pedro Henrique Carteli Rossetto

## Contexto

RF-05.5 exige um passaporte digital por etapa do lote, exportável em PDF, JSON ou QR Code. RNF-11 exige que os passaportes tenham hash, de modo que qualquer alteração posterior seja detectável. Em uma mistura, o passaporte precisa cobrir todos os lotes de entrada. O passaporte é o registro que comprova a origem e a conformidade do lote em cada etapa, inclusive na venda.

## Decisão

- A cada etapa (cadastro, mistura, transformação, transferência, venda) é gerado um **passaporte congelado** para o lote resultante.
- O passaporte contém **os dados da própria etapa** e **os hashes dos passaportes de entrada**. Ele não copia o histórico anterior.
- O hash é calculado sobre **JSON canônico (RFC 8785)**. Alterar qualquer passaporte anterior quebra a cadeia de hashes dos seguintes.
- O **histórico completo** é reconstruído percorrendo os vínculos e gerado sob demanda, para exportação ou auditoria.
- O passaporte é uma **declaração pontual**: registra os status vigentes na data de criação e não muda quando o status de uma origem muda depois. O status atual é exibido à parte (ADR-003, RN-21).
- Uma etapa só pode consumir um lote cujo passaporte já foi gerado, garantindo que todo vínculo de entrada exista (RN-20).
- Alterações posteriores no perfil de validação não modificam passaportes já gerados (RN-14).

## Alternativas consideradas

- **Passaporte com o histórico completo embutido:** leitura simples, mas duplica dados a cada etapa e cresce rapidamente em cadeias de mistura.
- **Passaporte único por lote, atualizado:** não registra o que foi declarado em cada etapa e não permite detectar alteração.

## Consequências

- O armazenamento cresce linearmente com o número de etapas.
- A verificação de integridade é possível em qualquer ponto da cadeia.
- A geração do passaporte é disparada pelo evento `LoteMovimentado` (ADR-002); a etapa seguinte aguarda sua conclusão.
- Anonimização por LGPD (RS-03) gera novo hash, registrado na trilha de auditoria (RNF-11).
