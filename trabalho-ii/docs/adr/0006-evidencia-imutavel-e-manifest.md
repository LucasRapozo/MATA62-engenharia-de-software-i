# ADR-0006: Evidence Vault imutável com hash e manifest assinado

- Status: Proposta
- Data: 2026-10-13
- Decisores: Arquiteto principal, Jurídico

## Contexto
O valor do produto é o registro (REQ13-15). Alterações posteriores destroem esse valor. Há conflito potencial com LGPD (ADR-0008).

## Alternativas consideradas
1. Armazenamento comum em banco/arquivo: rejeitado.
2. Object storage com versionamento e bloqueio lógico de escrita: escolhido para v1.
3. Object lock em modo compliance: prematuro; impede exclusão até o prazo.
4. Blockchain/ledger de terceiros: excesso para o v1.

## Decisão
- Object storage, bucket único por ambiente, versionamento ligado, política que proíbe sobrescrita e exclusão manual; object lock em modo governança (exclusão só por processo de retenção).
- SHA-256 por chunk e por artefato; manifest com lista de artefatos, hashes, metadados (ID, usuário, início/fim, duração, URL, tamanhos), relatório e ZIP.
- Manifest assinado via KMS/HSM (ADR-0009) e carimbado (ADR-0007).
- IDs de registro gerados no servidor.
- Relatório (REQ14) e ZIP (REQ15) são regenerados a partir do Vault e incluídos no manifest; cada download é auditado.
- Criptografia em repouso por envelope encryption (ADR-0009).

## Consequências
Positivas: evidência verificável e defensável.
Negativas: armazenamento cresce de forma monotônica; política de retenção obrigatória; finalização mais lenta.

## Fitness functions associadas
Re-hash por amostragem vs. manifest; tentativa de sobrescrita deve falhar; hash do ZIP confere com o manifest.

## Revisão
Migrar para compliance se o jurídico exigir imutabilidade formal.
