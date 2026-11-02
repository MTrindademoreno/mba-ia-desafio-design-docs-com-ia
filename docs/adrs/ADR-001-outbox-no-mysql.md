# ADR-001: Usar o padrão Outbox no MySQL existente para disparar os webhooks

- **Status:** Aceito
- **Data:** não informada (a transcrição registra apenas "quinta-feira, 09:00")
- **Decisores:** Larissa (Tech Lead), Bruno (Eng. Pleno, Pedidos), Diego (Eng. Sênior, Plataforma)
- **Decisões do catálogo:** D-01, S-02

## Contexto

Clientes B2B querem ser notificados quando o status dos seus pedidos muda, em vez de consultar a API periodicamente ([09:00] Marcos). A mudança de status já roda numa transação que atualiza o pedido, grava o histórico de status e ajusta estoque ([09:03] Larissa; `src/modules/orders/order.service.ts`, método `changeStatus`).

Fazer a chamada HTTP ao cliente dentro dessa transação travaria a mudança de status de outros pedidos enquanto o cliente responde, e não haveria o que fazer se o cliente estivesse offline: desfazer a mudança de status por causa de uma falha do cliente é inaceitável ([09:04] Bruno).

## Decisão

Na **mesma transação SQL** da mudança de status, gravamos o evento numa tabela de outbox no MySQL que a aplicação já usa (chamada na reunião de `webhook_outbox`; nome final definido na especificação). Um processo separado lê essa tabela e faz as chamadas HTTP ([09:06] Diego, [09:07] Diego, fechado em [09:08] Larissa). Se a transação commitou, o evento existe; se deu rollback, o evento some junto ([09:07] Diego).

A linha da outbox guarda o **payload já renderizado** no momento da inserção, e não apenas a referência ao pedido. Assim o evento reflete o estado do pedido no instante da mudança, mesmo que o pedido mude depois ([09:51] Bruno, [09:52] Larissa, [09:52] Diego).

Como o worker lê a outbox está em [ADR-002](ADR-002-worker-separado-polling-2s.md). Como a gravação entra no código de pedidos está em [ADR-007](ADR-007-integracao-changestatus-transacional.md).

## Alternativas consideradas

### Disparo síncrono no service de pedidos

- **Descrição:** chamar o endpoint do cliente diretamente dentro do fluxo de mudança de status.
- **Por que foi descartada:** trava a mudança de status de outros pedidos e acopla o sucesso da operação à disponibilidade do cliente ([09:04] Bruno).

### Redis Streams ou fila dedicada

- **Descrição:** publicar o evento numa fila externa ao banco.
- **Por que foi descartada:** exige subir e operar infraestrutura nova (Redis Cluster), o que é overengineering para um time pequeno quando o MySQL existente resolve ([09:07] Larissa, [09:07] Diego).

### Outbox guardando só o id do pedido

- **Descrição:** gravar apenas a referência ao pedido e montar o payload na hora do envio.
- **Por que foi descartada:** se o pedido mudar depois, o evento deixaria de refletir o estado do momento da mudança de status ([09:51] Bruno, [09:52] Larissa).

## Consequências

### Positivas

- Não existe status alterado sem evento, nem evento de mudança que não aconteceu: a consistência vem da própria transação ([09:07] Diego).
- Nenhuma infraestrutura nova além do banco que já existe ([09:07] Diego).
- O cliente recebe a "foto" do pedido no instante da mudança ([09:52] Diego).

### Negativas

- As linhas já entregues precisam ser arquivadas periodicamente (por volta de 30 dias), e esse arquivamento ficou **fora do escopo desta feature** ([09:08] Diego; catálogo X-05).
- Eventos acumulados na tabela podem deixar a leitura do worker mais lenta. A mitigação prevista é indexar por status e data de criação e ler os pendentes em lotes pequenos ([09:07] Bruno, [09:08] Diego).

**Trade-off:** aceitamos manter e arquivar uma tabela a mais no banco transacional em troca de consistência garantida entre status e evento, sem infraestrutura nova.

## Ganchos com o código

- `src/modules/orders/order.service.ts`: `changeStatus` abre `this.prisma.$transaction`, onde a gravação na outbox entra.
- `prisma/schema.prisma`: onde a nova tabela será modelada.

## Referências

- Origem na reunião: [09:03] Larissa, [09:04] Bruno, [09:06] Diego, [09:07] Larissa, [09:07] Diego, [09:07] Bruno, [09:08] Diego, [09:08] Larissa, [09:51] Bruno, [09:52] Larissa, [09:52] Diego
- Catálogo: D-01, S-02, X-05
- Relacionados: [ADR-002](ADR-002-worker-separado-polling-2s.md), [ADR-007](ADR-007-integracao-changestatus-transacional.md)
