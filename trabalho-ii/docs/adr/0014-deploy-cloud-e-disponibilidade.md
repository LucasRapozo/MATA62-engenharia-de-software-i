# ADR-0014: Deploy, cloud, disponibilidade e capacidade

- Status: Proposta (SLO, região e provedor a validar)
- Data: 2026-10-13
- Decisores: Arquiteto principal, Operações, Jurídico

## Contexto
A v1 priorizava elasticidade sem requisito de pico. O risco real é indisponibilidade no momento do fato e custo de sessões de navegador. LGPD sugere região no Brasil.

## Alternativas consideradas
1. Autoscaling agressivo com scale-to-zero: complexidade sem demanda comprovada.
2. Pool de workers com limite fixo, fila e pool aquecido, autoscaling simples por tamanho da fila: escolhido.
3. Infraestrutura própria: sem justificativa.

## Decisão
- Provedor de nuvem com região no Brasil (a confirmar); IaC e CI/CD com ambientes separados.
- Núcleo em multi-AZ; banco gerenciado com réplica e backups; RPO <= 5 min para metadados; artefatos confirmados com durabilidade do object storage; RTO <= 4 h; teste de restauração periódico.
- Workers: pool com limite máximo por concorrência (controle de custo), pool aquecido pequeno, autoscaling por fila com teto; quotas por usuário.
- SLO inicial proposto: 99,5% mensal para iniciar captura.
- Gatilho para elasticidade completa: concorrência sustentada > N sessões ou custo por sessão acima do alvo.

## Consequências
Positivas: custo previsível; complexidade proporcional à escala real.
Negativas: picos acima do teto enfileiram usuários; requer calibragem de N e do teto.

## Fitness functions associadas
Teste de restauração de backup; teste de failover de AZ; início de captura P95 dentro do SLO.
