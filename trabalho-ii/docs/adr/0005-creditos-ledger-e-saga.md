# ADR-0005: Créditos em ledger append-only com saga de reserva

- Status: Proposta
- Data: 2026-10-13
- Decisores: Arquiteto principal, Produto/Financeiro

## Contexto
Créditos são comprados (REQ05-06) e consumidos por registro (REQ08+). A v1 debitava após persistência, o que permite iniciar captura sem saldo. Pagamento é externo e assíncrono (Mercado Pago, PIX).

## Alternativas consideradas
1. Campo de saldo mutável: rejeitado (sem auditoria, difícil reconciliar).
2. Ledger append-only, débito após persistência: permite uso sem saldo.
3. Ledger append-only + reserva, confirmação e estorno: escolhido.

## Decisão
- Saldo derivado de lançamentos imutáveis (compra, reserva, confirmação, estorno, ajuste), todos com chave de idempotência.
- Saga de captura, dona: Capture Session; contabilidade, dono: Billing.
  1. Reservar crédito (falha se sem saldo).
  2. Iniciar captura.
  3. Persistir artefatos e finalizar.
  4. Confirmar débito.
  5. Em falha ou timeout: estornar reserva.
- Reservas expiram por timeout configurável.
- Pagamento: webhook assinado e idempotente; reconciliação periódica; tratamento de PIX expirado e estornos. Dados de cartão só no Mercado Pago.
- Compra referencia a versão do pacote (ADR-0012).

## Consequências
Positivas: auditável; sem saldo negativo; recuperação de falhas parciais.
Negativas: estados intermediários na UX ("pagamento em processamento"); mais código de compensação.

## Fitness functions associadas
Webhook repetido N vezes gera 1 lançamento; soma do ledger = saldo; pagamentos aprovados = créditos concedidos; reservas vencidas são estornadas.

## Revisão
Se surgirem planos de assinatura ou empresa (D1), revisar o modelo de contas.
