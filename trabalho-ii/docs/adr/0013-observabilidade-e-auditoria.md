# ADR-0013: Observabilidade e trilha de auditoria

- Status: Proposta
- Data: 2026-10-13
- Decisores: Arquiteto principal, Operações

## Contexto
Falhas de pagamento e captura precisam ser diagnosticadas rapidamente, sem expor dados pessoais. A trilha de auditoria sustenta a evidência.

## Alternativas consideradas
1. Logs de aplicação apenas: insuficiente.
2. Logs estruturados + métricas + traces (OpenTelemetry) + audit log separado: escolhido.

## Decisão
- OpenTelemetry para traces e métricas; logs estruturados com trace id e sem PII (mascaramento de CPF, e-mail e URLs com tokens).
- **Audit log** separado, append-only, com eventos de negócio (login, compra, captura, download, exclusão) e acesso administrativo; retenção própria.
- Métricas-chave: tempo de início de captura, taxa de falha de sessão, reservas expiradas, webhooks pendentes, divergência de reconciliação, custo por sessão.
- Alertas por SLO.

## Consequências
Positivas: diagnóstico rápido; base para SLO e custo.
Negativas: custo de armazenamento; disciplina de mascaramento.

## Fitness functions associadas
Scan de PII em logs no CI; teste de que eventos críticos geram registro de auditoria.
