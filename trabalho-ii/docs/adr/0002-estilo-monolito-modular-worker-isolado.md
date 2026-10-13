# ADR-0002: Monólito modular com Capture Worker isolado

- Status: Proposta
- Data: 2026-10-13
- Decisores: Arquiteto principal

## Contexto
Domínio pequeno (~15 REQs), equipe pequena, necessidade de consistência forte em créditos. Existe uma parte com perfil radicalmente distinto: execução de navegador sobre conteúdo não confiável.

## Direcionadores
Simplicidade operacional, ACID em créditos, isolamento de segurança, custo.

## Alternativas consideradas
1. Microserviços: rejeitado. Custo distribuído (rede, transações, operação) sem necessidade de deploy independente por feature.
2. Monólito em camadas: rejeitado. Acoplamento horizontal e risco de big ball of mud.
3. Event-driven puro: rejeitado. Créditos exigem consistência forte, não eventual.
4. Monólito modular + worker separado: escolhido.

## Decisão
Núcleo em **monólito modular** organizado por domínio (Identity, Privacy, Billing, Gateway, Notification, Capture Session, Registry, Reporting, Audit, Abuse), com eventos internos via outbox e fila. **Capture Worker** como serviço separado.

Architecture quanta:
- Núcleo transacional (consistência forte, carga estável).
- Capture Worker (alto risco, recursos variáveis, efêmero).

A separação do worker se justifica por **isolamento de segurança e perfil de recursos**, não por elasticidade.

## Consequências
Positivas: um deploy principal, transações locais, evolução rápida, opção de extrair módulos depois.
Negativas: exige disciplina de fronteiras; falha no núcleo afeta todos os módulos; dois pipelines de deploy.

## Fitness functions associadas
Teste de arquitetura: módulo não acessa tabelas/classes internas de outro; worker não depende de código do núcleo.

## Revisão
Reavaliar extração de Billing ou Reporting se times ou carga divergirem.
