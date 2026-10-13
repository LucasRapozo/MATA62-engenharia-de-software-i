# ADR-0007: Carimbo do tempo e validade probatória

- Status: Proposta (premissa a validar com Jurídico)
- Data: 2026-10-13
- Decisores: Jurídico, Arquiteto principal, Produto

## Contexto
A v1 propunha "timestamps NTP". Isso não tem valor probatório: é a palavra do próprio sistema. Se o produto pretende ser prova, precisa de terceiro de confiança. O documento de requisitos não afirma valor jurídico.

## Alternativas consideradas
1. Timestamp do servidor (NTP) sem terceiro: simples, sem valor probatório forte.
2. Carimbo de tempo de TSA (RFC 3161), ICP-Brasil/ITI ou provedor acreditado: valor probatório reforçado; custo por carimbo.
3. Ancoragem externa do hash (por exemplo, serviço de notarização): alternativa complementar.
4. Assinatura qualificada ICP-Brasil do manifest: máximo valor, maior custo e complexidade.

## Decisão
Arquitetura prevê componente **Timestamp/Signing Service** com interface única. Estratégia:
- v1 (se jurídico confirmar pretensão probatória): TSA RFC 3161 sobre o hash do manifest, mais assinatura por chave do Veriditi em KMS.
- Se o jurídico não exigir: manter timestamp de servidor com NTP e log, documentando a limitação ao usuário.
- Assinatura ICP-Brasil fica como evolução.

## Consequências
Positivas: evidência contestável com menor risco; interface permite trocar provedor.
Negativas: custo por registro; dependência de terceiro (falha da TSA não deve bloquear a captura: carimbar de forma assíncrona e marcar o status "carimbo pendente").

## Fitness functions associadas
Todo registro finalizado tem carimbo verificável em até X minutos; verificação independente do carimbo em CI com amostra.

## Premissas a validar
Pretensão de valor probatório; escolha de TSA; texto de aviso ao usuário.
