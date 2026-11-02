# ADR-005: Garantir entrega at-least-once, com `X-Event-Id` para o cliente deduplicar

- **Status:** Aceito
- **Data:** não informada (a transcrição registra apenas "quinta-feira, 09:00")
- **Decisores:** Larissa (Tech Lead), Diego (Eng. Sênior, Plataforma), Bruno (Eng. Pleno, Pedidos), Sofia (Eng. Segurança), Marcos (PM)
- **Decisões do catálogo:** D-07

## Contexto

Com retry ([ADR-003](ADR-003-retry-backoff-exponencial-e-dlq.md)), o cliente pode receber o mesmo evento mais de uma vez e precisa conseguir diferenciar as repetições ([09:24] Diego, [09:25] Bruno).

## Decisão

Assumimos garantia de entrega **at-least-once**. Cada evento recebe um **UUID único quando entra na outbox**, enviado no header `X-Event-Id`. Deduplicar por esse id é **responsabilidade do cliente** ([09:25] Diego, fechado em [09:26] Larissa).

## Alternativas consideradas

### Exactly-once

- **Descrição:** garantir que cada evento seja processado pelo cliente exatamente uma vez.
- **Por que foi descartada:** exigiria coordenação entre os dois lados e é muito mais complexo. At-least-once com id do evento resolve a grande maioria dos casos e é o modelo usado por Stripe e GitHub ([09:25] Diego).

## Consequências

### Positivas

- Implementação simples, alinhada ao padrão de mercado ([09:25] Diego).
- O id é gerado uma vez, na inserção, e se mantém igual em todas as tentativas, o que permite deduplicar com segurança ([09:25] Diego).

### Negativas

- A responsabilidade de deduplicar fica com o cliente ([09:25] Sofia; catálogo P-05). A mitigação é documentar isso com destaque no portal do desenvolvedor ([09:26] Marcos).

**Trade-off:** aceitamos transferir a deduplicação para o cliente em troca de uma entrega simples e confiável, sem coordenação entre os dois lados.

## Referências

- Origem na reunião: [09:24] Diego, [09:25] Bruno, [09:25] Diego, [09:25] Sofia, [09:26] Marcos, [09:26] Larissa
- Catálogo: D-07, P-05
- Relacionados: [ADR-001](ADR-001-outbox-no-mysql.md), [ADR-003](ADR-003-retry-backoff-exponencial-e-dlq.md)
