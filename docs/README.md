# Documentação

Cada versão da especificação fica em sua própria pasta, com os requisitos e os diagramas derivados deles. A versão vigente é a **v3**.

```
docs/
├── README.md                 este índice
├── modelo-requisitos.docx    modelo do professor (base dos .docx de todas as versões)
├── adr/                      decisões de arquitetura ADR-001 a ADR-008 (valem a partir da v3)
├── v1/
│   ├── requisitos.{md,docx}
│   └── diagrams/             casos de uso (Mermaid + PlantUML) da v1
├── v2/
│   ├── requisitos.{md,yaml,docx}
│   └── diagrams/plantuml/    classes conceitual e de domínio
└── v3/                       ← vigente
    ├── requisitos.{md,yaml,docx}
    └── casos-de-uso/         especificações (UC07, UC17, UC22, UC35, UC36) e cenário do ciclo de vida
        └── plantuml/         diagramas de casos de uso e de atividades (.puml + .png)
```

## Versões

| Versão | Data | Conteúdo | Observações |
|---|---|---|---|
| [v1](v1/) | 06/11/2026 | Requisitos, casos de uso | Primeira versão, com prioridade MoSCoW. Sem YAML estruturado. |
| [v2](v2/) | 19/11/2026 | Requisitos, diagramas de classes | Requisitos simplificados, divididos em estágios Demo, Piloto e Pós-piloto. |
| [v3](v3/) | 25/11/2026 | Requisitos, especificações de casos de uso e cenário | Incorpora as decisões de [`adr/`](adr/). Alterações em relação à v2 no Apêndice C de [`v3/requisitos.md`](v3/requisitos.md). |

## Convenções

- Dentro de cada pasta, os arquivos não levam sufixo de versão: a versão é dada pela pasta.
- A partir da v2, `requisitos.yaml` é a fonte estruturada de cada versão; `requisitos.md` e `requisitos.docx` são gerados a partir dela.
- Os PNGs do PlantUML são gerados na própria pasta do `.puml`, por exemplo `plantuml -tpng docs/v3/casos-de-uso/plantuml/*.puml`.
- Uma nova versão começa como cópia da pasta anterior; versões antigas não são editadas.
