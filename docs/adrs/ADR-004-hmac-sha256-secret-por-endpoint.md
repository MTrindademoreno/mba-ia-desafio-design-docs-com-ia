# ADR-004: Assinar os webhooks com HMAC-SHA256 usando uma secret por endpoint, rotacionável com 24h de convivência

- **Status:** Aceito
- **Data:** não informada (a transcrição registra apenas "quinta-feira, 09:00")
- **Decisores:** Sofia (Eng. Segurança), Bruno (Eng. Pleno, Pedidos), Diego (Eng. Sênior, Plataforma)
- **Decisões do catálogo:** D-06

## Contexto

Os eventos carregam dados de pedidos e vão para endpoints fora da infraestrutura da plataforma. O cliente precisa conseguir verificar que a chamada veio da plataforma e que o corpo não foi alterado no caminho ([09:19] Sofia, [09:20] Sofia).

Já aconteceu de um cliente vazar uma secret em log de aplicação ([09:22] Diego), então o desenho precisa limitar o estrago de um vazamento.

## Decisão

Cada request de webhook leva uma **assinatura HMAC-SHA256 do corpo**, calculada com uma secret compartilhada e enviada no header `X-Signature` ([09:20] Sofia).

A secret é **única por endpoint**, nunca global, e é **gerada pela plataforma** ([09:21] Sofia, [09:21] Bruno). Ela pode ser **rotacionada** a pedido do cliente: ao rotacionar, a secret antiga continua válida em paralelo por **24 horas** e depois é invalidada ([09:21] Sofia, fechado em [09:22] Sofia).

## Alternativas consideradas

### Secret global da plataforma

- **Descrição:** uma única secret para assinar os eventos de todos os clientes.
- **Por que foi descartada:** se uma vaza, vaza tudo ([09:21] Sofia), e vazamento de secret por cliente já aconteceu ([09:22] Diego).

### Outro algoritmo de assinatura

- **Descrição:** usar um algoritmo diferente de HMAC-SHA256.
- **Por que foi descartada:** HMAC-SHA256 é o padrão de mercado e toda biblioteca séria suporta, o que facilita a verificação do lado do cliente ([09:20] Sofia).

## Consequências

### Positivas

- O estrago de um vazamento fica restrito a um endpoint de um cliente ([09:21] Sofia).
- O cliente troca a secret sem downtime, graças às 24h de convivência ([09:21] Sofia).
- O cliente verifica a assinatura com bibliotecas comuns ([09:20] Sofia).

### Negativas

- Durante a janela de rotação, a configuração do endpoint guarda duas secrets válidas e precisa controlar a expiração da antiga (catálogo D-06).
- A geração de secret e o esquema HMAC passam por **revisão de segurança obrigatória antes do deploy**, com pelo menos 2 dias úteis reservados no cronograma ([09:46] Sofia, [09:47] Larissa, [09:49] Sofia; catálogo P-06).

**Trade-off:** aceitamos a complexidade de gerenciar secrets por endpoint e janelas de rotação em troca de limitar o estrago de um vazamento a um único cliente.

## Referências

- Origem na reunião: [09:19] Sofia, [09:20] Sofia, [09:20] Bruno, [09:21] Sofia, [09:21] Bruno, [09:22] Diego, [09:22] Sofia, [09:46] Sofia, [09:47] Larissa, [09:49] Sofia
- Catálogo: D-06, P-06
- Relacionados: [ADR-005](ADR-005-at-least-once-x-event-id.md)
