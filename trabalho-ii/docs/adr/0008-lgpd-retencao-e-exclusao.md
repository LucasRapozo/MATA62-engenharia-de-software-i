# ADR-0008: LGPD, retenção e exclusão (crypto-shredding)

- Status: Proposta (prazos a validar com Jurídico)
- Data: 2026-10-13
- Decisores: Jurídico/DPO, Arquiteto principal

## Contexto
O sistema trata CPF, OAB, dados de faturamento (endereço, telefone) e conteúdo capturado, que pode incluir dados de terceiros. O Vault imutável (ADR-0006) conflita com o direito de exclusão.

## Alternativas consideradas
1. Reter para sempre: viola minimização e exclusão.
2. Exclusão física sob demanda (quebra imutabilidade e manifest): rejeitado.
3. Retenção com prazo definido + exclusão por **crypto-shredding** (destruir a chave): escolhido.

## Decisão
- Mapear dados pessoais, finalidade e base legal por dado (execução de contrato, obrigação legal, legítimo interesse).
- Política de retenção com prazo mínimo e máximo por tipo de dado (evidência, faturamento, logs); valores definidos pelo jurídico.
- Evidência criptografada com chave por tenant/usuário (ADR-0009); exclusão ao fim da retenção ou a pedido, quando permitido, por destruição da chave; manifest preserva hashes sem dados pessoais em claro.
- Dados fiscais/faturamento retidos pelo prazo legal.
- Região de dados no Brasil; logs e métricas sem PII.
- Canal do titular (acesso, correção, exclusão, portabilidade); RIPD antes do go-live.
- Aviso ao usuário sobre responsabilidade pelo conteúdo capturado e dados de terceiros.

## Consequências
Positivas: conciliar imutabilidade e exclusão; reduz risco regulatório.
Negativas: gestão de chaves crítica; perda da chave = perda irrecuperável; backup precisa respeitar a política.

## Fitness functions associadas
Teste de exclusão ponta a ponta (após shredding o artefato é ilegível); varredura de PII em logs; relatório de retenção vencida.

## Premissas a validar
Prazos de retenção, bases legais, papel de controlador/operador por tipo de usuário (advogado, comum, futura empresa).
