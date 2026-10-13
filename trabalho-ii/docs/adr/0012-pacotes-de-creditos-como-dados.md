# ADR-0012: Pacotes de créditos como dados versionados (D2)

- Status: Proposta
- Data: 2026-10-13
- Decisores: Arquiteto principal, Produto

## Contexto
D2: pacotes Básico, Médio e Premium na v1. Campos: nome, quantidade de créditos, valor por crédito, descrição dos benefícios. Preços e composição tendem a mudar.

## Alternativas consideradas
1. Pacotes em código/configuração de deploy: exige deploy a cada mudança.
2. Pacotes em tabela, mutáveis: perde histórico.
3. Pacotes em tabela com versionamento imutável: escolhido.

## Decisão
Catálogo persistido; cada alteração cria nova versão; compra grava referência à versão e o valor total calculado no momento. Pacotes ativos/inativos por flag.

## Consequências
Positivas: novos pacotes sem deploy; histórico fiel de preços.
Negativas: relatórios e financeiro precisam considerar versão; tela administrativa necessária.

## Fitness functions associadas
Compra aponta para versão imutável; mudança de preço não altera compras anteriores.
