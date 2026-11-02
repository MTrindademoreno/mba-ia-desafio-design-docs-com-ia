# Architectural Decision Records

Este diretório armazena os ADRs (Architectural Decision Records) do projeto.
Cada decisão arquitetural relevante deve ser registrada aqui em arquivos individuais,
nomeados sequencialmente no formato `ADR-NNN-titulo-em-kebab-case.md`
(por exemplo `ADR-001-outbox-no-mysql.md`).

## Índice

| ADR | Título | Status | Decisões do catálogo |
| --- | --- | --- | --- |
| [ADR-001](ADR-001-outbox-no-mysql.md) | Usar o padrão Outbox no MySQL existente para disparar os webhooks | Aceito | D-01, S-02 |
| [ADR-002](ADR-002-worker-separado-polling-2s.md) | Rodar o worker de entrega em processo separado, com polling de 2 segundos | Aceito | D-02 |
| [ADR-003](ADR-003-retry-backoff-exponencial-e-dlq.md) | Fazer retry com backoff exponencial em 5 tentativas e mover falhas definitivas para uma DLQ | Aceito | D-04, D-05, S-04 |
| [ADR-004](ADR-004-hmac-sha256-secret-por-endpoint.md) | Assinar os webhooks com HMAC-SHA256 usando uma secret por endpoint, rotacionável com 24h de convivência | Aceito | D-06 |
| [ADR-005](ADR-005-at-least-once-x-event-id.md) | Garantir entrega at-least-once, com `X-Event-Id` para o cliente deduplicar | Aceito | D-07 |
| [ADR-006](ADR-006-reuso-padroes-do-projeto.md) | Construir o módulo de webhooks sobre os padrões que o projeto já usa | Aceito | D-08, D-10 |
| [ADR-007](ADR-007-integracao-changestatus-transacional.md) | Gravar o evento dentro da transação de `changeStatus`, por uma função pura que recebe o client da transação | Aceito | D-09 |
| [ADR-008](ADR-008-ordering-single-worker-por-order-id.md) | Usar um único worker, garantindo a ordem dos eventos apenas por pedido | Aceito | D-03 |
