# ADR-002: Rodar o worker de entrega em processo separado, com polling de 2 segundos

- **Status:** Aceito
- **Data:** não informada (a transcrição registra apenas "quinta-feira, 09:00")
- **Decisores:** Larissa (Tech Lead), Diego (Eng. Sênior, Plataforma), Bruno (Eng. Pleno, Pedidos), Marcos (PM)
- **Decisões do catálogo:** D-02

## Contexto

Com o evento gravado na outbox ([ADR-001](ADR-001-outbox-no-mysql.md)), falta decidir como ele sai da tabela e chega ao cliente ([09:09] Diego).

Para os clientes, "tempo real" é qualquer latência abaixo de 10 segundos ([09:02] Marcos). Essa premissa define quanto atraso o mecanismo de leitura pode ter.

## Decisão

Um worker em loop de **polling a cada 2 segundos** busca os eventos pendentes mais antigos da outbox, faz a entrega e marca o resultado ([09:09] Diego, registrado em [09:10] Larissa).

O worker roda como **processo separado da API**, com a mesma stack e o mesmo banco ([09:11] Diego). Ele cria sua própria instância do cliente de ORM, porque o client é por processo ([09:30] Bruno). Ganha um ponto de entrada novo, análogo ao do servidor HTTP em `src/server.ts`, com um script próprio para iniciá-lo (nomes de arquivo e de script definidos na especificação) ([09:11] Larissa).

## Alternativas consideradas

### Trigger de banco (abordagem reativa)

- **Descrição:** reagir à inserção na outbox em vez de consultar periodicamente.
- **Por que foi descartada:** o MySQL não tem `NOTIFY/LISTEN` como o Postgres; um trigger só executa SQL e não consegue avisar um processo externo ([09:09] Diego).

### Worker dentro da instância da API

- **Descrição:** rodar o loop de entrega no mesmo processo do servidor HTTP.
- **Por que foi descartada:** quando a API reinicia, o worker cai junto ([09:11] Diego).

## Consequências

### Positivas

- Polling de 2s atende com folga o limite de 10s que os clientes chamam de "tempo real" ([09:09] Diego, [09:10] Marcos).
- O ciclo de vida da entrega fica independente do ciclo de vida da API ([09:11] Diego).
- Reaproveita a stack e o banco que já existem ([09:11] Diego; `src/config/database.ts`).

### Negativas

- Latência mínima de até 2s no pior caso, aceita explicitamente ([09:10] Larissa; catálogo P-03).
- Mais um processo para implantar, operar e monitorar (catálogo D-02).

**Trade-off:** aceitamos até 2s de atraso e um processo a mais para operar em troca de um mecanismo simples, sem infraestrutura nova e que não cai junto com a API.

## Ganchos com o código

- `src/server.ts`: modelo de ponto de entrada a replicar para o worker.
- `src/config/database.ts`: `createPrismaClient()`, usado pelo worker para criar a própria instância do ORM.
- `package.json`: onde entra o script de execução do worker.

## Referências

- Origem na reunião: [09:02] Marcos, [09:09] Diego, [09:09] Bruno, [09:10] Marcos, [09:10] Larissa, [09:11] Diego, [09:11] Larissa, [09:30] Bruno
- Catálogo: D-02, P-03, P-08
- Relacionados: [ADR-001](ADR-001-outbox-no-mysql.md), [ADR-008](ADR-008-ordering-single-worker-por-order-id.md)
