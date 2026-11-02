# ADR-007: Gravar o evento dentro da transação de `changeStatus`, por uma função pura que recebe o client da transação

- **Status:** Aceito
- **Data:** não informada (a transcrição registra apenas "quinta-feira, 09:00")
- **Decisores:** Bruno (Eng. Pleno, Pedidos), Diego (Eng. Sênior, Plataforma), Marcos (PM)
- **Decisões do catálogo:** D-09

## Contexto

A única alteração crítica no código existente é no método `changeStatus` do service de pedidos ([09:40] Bruno). Para valer a garantia do [ADR-001](ADR-001-outbox-no-mysql.md), a gravação na outbox precisa ser atômica com a mudança de status (`src/modules/orders/order.service.ts`, `changeStatus`, que já executa `this.prisma.$transaction`).

## Decisão

A gravação do evento na outbox acontece **dentro da mesma transação** de `changeStatus`. Se essa gravação falhar, a transação inteira é desfeita: não pode existir status alterado sem evento ([09:40] Bruno, [09:41] Diego).

A gravação é feita por uma **função pura que recebe o client da transação atual** e é chamada pelo service de pedidos (citada na reunião como `publishWebhookEvent(tx, order, fromStatus, toStatus)`; nome final definido na especificação) ([09:41] Bruno, [09:41] Diego).

O filtro por status que cada endpoint quer ouvir é aplicado nesse momento: se nenhum webhook do cliente se interessa pelo novo status, nenhuma linha é gravada ([09:33] Marcos, [09:34] Bruno, [09:34] Diego; catálogo S-01).

## Alternativas consideradas

### Gravar o evento fora da transação

- **Descrição:** inserir na outbox depois do commit da mudança de status.
- **Por que foi descartada:** perde toda a garantia de consistência entre status e evento ([09:41] Diego).

### Injetar o repository de webhooks no service de pedidos

- **Descrição:** dar ao service de pedidos uma dependência completa do módulo de webhooks.
- **Por que foi descartada:** desnecessário; uma função pura que recebe o client da transação resolve com acoplamento menor ([09:41] Diego).

## Consequências

### Positivas

- A atomicidade entre mudança de status e evento fica garantida pelo banco ([09:40] Bruno, [09:41] Diego).
- Status que nenhum endpoint quer ouvir não geram linha na outbox ([09:34] Bruno).
- O acoplamento entre os módulos de pedidos e de webhooks se resume a uma função (catálogo D-09).

### Negativas

- O módulo de pedidos passa a depender de uma função do módulo de webhooks (catálogo D-09).
- A consulta das configurações para aplicar o filtro de status roda dentro da transação de mudança de status, aumentando um pouco o trabalho feito nela (catálogo D-09, S-01).
- Uma falha na gravação do evento passa a impedir a mudança de status ([09:40] Bruno).

**Trade-off:** aceitamos que uma falha ao gravar o evento bloqueie a mudança de status em troca da garantia de que nenhuma mudança fica sem notificação.

## Ganchos com o código

- `src/modules/orders/order.service.ts`: método `changeStatus`. A chamada entra dentro do `this.prisma.$transaction`, depois de `tx.order.update` e `tx.orderStatusHistory.create`.
- `src/modules/orders/order.status.ts`: máquina de estados que define as transições que geram eventos.

## Referências

- Origem na reunião: [09:33] Marcos, [09:34] Bruno, [09:34] Diego, [09:40] Bruno, [09:41] Diego, [09:41] Bruno
- Catálogo: D-09, S-01
- Relacionados: [ADR-001](ADR-001-outbox-no-mysql.md), [ADR-006](ADR-006-reuso-padroes-do-projeto.md)
