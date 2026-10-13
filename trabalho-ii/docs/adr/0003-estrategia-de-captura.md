# ADR-0003: Estratégia de captura (cliente x servidor x híbrido)

- Status: Proposta (depende de validação de Produto)
- Data: 2026-10-13
- Decisores: Arquiteto principal, Produto, Segurança

## Contexto
REQ08 diz que o sistema "permite a captura de conteúdo através de um site (tela ou vídeo de navegação)". O documento não diz onde a captura executa. A escolha define confiabilidade da evidência, superfície de ataque e complexidade.

## Direcionadores
Confiança na origem da evidência, risco de SSRF e conteúdo malicioso, custo, experiência do usuário.

## Alternativas consideradas
1. **Extensão/app no cliente**: captura local. Sem SSRF e custo baixo; evidência menos confiável (cliente pode manipular), dependente de instalação e de navegador.
2. **Navegador remoto no servidor**: o usuário controla um navegador isolado via streaming. Evidência produzida em ambiente controlado; risco alto e complexidade de streaming interativo.
3. **Híbrido**: extensão para conveniência e servidor para casos de alto valor probatório.
4. **Navegação automatizada (sem interação)**: worker abre a URL, rola e captura. Mais simples, mas não cobre fluxos que exigem login ou clique.

## Decisão
Para o v1: **navegador remoto server-side com sessão interativa** (alternativa 2), com URL inicial informada (REQ08), por entregar evidência gerada em ambiente controlado. A alternativa 4 é o plano de contingência se o streaming interativo se mostrar inviável no prazo.

## Consequências
Positivas: origem da evidência controlada; sem dependência de instalação no cliente.
Negativas: SSRF, conteúdo malicioso e abuso viram risco principal; custo de infraestrutura por sessão; latência de streaming; necessidade de quotas. Mitigações no ADR-0004.

## Fitness functions associadas
Teste de egress; limite de duração/tamanho; custo por sessão monitorado.

## Premissas a validar
Produto confirma que o usuário precisa interagir com o site (login, cliques) durante a captura.

## Revisão
Se a validação indicar captura local suficiente, trocar por alternativa 1 e remover o isolamento pesado.
