# ADR-0001: Top 4 características arquiteturais (catálogo do livro)

- Status: Proposta
- Data: 2026-10-13
- Decisores: Arquiteto principal (a confirmar: Produto, Jurídico)
- Substitui: v1 (Segurança, Integridade, Confiabilidade, Elasticidade) e v2 (Segurança, Integridade, Confiabilidade, Conformidade)

## Contexto
O Veriditi vende registros de conteúdo web usados como evidência, com modelo de créditos. Foram avaliadas as 33 características do catálogo do livro (ver `docs/architecture.md`, seção 1). Apenas Segurança é NFR explícito; as demais são derivadas ou premissas. O catálogo não tem "Integridade": ela é tratada em Segurança. Revisão anterior mostrou que Elasticidade não tem requisito e que LGPD (Legalidade) foi omitida.

## Direcionadores
- Registro é evidência e irreproduzível (o site muda).
- Dados pessoais: CPF, OAB, faturamento, conteúdo de terceiros.
- Dinheiro envolvido (créditos, PIX, Mercado Pago).
- Captura longa e interativa, sujeita a falhas de rede e de worker.
- Equipe e escala v1 pequenas.

## Alternativas consideradas
1. Segurança, Integridade, Confiabilidade, Elasticidade (v1): Elasticidade sem requisito; Integridade fora do catálogo.
2. Segurança, Disponibilidade, Confiabilidade, Desempenho: tolera paradas curtas; Disponibilidade e Desempenho não moldam a estrutura.
3. **Segurança, Legalidade, Confiabilidade (Reliability/Safety), Robustez**.

## Decisão
Opção 3.
- Segurança (inclui Autenticação, Autorização e integridade da evidência).
- Legalidade (inclui Privacidade e Arquivamento).
- Confiabilidade (Reliability/Safety).
- Robustez.

Apoio: Disponibilidade, Continuidade, Recuperabilidade, Tolerância a falhas, Desempenho, Escalabilidade, Extensibilidade, Configurabilidade, Manutenibilidade, Atualizabilidade, Suportabilidade, Acessibilidade, Interoperabilidade, Usabilidade, Agilidade, Testabilidade, Implantabilidade, Autenticação, Autorização, Privacidade, Arquivamento. Elasticidade fica com gatilho de revisão. N/A: Instalabilidade, Alavancagem/Reuso, Customização (v1).

## Consequências
Positivas: critério claro para decisões; LGPD e Robustez entram no desenho; evita autoscaling prematuro.
Negativas: atrito de MFA; escrita mais lenta por hash e assinatura; complexidade de saga, reconciliação e retomada de sessão; gestão de chaves crítica.

Trade-offs aceitos:
- Segurança cede Usabilidade e Desempenho.
- Legalidade limita Portabilidade e logs/analytics.
- Confiabilidade custa Simplicidade e Agilidade.
- Robustez custa Desempenho e armazenamento.

Conflitos: imutabilidade x exclusão (ADR-0008); sessão longa x token curto (ADR-0010); Privacidade x Suportabilidade (suporte vê metadados, não conteúdo).

## Fitness functions associadas
IDOR; re-hash por amostragem; reconciliação do ledger; logs sem PII; matar worker na captura e retomar; queda de rede do cliente.

## Premissas a validar
Prazo de retenção, valor probatório formal, escala esperada, política de acesso da equipe interna.

## Revisão
Reavaliar se a concorrência exceder N sessões, se o jurídico exigir valor probatório formal ou se o SLA contratual exigir Disponibilidade alta.
