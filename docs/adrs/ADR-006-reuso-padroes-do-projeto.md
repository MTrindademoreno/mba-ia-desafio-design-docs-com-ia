# ADR-006: Construir o módulo de webhooks sobre os padrões que o projeto já usa

- **Status:** Aceito
- **Data:** não informada (a transcrição registra apenas "quinta-feira, 09:00")
- **Decisores:** Larissa (Tech Lead), Bruno (Eng. Pleno, Pedidos), Diego (Eng. Sênior, Plataforma), Sofia (Eng. Segurança), Marcos (PM)
- **Decisões do catálogo:** D-08, D-10

## Contexto

A base de código tem padrões claros de módulo, erros, validação, autorização e logging. O time quer que a nova feature os siga, em vez de introduzir estruturas próprias ([09:27] Bruno, [09:28] Bruno, [09:30] Larissa).

## Decisão

O módulo de webhooks segue os padrões existentes ([09:27] Bruno, [09:28] Bruno, [09:29] Larissa, [09:29] Bruno, fechado em [09:30] Larissa):

- **Estrutura de módulo:** controller, service, repository, routes e schemas, no mesmo molde de `src/modules/orders/`.
- **Erros:** classes que estendem `AppError` (`src/shared/errors/app-error.ts`), análogas a `InsufficientStockError` e `InvalidStatusTransitionError` (`src/shared/errors/http-errors.ts`), com códigos de erro de **prefixo `WEBHOOK_`**.
- **Tratamento de erros:** o middleware centralizado (`src/middlewares/error.middleware.ts`) já trata `AppError`, erros de validação e erros do ORM, então recebe os novos erros sem alteração.
- **Validação:** schemas com o middleware `validate` (`src/middlewares/validate.middleware.ts`).
- **Logging:** o logger existente (`src/shared/logger/index.ts`), sem nada novo.
- **Autorização:** `requireRole` (`src/middlewares/auth.middleware.ts`).

### Autorização das operações

- O **replay de eventos da DLQ** ([ADR-003](ADR-003-retry-backoff-exponencial-e-dlq.md)) exige **role ADMIN**, aplicada com `requireRole`, e registra em log quem executou o replay, para auditoria ([09:35] Larissa, [09:36] Sofia, [09:36] Larissa).
- O **CRUD de configuração** de webhooks aceita qualquer usuário autenticado, por enquanto ([09:36] Marcos, [09:37] Sofia).

## Alternativas consideradas

### Estruturas próprias para o módulo

- **Descrição:** introduzir bibliotecas, formato de erro ou camadas específicas para webhooks.
- **Por que foi descartada:** o time optou por consistência com a base existente ([09:29] Larissa, [09:30] Larissa). *(Alternativa não debatida explicitamente na reunião; registrada para completar o trade-off.)*

### Liberar o replay da DLQ para o role OPERATOR

- **Descrição:** permitir que operadores também reprocessem eventos.
- **Por que foi descartada:** mexer na fila de entrega de notificações não é atribuição de operador ([09:36] Sofia).

## Consequências

### Positivas

- O módulo fica igual aos outros, sem estrutura nova para o time conhecer ([09:30] Larissa).
- O middleware de erro e o logging funcionam sem mudança ([09:29] Bruno).
- A operação sensível (replay) fica protegida e auditável sem código novo de autorização ([09:36] Sofia).

### Negativas

- O módulo herda as limitações que os padrões atuais tiverem (catálogo D-08).
- A autorização do CRUD de configuração fica frouxa nesta fase. Endurecê-la foi **adiado** ([09:37] Sofia; catálogo X-06).

**Trade-off:** aceitamos herdar os limites dos padrões atuais e uma autorização mais permissiva na configuração em troca de consistência, velocidade de entrega e zero código novo de infraestrutura transversal.

## Ganchos com o código

- `src/modules/orders/`: molde de estrutura de módulo.
- `src/shared/errors/app-error.ts`: classe base `AppError`.
- `src/shared/errors/http-errors.ts`: modelo de erros específicos com código (`INSUFFICIENT_STOCK`, `INVALID_STATUS_TRANSITION`).
- `src/shared/errors/index.ts`: barrel onde os novos erros serão exportados.
- `src/middlewares/error.middleware.ts`: tratamento centralizado, sem alteração.
- `src/middlewares/validate.middleware.ts`: validação de entrada.
- `src/middlewares/auth.middleware.ts`: `authenticate` e `requireRole`, com roles `ADMIN` e `OPERATOR`.
- `src/shared/logger/index.ts`: logger, incluindo o log de auditoria do replay.

## Referências

- Origem na reunião: [09:27] Bruno, [09:28] Bruno, [09:29] Larissa, [09:29] Bruno, [09:30] Larissa, [09:35] Larissa, [09:36] Sofia, [09:36] Larissa, [09:36] Marcos, [09:37] Sofia
- Catálogo: D-08, D-10, X-06
- Relacionados: [ADR-003](ADR-003-retry-backoff-exponencial-e-dlq.md), [ADR-007](ADR-007-integracao-changestatus-transacional.md)
