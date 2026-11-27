# ADR-006: Dados de referência compartilhados e versionados por data

**Status:** Aceito
**Data:** 25/11/2026
**Decisores:** Pedro Henrique Carteli Rossetto

## Contexto

Listas de embargo (Ibama, ICMBio), a lista suja do trabalho análogo à escravidão e os dados do SICAR são públicos e iguais para todas as empresas. Um passaporte precisa provar o que a lista dizia em uma data (RNF-12). No estágio Demo, essas integrações são simuladas.

## Decisão

Formalizada como RN-22 nos requisitos v3.

- Os dados de referência ficam no **schema compartilhado** e são consultados **uma única vez** por execução, para todas as empresas.
- Cada registro é **versionado por data**, com início e fim de vigência, além de fonte, data de obtenção e versão (RNF-12).
- Os **resultados por empresa** (quais origens daquela empresa coincidiram com a lista) ficam no schema da empresa.
- A tarefa diária roda uma vez sobre os dados compartilhados e emite **um evento por empresa afetada** pela outbox (ADR-002).
- Uma **lista de bloqueio própria do cliente** não é dado de referência: fica no schema daquela empresa.
- Os papéis de empresa têm acesso **somente leitura** ao schema compartilhado (ADR-004).
- Cada fonte externa fica atrás de um **adaptador** com a mesma interface. No Demo, o adaptador devolve dados simulados; no Piloto, consulta a fonte real.

## Consequências

- Uma única consulta por fonte reduz custo e carga sobre APIs públicas.
- A consulta em data passada (RF-07.4) e a reprodução de análises usam a vigência dos registros.
- O volume de histórico cresce com o tempo; regras de retenção devem ser definidas.
