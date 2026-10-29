# Requisitos: O que o sistema deve fazer.


## Requisitos Funcionais


- RF-01: Cadastro de fornecedores e propriedades (um fornecedor pode ter várias propriedades) com arquivos de geolocalização como .shp, .geojson, .kml; Os fornecedores e propriedades devem ser cadastrados com informações básicas e geolocalizadas, e enriquecidos com dados de embargos e conformidade, como por exemplo a lista de embargos do Ibama, ICMBio e a lista suja do trabalho análogo à escravidão.
- RF-02: Cadastro de Lotes, com modelagem via balanço de massa e Grafo Acíclico Direcionado, armazenando informações como tonelagem, critérios de qualidade e conformidade ESG, para relacionar fornecedores e lotes, criando um passaporte digital, até seu destino final onde será dado baixa. 
- RF-03: Deve ser possível validar a base de fornecedores e seus lotes, assim como ponderar onde passaram e as misturas que ocorreram, verificando a situação de conformidade com critérios definidos pelo usuário.
- RF-04: O usuário deve conseguir visualizar imagens de satélite sobre seus fornecedores, para validar a análise de conformidade, como por exemplo com alertas de desmatamento, acessando imagens de diferentes satélites via API.
- RF-05: Os fornecedores, propriedades e lotes em trânsito devem ser visualizados em um mapa interativo, com possibilidade de filtragem e busca por localização.
- RF-07: As propriedades deverão conter informações atualizadas sobre conformidade, situação do CAR, e outras informações relevantes.
- RF-08: O sistema deve consultar automaticamente, via API ou base baixada, as listas de embargos do Ibama, ICMBio e a lista suja do trabalho análogo à escravidão.
- RF-09: O sistema deve emitir automaticamente a Declaração de Devida Diligência (DDS), se possível diretamente para o sistema TRACES da União Europeia, senão para o sistema do comprador e salvar uma cópia local e ancorada criptograficamente em blockchain.
- RF-10: O sistema deve processar relações de parentesco ou grupos econômicos (vários CNPJs/CPFs conectados ao mesmo produtor) para unificar o risco reputacional do grupo.
- RF-11: O sistema deve calcular o Score de Risco ESG para cada nível de granularidade (Região, Área, Produtor, Propriedade e Lote) com base em pesos parametrizáveis.


## Requisitos Não Funcionais

- RNF-01 (RF-02): Os lotes devem ser acompanhados por informações o mais automatizadas possível, como por API de um ERP, ou sistemas de telemetria da operação.

## Regras de Negócio

- RN-01: O sistema deve impedir dupla contagem de grãos, evitando que o mesmo lote seja contabilizado mais de uma vez para fins de recebimento duplo de crédito ou similares.
- RN-02: O sistema deve impedir fraudes de lavagem de grãos, evitando que áreas inconformes passem seus lotes para áreas conformes.
