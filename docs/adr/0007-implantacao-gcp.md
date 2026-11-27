# ADR-007: Implantação no Google Cloud

**Status:** Aceito
**Data:** 25/11/2026
**Decisores:** Pedro Henrique Carteli Rossetto

## Contexto

O sistema precisa de PostgreSQL com PostGIS, armazenamento de documentos com versões (RF-12.3), tarefas agendadas (RF-03.1), backup diário (RNF-06) e baixo custo operacional para um desenvolvedor. Dados pessoais estão sujeitos à LGPD.

## Decisão

| Necessidade | Serviço |
|---|---|
| Processos `api` e `worker` (ADR-002) | Cloud Run |
| Banco PostgreSQL + PostGIS | Cloud SQL |
| Documentos, arquivos importados, PDFs e passaportes exportados | Cloud Storage, com versionamento de objetos; envio e download por URLs assinadas |
| Consultas diárias às fontes | Cloud Scheduler disparando o `worker` |
| Segredos | Secret Manager |
| SPA | Cloud Storage + Cloud CDN (ou Cloud Run) |

- Região **`southamerica-east1`** (São Paulo), mantendo os dados no Brasil (RS-04).
- Backup automático diário do Cloud SQL com retenção de 30 dias (RNF-06).
- Os arquivos não passam pela API: o cliente envia e baixa diretamente do Cloud Storage com URLs assinadas, e a API registra os metadados.

## Alternativas consideradas

- **GKE:** mais controle, operação muito maior.
- **VMs (Compute Engine):** barato, mas exige manutenção manual de sistema operacional, banco e backup.

## Consequências

- Custo proporcional ao uso e pouca operação.
- O Cloud Run escala instâncias automaticamente; com várias instâncias, é necessário um pooler de conexões (ADR-004).
- O `worker` em Cloud Run precisa de configuração para tarefas longas (recálculos e importações grandes).
