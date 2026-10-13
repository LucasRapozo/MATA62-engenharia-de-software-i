# ADR-0011: Perfis de usuário (D1) e conceito de organização

- Status: Proposta
- Data: 2026-10-13
- Decisores: Arquiteto principal, Produto

## Contexto
D1 prevê advogados, usuários comuns e empresas; v1 só os dois primeiros. Advogado tem CPF e OAB; usuário comum, CPF. Empresa implica organização com vários usuários e créditos compartilhados.

## Alternativas consideradas
1. Tabelas separadas por tipo: duplica regras.
2. Um tipo de usuário com papel e atributos de perfil: escolhido.
3. Modelar tenant/organização agora: especulativo.

## Decisão
- Entidade Usuário com `papel` (ADVOGADO, COMUM) e perfil com atributos específicos (OAB para advogado, validada).
- Permissões no módulo Identity, referenciadas por papel.
- **Não** construir organização na v1; reservar identificador `owner` genérico (usuário ou futura organização) em registros, ledger e chaves, para evitar migração dolorosa.

## Consequências
Positivas: empresa vira adição de papel e entidade organização; sem redesign do Registry.
Negativas: indireção mínima no `owner`; decisão sobre créditos compartilhados fica para v2.

## Fitness functions associadas
Adicionar papel de teste sem alterar Registry; testes de autorização por papel.
