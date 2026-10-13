# ADR-0004: Isolamento do Capture Worker e proteção contra SSRF

- Status: Proposta
- Data: 2026-10-13
- Decisores: Arquiteto principal, Segurança

## Contexto
O worker navega em URLs arbitrárias fornecidas por usuários (REQ08). Riscos: SSRF contra rede interna e metadata de cloud, exploit de navegador, download malicioso, uso como proxy/DDoS, conteúdo ilegal, custo descontrolado.

## Alternativas consideradas
1. Container comum com rede aberta: rejeitado.
2. Container endurecido + proxy de egress: base mínima.
3. MicroVM (Firecracker) ou gVisor + proxy de egress + sessão descartável: escolhido.
4. Serviço SaaS de navegador remoto: opção de compra, não avaliada em custo.

## Decisão
- Uma sessão = um sandbox efêmero (gVisor ou Firecracker), destruído ao final.
- Execução sem privilégios, sistema de arquivos somente leitura, sem segredos do núcleo.
- Egress somente por proxy que: aceita só http/https; resolve DNS e fixa o IP; bloqueia loopback, RFC1918, link-local, 169.254.169.254, IPv6 equivalentes e redirecionamentos para esses destinos.
- Rede do worker sem rota para banco, fila ou serviços internos; comunicação com o núcleo só por canal autenticado de escopo único.
- Limites: tempo máximo, memória, CPU, tamanho de artefato e de downloads (downloads desabilitados por padrão).
- Quotas e rate limit por usuário (componente Abuse & Quota); denylist de domínios; canal de denúncia.

## Consequências
Positivas: reduz o maior risco do sistema; contém a exploração do navegador.
Negativas: custo e tempo de inicialização por sessão (pool aquecido); operação mais complexa; sites que bloqueiam IPs de datacenter podem falhar.

## Fitness functions associadas
Teste automatizado de egress (alvos internos e metadata devem falhar); teste de redirect para IP privado; teste de sandbox destruído ao final.

## Revisão
Revisar após pentest e a cada atualização major do navegador.
