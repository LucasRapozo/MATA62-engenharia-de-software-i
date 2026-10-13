# ADR-0015: Mensageria, outbox e notificações

- Status: Proposta
- Data: 2026-10-13
- Decisores: Arquiteto principal

## Contexto
E-mails de confirmação (REQ07, REQ10, REQ11b), webhooks de pagamento e geração de relatórios precisam de entrega confiável sem acoplar o fluxo principal.

## Alternativas consideradas
1. Chamadas síncronas ao provedor de e-mail: falha propaga ao usuário.
2. Broker + publicação direta após commit: risco de perda ou duplicação.
3. **Transactional outbox + fila durável com DLQ**: escolhido.

## Decisão
- Eventos gravados na mesma transação do negócio (outbox) e publicados por relay para a fila durável.
- Consumidores idempotentes; retry com backoff; DLQ monitorada; ordenação garantida apenas por chave de agregado quando necessário.
- Notification usa provedor transacional de e-mail, com templates, tratamento de bounce e deduplicação.
- Tecnologia da fila: gerenciada pelo provedor de nuvem (decisão final no ADR-0014).

## Consequências
Positivas: nenhuma perda de evento; falha de e-mail não afeta o fluxo.
Negativas: entrega pelo menos uma vez (exige idempotência); operação de DLQ.

## Fitness functions associadas
Falha simulada do provedor de e-mail não perde evento; reprocessamento não duplica e-mail.
