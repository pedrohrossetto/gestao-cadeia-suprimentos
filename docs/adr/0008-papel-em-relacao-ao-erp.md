# ADR-008: Papel do sistema em relação ao ERP

**Status:** Aceito
**Data:** 25/11/2026
**Decisores:** Pedro Henrique Carteli Rossetto

## Contexto

Armazéns, tradings e indústrias costumam já usar um ERP para cadastro de parceiros, recebimento com balança, estoque por armazém, ordens de produção com rendimento, faturamento e emissão de NF-e. Comparando com os requisitos da v3, cerca de um terço dos requisitos funcionais (recebimento, armazenagem, transformação, venda, documentos, usuários) já é atendido por um ERP.

O que o ERP não atende é justamente o núcleo do sistema:

- conformidade por critério nas origens, com histórico que nunca é sobrescrito (RF-07.1, RF-08, RN-16, RN-17);
- distribuição de massa por status, rastreando quantos kg de cada propriedade estão em cada lote depois de quebras, misturas em silo e transformações (RF-05.6, RF-07.2, RN-05). A genealogia de lote do ERP diz quais lotes entraram, mas não quanto de cada origem, e o estoque de silo comum costuma ser controlado por custo médio ou FIFO;
- consulta diária a fontes externas, com vigência por data (RF-03, RNF-12, RN-22);
- dados geoespaciais: polígonos, sobreposição, imagens de satélite, mapa (RF-01.2, RF-02, RF-09, RF-10);
- perfil de validação, reavaliação automática, consulta em data passada e passaporte com cadeia de hash (RF-06, RF-07.3, RF-07.4, RF-05.5, RNF-11).

Até aqui, os casos de uso supunham que o Operador registra no sistema todas as movimentações. Para quem já tem ERP, isso é dupla digitação, o principal obstáculo à adoção.

## Decisão

- O sistema **complementa** o ERP, não o substitui.
- **O ERP é a fonte do dado operacional e fiscal**: massas, estoque, balança, produção e documento fiscal.
- **O sistema é a fonte do dado de origem e conformidade**: propriedades e geometrias, critérios e status, participações de origem, validação e passaportes (RN-25).
- O ERP envia ao sistema recebimentos, transformações, transferências e vendas, por **API REST ou arquivo CSV** (RF-15.2, RNF-13, RNF-14), depois de mapear os identificadores (RF-15.1).
- Movimentações importadas passam pelas mesmas regras das registradas no sistema; se alguma regra falhar, a movimentação fica **pendente**, sem aplicação parcial (RN-24). A importação é **idempotente**, pela chave da movimentação no ERP (RNF-23).
- Uma **conciliação de massas** compara periodicamente os saldos de lotes e unidades com os do ERP (RF-15.3).
- O **registro manual** das movimentações (UC07, UC35, UC36, UC22) continua disponível para empresas sem ERP e para a demonstração.
- A integração entra no estágio **Pós-piloto**. O piloto usa registro manual.

## Alternativas consideradas

| Opção | Prós | Contras |
|---|---|---|
| **Complementar o ERP, com importação (escolhida)** | Sem dupla digitação; cada sistema faz o que faz melhor; adoção mais fácil | Exige mapeamento de identificadores e conciliação |
| Substituir o ERP nas funções operacionais | Um sistema só para o usuário | Compete com produtos maduros (fiscal, custos, produção) fora do foco do projeto |
| Construir a solução como extensão dentro de um ERP | Sem integração | Preso a um fornecedor; o ERP não tem suporte geoespacial nem multiempresa com dados de referência compartilhados |
| Integração bidirecional (o sistema também escreve no ERP) | Bloqueios de conformidade refletidos no ERP | Muito mais complexa; fica como evolução futura |

## Consequências

- O valor do sistema fica concentrado no que o ERP não faz, e o esforço de desenvolvimento também.
- Cada cliente precisa de um mapeamento inicial de cadastros e de um conector (API ou CSV). Os conectores seguem o padrão de adaptadores das fontes externas (RNF-20).
- Pode haver divergência temporária entre os saldos do ERP e do sistema; a conciliação torna isso visível.
- Correções de massa em lotes integrados são feitas no ERP e reimportadas, nunca editadas no sistema.
- A venda continua exigindo verificação de versão das origens (RN-19) também quando é importada, o que pode deixar uma venda do ERP pendente até a revalidação.
