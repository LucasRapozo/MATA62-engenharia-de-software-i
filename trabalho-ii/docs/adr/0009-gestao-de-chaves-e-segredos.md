# ADR-0009: Gestão de chaves e segredos

- Status: Proposta
- Data: 2026-10-13
- Decisores: Arquiteto principal, Segurança

## Contexto
A v1 sugeria chave por registro, o que complica relatório, ZIP, recuperação e é provável over-engineering. Há também chaves de assinatura e segredos de integração.

## Alternativas consideradas
1. Chave única mestre: perda ou vazamento compromete tudo; impede crypto-shredding granular.
2. Chave por registro: granularidade máxima, alto custo operacional.
3. **Envelope encryption com KMS, chave (KEK) por tenant/usuário**: escolhido.

## Decisão
- KMS gerenciado na região do Brasil; KEK por usuário (e por organização quando existir); DEK por registro cifrada pela KEK.
- Chave de assinatura do manifest separada, em KMS/HSM, com rotação e política de revogação.
- Segredos (Mercado Pago, e-mail, banco) em vault; nenhum segredo no Capture Worker.
- Backup das chaves com controle dividido e teste de recuperação; acesso por privilégio mínimo e auditado.
- Rotação periódica de KEK com re-encriptação de DEK.

## Consequências
Positivas: crypto-shredding por titular; menor complexidade que chave por registro.
Negativas: dependência do KMS; perda de KEK é perda irreversível; rotação exige processo.

## Fitness functions associadas
Teste de recuperação de chave em ambiente de teste; verificação de que o worker não possui segredos; scan de segredos no repositório.
