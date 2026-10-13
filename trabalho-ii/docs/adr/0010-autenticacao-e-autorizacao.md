# ADR-0010: Autenticação, sessão e autorização

- Status: Proposta (MFA a validar com Produto)
- Data: 2026-10-13
- Decisores: Arquiteto principal, Segurança, Produto

## Contexto
REQ01-04 cobrem cadastro, recuperação de senha, login e logout. O NFR é acesso apenas por usuários cadastrados. Capturas podem durar muito mais que uma sessão curta de login.

## Alternativas consideradas
1. Provedor de identidade externo (Cognito, Auth0, Keycloak): menos código sensível, dependência e custo.
2. Identity próprio no monólito: controle total, risco de implementação.
3. IdP gerenciado para autenticação/MFA, autorização (RBAC e owner check) no núcleo: escolhido como recomendação.

## Decisão
- Senha com Argon2id; MFA TOTP obrigatório para advogados e opcional (incentivado) para usuários comuns (premissa).
- Access token curto + refresh token rotativo, revogáveis no servidor. Logout (REQ04) revoga a sessão; troca de senha invalida todas.
- Recuperação de senha (REQ02): token de uso único, TTL curto, rate limit, resposta uniforme (sem enumeração).
- **Token de sessão de captura** separado, escopo único, para que a expiração do login não interrompa uma gravação longa.
- Autorização: RBAC para ações e owner check em todo recurso (Registry, Reporting, downloads) contra IDOR.
- Rate limit e bloqueio progressivo no login; auditoria de eventos de autenticação.

## Consequências
Positivas: reduz risco de falhas de autenticação; separa login e captura.
Negativas: dependência de IdP; atrito de MFA; mais um tipo de token.

## Fitness functions associadas
Teste de IDOR; token revogado rejeitado; token de reset não reutilizável; teste de enumeração de contas.
