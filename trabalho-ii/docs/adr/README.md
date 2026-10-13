# Índice de ADRs do Veriditi

Template: MADR adaptado (Status, Data, Decisores, Contexto, Alternativas, Decisão, Consequências, Fitness functions, Premissas/Revisão).

| ADR | Título | Status |
|---|---|---|
| [0001](0001-top-4-caracteristicas.md) | Top 4 características (Segurança, Legalidade, Confiabilidade, Robustez) | Proposta |
| [0002](0002-estilo-monolito-modular-worker-isolado.md) | Monólito modular com Capture Worker isolado | Proposta |
| [0003](0003-estrategia-de-captura.md) | Estratégia de captura | Proposta (validar com Produto) |
| [0004](0004-isolamento-do-worker-e-ssrf.md) | Isolamento do worker e SSRF | Proposta |
| [0005](0005-creditos-ledger-e-saga.md) | Ledger de créditos e saga de reserva | Proposta |
| [0006](0006-evidencia-imutavel-e-manifest.md) | Evidence Vault imutável e manifest | Proposta |
| [0007](0007-carimbo-do-tempo-e-validade-probatoria.md) | Carimbo do tempo e validade probatória | Proposta (validar com Jurídico) |
| [0008](0008-lgpd-retencao-e-exclusao.md) | LGPD, retenção e exclusão | Proposta (validar com Jurídico) |
| [0009](0009-gestao-de-chaves-e-segredos.md) | Gestão de chaves e segredos | Proposta |
| [0010](0010-autenticacao-e-autorizacao.md) | Autenticação, sessão e autorização | Proposta |
| [0011](0011-perfis-de-usuario-e-tenant.md) | Perfis de usuário (D1) e organização | Proposta |
| [0012](0012-pacotes-de-creditos-como-dados.md) | Pacotes de créditos como dados (D2) | Proposta |
| [0013](0013-observabilidade-e-auditoria.md) | Observabilidade e auditoria | Proposta |
| [0014](0014-deploy-cloud-e-disponibilidade.md) | Deploy, cloud e disponibilidade | Proposta |
| [0015](0015-mensageria-outbox-e-notificacoes.md) | Mensageria, outbox e notificações | Proposta |
| [0016](0016-robustez-da-sessao-de-captura.md) | Robustez da sessão de captura | Proposta |

Dependências principais: 0001 -> todos; 0003 -> 0004; 0006 -> 0007, 0008, 0009; 0005 -> 0012, 0015; 0003 -> 0016 -> 0010.
