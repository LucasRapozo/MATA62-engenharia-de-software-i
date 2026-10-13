# ADR-0016: Robustez da sessão de captura (retomada e finalização segura)

- Status: Proposta
- Data: 2026-10-13
- Decisores: Arquiteto principal, Produto

## Contexto
Robustez é característica Driving (ADR-0001). A captura é longa e interativa (REQ08-11) e sofre quedas de rede do usuário, falha de worker, sites lentos ou fora do ar, e timeouts de gateway e TSA. Perder a sessão perde evidência irreproduzível.

## Alternativas consideradas
1. Sessão volátil em memória do worker: simples, perde tudo em falha.
2. Persistência apenas ao final: mesmo problema.
3. **Persistência incremental de chunks + sessão retomável + finalização segura por abandono**: escolhido.

## Decisão
- Worker grava chunks no Vault (área de staging) com hash por chunk, a cada intervalo curto (por exemplo 5-10 s).
- Estado da sessão (Capture Session) é persistido no núcleo: INICIADA, GRAVANDO, INTERROMPIDA, FINALIZANDO, CONCLUÍDA, FALHA, ABANDONADA.
- Falha de worker: novo worker assume a sessão e continua a partir do último chunk confirmado; evento marcado no relatório como interrupção.
- Queda do cliente: heartbeat; após timeout configurável, a sessão é finalizada com segurança (conteúdo gravado até então vira registro, débito confirmado conforme regra de negócio).
- Token de sessão de captura independente do login (ADR-0010).
- Chamadas externas (gateway, e-mail, TSA) com timeout, circuit breaker e retry; carimbo de tempo assíncrono.
- Limites: duração máxima, tamanho máximo, número de redirects, downloads desabilitados.

## Consequências
Positivas: perda máxima limitada ao intervalo do último chunk; menos reclamações e estornos.
Negativas: mais estados e código; custo extra de armazenamento em staging; regra de negócio necessária para cobrar sessões interrompidas; relatório precisa registrar interrupções.

## Fitness functions associadas
Matar worker em meio à captura: sessão retomada, perda <= 10 s; queda de rede de 60 s: sessão preservada; abandono: finalização segura e reserva resolvida; staging limpo após TTL.

## Premissas a validar
Regra de cobrança para sessão interrompida ou abandonada (Produto).
