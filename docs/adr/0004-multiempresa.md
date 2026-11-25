# ADR-004: Multiempresa com schema e papel de banco por empresa

**Status:** Aceito
**Data:** 25/11/2026
**Decisores:** Pedro Henrique Carteli Rossetto

## Contexto

Uma mesma implantação atende várias empresas clientes. Os dados de uma empresa (fornecedores, lotes, status, documentos) não podem ser vistos por outra, inclusive em caso de erro de programação em uma consulta.

## Decisão

- **Um schema por empresa**, com as mesmas tabelas, e um **schema compartilhado** para empresas, usuários e dados de referência (ADR-006).
- **Um papel (role) de banco por empresa**, com acesso apenas ao seu schema e acesso somente leitura aos dados de referência compartilhados.
- A empresa é determinada **no servidor**, a partir do usuário autenticado; nunca a partir de parâmetro enviado pelo cliente (RNF-18).
- Em cada transação, a API executa `SET LOCAL ROLE` e `SET LOCAL search_path` para a empresa do usuário. `LOCAL` é desfeito automaticamente no commit ou rollback, de modo que uma conexão nunca volta ao pool com a empresa errada.
- **Um único pool de conexões** para todas as empresas. Com mais de algumas instâncias Cloud Run, adiciona-se um pooler de conexões, pois cada instância mantém várias conexões.
- **Migrações** em SQL puro, aplicadas por um executor próprio ao schema compartilhado e a cada schema de empresa.

## Limites conhecidos

- O papel de login da aplicação precisa ser membro de todos os papéis de empresa para poder alternar entre eles. A separação protege contra erros de `search_path` e consultas mal escritas, mas **não** contra um atacante capaz de executar SQL arbitrário pela aplicação.
- O servidor de tiles do mapa e as rotas de imagem de satélite precisam aplicar a mesma verificação de empresa; ficam fora da troca de papel feita pelas rotas da API e devem ser tratados explicitamente.

## Alternativas consideradas

| Opção | Isolamento | Complexidade | Custo |
|---|---|---|---|
| **Schema + papel por empresa (escolhida)** | Alto | Média (migração por schema) | Baixo |
| Coluna `empresa_id` + row-level security | Médio (depende das políticas) | Média | Baixo |
| Banco por empresa | Muito alto | Alta | Alto |

## Consequências

- Exportação, backup e remoção de uma empresa são simples (um schema).
- Cada migração é aplicada N vezes; o executor deve registrar a versão por schema e falhar de forma segura.
- O número de empresas suportado é de dezenas a centenas; acima disso, rever.
