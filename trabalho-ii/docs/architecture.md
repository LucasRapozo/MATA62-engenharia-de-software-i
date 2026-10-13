# Arquitetura do Veriditi (v3, catálogo de características do livro)

Referência: *Fundamentals of Software Architecture* (Richards & Ford).
Fonte dos requisitos: Veridit - Documento de Requisitos v01.00.
Decisões detalhadas: [docs/adr/](adr/README.md).

Origem: **Explícita** (consta no documento), **Derivada** (decorre de REQ/D), **Premissa** (hipótese a validar).
Papel: **Driving** (guia a arquitetura, Top 4), **Supporting** (atendida por táticas e fitness functions, sem moldar o estilo), **Baixa** (registrada, sem custo no v1), **N/A** (não se aplica).

Nota: o catálogo não tem "Integridade" como item. Integridade de dados é tratada dentro de **Segurança** (e sustentada por Confiabilidade e Arquivamento).

---

## 1. Catálogo completo avaliado (33 características)

### 1.1 Operacionais

| Característica | Papel | Origem | Justificativa e meta para o Veriditi |
|---|---|---|---|
| **Disponibilidade** | Supporting | Derivada (REQ08) | Captura ocorre no momento do fato. SLO proposto 99,5% mensal para iniciar captura; janelas de manutenção fora do horário comercial. Não é 24/7 de missão crítica. |
| **Continuidade** | Supporting | Premissa | Backups de metadados e artefatos replicados para segunda região do Brasil; plano de DR testado semestralmente. |
| **Desempenho** | Supporting | Premissa | Início de captura P95 < 15 s; relatório P95 < 30 s; ZIP até tamanho máximo definido. Teste de estresse do pool de workers antes do go-live; análise de pico no lançamento. |
| **Recuperabilidade** | Supporting | Premissa | RPO <= 5 min (metadados), RPO = 0 para artefato confirmado; RTO <= 4 h. Restore testado. |
| **Confiabilidade/Segurança (Reliability/Safety)** | **Driving** | Derivada (REQ05-11) | Sem risco à vida, mas falha custa dinheiro (cobrança sem crédito, estorno) e perde evidência irreproduzível (o site muda). Exige consistência de créditos e persistência durável da captura. |
| **Robustez** | **Driving** | Derivada (REQ08-09) | Captura é longa e interativa: queda de internet do usuário, falha de worker, site lento ou fora do ar, timeout do gateway não podem destruir a sessão nem o saldo. Sessão retomável; fim de sessão por abandono tratado com finalização segura. |
| **Escalabilidade** | Supporting | Premissa | Núcleo sem estado e escalável horizontalmente; banco com réplica de leitura. Crescimento esperado moderado no v1. |
| **Elasticidade** | Baixa (gatilho) | Premissa | Nenhum requisito de pico. Pool de workers com teto e autoscaling simples por fila. Gatilho: concorrência sustentada > N. |
| **Tolerância a falhas** | Supporting | Derivada | Núcleo multi-AZ, worker falha e é substituído, gateway e e-mail com retry, fila durável com DLQ. |
| **Arquivamento** | Supporting | Premissa + Legal | Retenção com prazo mínimo/máximo, arquivamento para storage frio e exclusão por crypto-shredding. |
| **Suportabilidade** | Supporting | Premissa | Trace id por sessão, logs sem PII, ferramenta de suporte que mostra status sem expor conteúdo. |

### 1.2 Estruturais

| Característica | Papel | Origem | Justificativa e meta |
|---|---|---|---|
| **Configurabilidade** | Supporting | Derivada (D2) | Pacotes, quotas, limites de sessão e prazo de reserva configuráveis por administrador, sem deploy. Usuário final não configura o sistema. |
| **Extensibilidade** | Supporting | Derivada (D1) | Perfil "empresa", novos pacotes, novos meios de pagamento, novos tipos de captura. Atendida pela modularização e interfaces (ADR-0011, ADR-0012). |
| **Instalabilidade** | N/A | - | SaaS web. Só reconsiderar se a alternativa de extensão no cliente (ADR-0003) for adotada. |
| **Alavancagem/Reuso** | N/A | - | Produto único; sem família de produtos. |
| **Localização** | Baixa | Premissa | pt-BR, BRL, fuso de Brasília; textos externalizados para permitir novos idiomas. Sem requisito de múltiplas moedas. |
| **Manutenibilidade** | Supporting | Premissa | Monólito modular com fronteiras verificadas por fitness functions. |
| **Portabilidade** | Baixa | Premissa | Containers e abstração de object storage/KMS para reduzir lock-in; sem requisito de multi-cloud. |
| **Atualizabilidade** | Supporting | Premissa | Imagem do worker (navegador) precisa de atualização frequente por segurança; migração de banco sem downtime. |
| **Acessibilidade** | Supporting | Premissa | WCAG 2.1 AA na interface; atende Lei Brasileira de Inclusão. Atenção ao streaming de captura. |

### 1.3 Segurança e conformidade (transversais)

| Característica | Papel | Origem | Justificativa e meta |
|---|---|---|---|
| **Segurança** | **Driving** | Explícita (NFR) | Acesso só a cadastrados, criptografia em repouso e em trânsito, integridade da evidência (hash, manifest assinado, imutabilidade), isolamento do worker contra SSRF e exploits. |
| **Autenticação** | Supporting | Explícita (REQ01-04) | Senha Argon2id, MFA, sessão revogável, recuperação de senha segura. |
| **Autorização** | Supporting | Derivada (REQ12-15) | RBAC e verificação de dono em cada recurso (IDOR). |
| **Legalidade** | **Driving** | Derivada | LGPD (CPF, OAB, faturamento, conteúdo de terceiros), obrigações fiscais, validade probatória (premissa). Restringe retenção, região, logs e exclusão. |
| **Privacidade** | Supporting | Premissa | Equipe interna (suporte, operações) não vê o conteúdo capturado: envelope encryption, acesso excepcional com aprovação e auditoria (break-glass). |

### 1.4 Experiência e integração

| Característica | Papel | Origem | Justificativa e meta |
|---|---|---|---|
| **Usabilidade** | Supporting | Derivada | Iniciar captura em <= 3 passos. Advogado usa sob pressão. |
| **Aprendizagem** | Baixa | Premissa | Onboarding curto; ajuda contextual. Sem algoritmos que aprendem. |
| **Interoperabilidade** | Supporting | Explícita (REQ06-07) | Mercado Pago, e-mail transacional, TSA, KMS. Adapters isolam o núcleo. |
| **Compatibilidade** | Baixa | Premissa | Navegadores modernos suportados pela interface. Sem integração por ambiente compartilhado. |
| **Customização** | N/A (v1) | - | Sem fluxos por cliente. Reavaliar com perfil empresa. |

### 1.5 Engenharia

| Característica | Papel | Origem | Justificativa e meta |
|---|---|---|---|
| **Agilidade** | Supporting | Premissa | Equipe pequena entregando rápido. Composta por Testabilidade e Implantabilidade. |
| **Testabilidade** | Supporting | Premissa | Fitness functions automatizadas; ambiente de teste do gateway e da TSA (sandbox). |
| **Implantabilidade** | Supporting | Premissa | CI/CD com dois artefatos (núcleo e worker), IaC, deploy sem downtime, rollback. |

Resumo: 4 Driving, 20 Supporting, 5 Baixa, 4 N/A (Instalabilidade, Alavancagem, Customização v1 e a parte de Aprendizagem algorítmica).

## 2. Top 4 e análise de trade-offs

**Top 4 (Driving): Segurança, Legalidade, Confiabilidade (Reliability/Safety) e Robustez.**

Por que estas e não as demais: o livro orienta escolher poucas características que de fato moldam a arquitetura e tratar o resto como apoio. O Veriditi vende evidência: o produto é inútil se o registro vazar ou for adulterado (Segurança), se for ilegal operar/armazenar (Legalidade), se o registro ou o dinheiro se perdem (Confiabilidade) ou se a sessão de captura não sobrevive às falhas comuns do mundo real (Robustez).

| Característica | Custo aceito (trade-off) | Característica sacrificada ou secundária |
|---|---|---|
| Segurança | Atrito de MFA; sandbox e proxy de egress elevam custo e latência de início; hash/assinatura tornam finalização mais lenta. | Usabilidade e Desempenho cedem em fluxos críticos. |
| Legalidade | Retenção com prazo e crypto-shredding exigem gestão de chaves; limita logs e analytics; região no Brasil limita provedores. | Portabilidade e Extensibilidade de provedores. |
| Confiabilidade | Ledger, saga de reserva, outbox e reconciliação aumentam código e estados intermediários na UX. | Simplicidade e Agilidade. |
| Robustez | Persistência incremental, sessão retomável e token de sessão separado aumentam a complexidade do worker e o custo de armazenamento. | Desempenho e custo por sessão. |

Conflitos de característica tratados explicitamente:
- **Segurança/Confiabilidade (imutabilidade) x Legalidade/Arquivamento (exclusão):** resolvido por retenção + crypto-shredding (ADR-0008).
- **Robustez (sessão longa) x Segurança (token curto):** resolvido por token de sessão de captura separado (ADR-0010).
- **Privacidade (equipe não vê conteúdo) x Suportabilidade:** suporte vê status e metadados, não conteúdo; acesso excepcional auditado.
- **Elasticidade x Custo:** pool com teto no v1 (ADR-0014).

Por que as demais ficaram de apoio: Disponibilidade, Recuperabilidade, Continuidade e Tolerância a falhas são exigidas, mas o produto tolera paradas curtas e não tem SLA de missão crítica; são atendidas por práticas padrão de nuvem. Elasticidade e Escalabilidade não têm requisito de pico. Usabilidade e Acessibilidade são importantes mas não alteram a estrutura.

## 3. Componentes

| Componente | Responsabilidades | Reqs | Características que suporta |
|---|---|---|---|
| **Edge/BFF** | Entrada única, WAF, rate limit, validação de token | todos | Segurança, Desempenho |
| **Identity & Access** | Cadastro (D1), login, logout, senha, MFA, RBAC, owner check | REQ01-04 | Autenticação, Autorização, Segurança |
| **Privacy & Retention** | Bases legais, retenção, exclusão, crypto-shredding, direitos do titular | transversal | Legalidade, Arquivamento, Privacidade |
| **Billing & Credits** | Pacotes versionados (D2), ledger, reserva/confirmação/estorno | REQ05 | Confiabilidade, Configurabilidade |
| **Payment Gateway Adapter** | Mercado Pago, PIX, webhooks, reconciliação | REQ05-06 | Interoperabilidade, Confiabilidade |
| **Notification** | E-mails via fila, retry, bounce | REQ07, 10, 11b | Tolerância a falhas |
| **Capture Session** | Saga de captura, ciclo de vida, token de sessão, retomada | REQ08-11 | Robustez, Confiabilidade |
| **Capture Worker** (separado) | Navegador isolado, gravação, buffer incremental, hash | REQ08-11 | Segurança, Robustez, Tolerância a falhas |
| **Evidence Vault** | Storage imutável, envelope encryption, manifest | REQ08-15 | Segurança, Arquivamento, Recuperabilidade |
| **Timestamp/Signing Service** | TSA RFC 3161 e assinatura via KMS | REQ14 | Segurança, Legalidade |
| **Registry & Query** | Listagem e detalhes por dono | REQ12-13 | Autorização, Usabilidade |
| **Reporting** | Relatório e ZIP a partir do Vault | REQ14-15 | Segurança, Desempenho |
| **Audit Log** | Trilha append-only | transversal | Segurança, Legalidade, Suportabilidade |
| **Abuse & Quota** | Quotas, limites, denylist | REQ08 | Segurança, Desempenho |
| **Admin/Config** | Pacotes, limites, parâmetros | D2 | Configurabilidade |

## 4. Estilo: monólito modular + Capture Worker isolado

Quanta: (1) núcleo transacional; (2) Capture Worker. Separação do worker por **Segurança** (executa conteúdo não confiável) e perfil de recursos. Microserviços rejeitados (Agilidade, Manutenibilidade e custo operacional com equipe pequena; sem necessidade de deploy independente). Event-driven puro rejeitado (Confiabilidade de créditos exige consistência forte). Detalhes: ADR-0002.

Características do estilo (ranking esperado): Implantabilidade e Testabilidade médias; Manutenibilidade boa com fronteiras verificadas; Escalabilidade e Elasticidade limitadas ao núcleo (aceito); Tolerância a falhas do núcleo moderada (multi-AZ); isolamento do worker alto.

## 5. Táticas por característica

### 5.1 Top 4

**Segurança** (inclui Autenticação, Autorização e integridade)
- MFA TOTP para advogados; senha Argon2id; sessões revogáveis; recuperação de senha com token único, TTL e resposta uniforme.
- Owner check em todo recurso (IDOR); RBAC.
- Worker: sandbox gVisor/Firecracker efêmero; proxy de egress com bloqueio de RFC1918, link-local, loopback, metadata; sem segredos; limites de tempo/memória/tamanho.
- Integridade: SHA-256 por chunk e artefato; manifest assinado via KMS; object storage versionado com bloqueio de sobrescrita; trilha de auditoria.
- TLS, criptografia em repouso (envelope), WAF, SAST/SCA, validação de assinatura de webhook.

**Legalidade** (inclui Privacidade e Arquivamento)
- Mapeamento de dados e base legal; minimização; RIPD antes do go-live.
- Retenção mínima/máxima; exclusão por crypto-shredding; região Brasil.
- Logs sem PII; aviso sobre conteúdo de terceiros.
- Privacidade interna: equipe não acessa conteúdo; break-glass aprovado e auditado.
- Carimbo de tempo TSA se houver pretensão probatória.

**Confiabilidade (Reliability/Safety)**
- Ledger append-only, saga reserva-confirmação-estorno, idempotência por chave.
- Outbox + fila durável com DLQ; reconciliação periódica com Mercado Pago; tratamento de PIX expirado e estornos.
- Confirmação de débito só após artefato persistido e hash gravado.
- Read-your-writes para status do próprio registro.

**Robustez**
- Persistência incremental de chunks no worker; sessão retomável após queda do worker ou do usuário.
- Heartbeat do cliente; finalização segura por abandono (timeout) preservando o que foi gravado.
- Timeouts, circuit breaker e retry com backoff para gateway, e-mail e TSA; carimbo assíncrono ("carimbo pendente").
- Token de sessão de captura independente do login.
- Limites e quotas para proteger contra entradas extremas (sites enormes, loops de redirect, downloads).

### 5.2 Apoio (resumo)

| Característica | Tática |
|---|---|
| Disponibilidade | Multi-AZ, health checks, deploy sem downtime, SLO 99,5% |
| Continuidade/Recuperabilidade | Backups, réplica entre regiões, RPO/RTO, teste de restore semestral |
| Desempenho | Pool aquecido de workers, cache de catálogo, geração assíncrona de relatório, testes de estresse |
| Escalabilidade/Elasticidade | Núcleo sem estado; autoscaling do worker por fila com teto; gatilho para revisão |
| Tolerância a falhas | Substituição automática de worker; retry; DLQ |
| Configurabilidade | Admin/Config e catálogo de pacotes versionado |
| Extensibilidade | Interfaces de adapter; papel + `owner` genérico |
| Manutenibilidade | Fronteiras por módulo + teste de arquitetura |
| Atualizabilidade | Imagem de worker com atualização mensal automática do navegador; migrações expand/contract |
| Suportabilidade | Trace id, painel de status sem conteúdo |
| Acessibilidade | WCAG 2.1 AA, testes automatizados e manuais |
| Interoperabilidade | Adapters e contratos de integração testados (sandbox) |
| Usabilidade | Fluxo de captura em <= 3 passos |
| Agilidade/Testabilidade/Implantabilidade | CI/CD, IaC, fitness functions, ambientes efêmeros |

## 6. Rastreabilidade REQ/D -> componente -> tática -> teste

| REQ/D | Componente | Característica | Tática | Teste/Fitness function |
|---|---|---|---|---|
| REQ01 | Identity | Autenticação, Legalidade | Validação, unicidade, Argon2id, consentimento | Cadastro e duplicidade |
| REQ02 | Identity, Notification | Autenticação | Token único, TTL, rate limit, resposta uniforme | Enumeração e reuso de token |
| REQ03 | Identity | Autenticação | MFA, bloqueio progressivo | Brute force |
| REQ04 | Identity | Autenticação | Revogação server-side | Token revogado rejeitado |
| REQ05 | Billing, Gateway | Confiabilidade | Ledger, idempotência | Webhook repetido gera 1 lançamento |
| REQ06 | Gateway | Interoperabilidade, Confiabilidade | Webhook assinado, reconciliação | Reconciliação diária sem divergência |
| REQ07, 10, 11b | Notification | Tolerância a falhas | Outbox, retry, DLQ | Falha do provedor não perde evento |
| REQ08 | Capture Session, Worker, Abuse | Segurança, Robustez | Sandbox, egress, limites, reserva | Teste de egress; quota |
| REQ09, 11 | Capture Session, Vault, Signing | Robustez, Segurança | Persistência incremental, hash, manifest | Retomada após falha; re-hash |
| REQ12, 13 | Registry | Autorização | Owner check | IDOR |
| REQ14 | Reporting, Signing | Segurança, Legalidade | Relatório no manifest | Hash e carimbo verificáveis |
| REQ15 | Reporting | Segurança | ZIP regenerado e auditado | Hash do ZIP confere |
| D1 | Identity | Extensibilidade | Papel + owner genérico | Novo papel sem alterar Registry |
| D2 | Billing, Admin | Configurabilidade | Pacotes versionados | Compra referencia versão |
| NFR Segurança | Todos | Segurança | Edge + RBAC + auditoria | SAST/SCA; acesso sem autenticação |

Pendência do documento: REQ11 duplicado; o segundo tratado como **REQ11b**.

## 7. Fitness functions

- Módulo não acessa tabelas de outro (CI).
- Worker não alcança redes privadas nem metadata de cloud.
- Usuário A nunca lê registro de B.
- Webhook repetido gera 1 lançamento; soma do ledger = saldo; pagamentos = créditos.
- Re-hash por amostragem contra o manifest.
- Matar o worker durante a captura: sessão retomada, nenhuma perda de mais de 10 s, reserva não duplicada (Robustez).
- Queda de rede do cliente por 60 s: sessão preservada ou finalizada com segurança.
- Logs sem PII; exclusão por shredding deixa artefato ilegível.
- Restore de backup dentro do RTO; failover de AZ.
- Teste de estresse: pool de workers no teto não degrada o núcleo.
- Auditoria WCAG automatizada em CI.

## 8. Premissas a validar

| Premissa | Dono | Impacto se falsa |
|---|---|---|
| Valor probatório formal (TSA) | Jurídico | Remove ADR-0007 |
| Captura server-side interativa | Produto | Troca ADR-0003 e reduz ADR-0004 |
| MFA obrigatório para advogados | Produto | Ajusta ADR-0010 |
| Prazos de retenção | Jurídico | Define ADR-0008 |
| SLO 99,5%, concorrência N | Negócio | Revisa ADR-0014 |
| Equipe interna não vê conteúdo (Privacidade) | Segurança/Negócio | Simplifica ADR-0009 |
| Região Brasil obrigatória | Jurídico | Define provedor |
