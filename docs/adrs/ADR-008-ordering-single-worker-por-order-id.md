# ADR-008: Usar um único worker, garantindo a ordem dos eventos apenas por pedido

- **Status:** Aceito
- **Data:** não informada (a transcrição registra apenas "quinta-feira, 09:00")
- **Decisores:** Larissa (Tech Lead), Diego (Eng. Sênior, Plataforma), Bruno (Eng. Pleno, Pedidos), Marcos (PM)
- **Decisões do catálogo:** D-03

## Contexto

Um pedido pode passar por várias mudanças de status em sequência rápida, e o cliente precisa receber os eventos desse pedido na ordem em que aconteceram ([09:12] Larissa).

## Decisão

Um **único worker** processa a outbox em ordem de criação dos eventos, o que garante a ordem **por pedido** ([09:12] Diego, [09:13] Larissa). **Não há garantia de ordem global** entre pedidos diferentes, e isso fica registrado como limitação conhecida ([09:13] Larissa, [09:14] Marcos).

A garantia discutida na reunião vem de processar a outbox em ordem de criação ([09:12] Diego). Como ela se comporta quando um evento entra em retry ([ADR-003](ADR-003-retry-backoff-exponencial-e-dlq.md)) **não foi discutido**; ver Consequências negativas.

## Alternativas consideradas

### Vários workers em paralelo desde já

- **Descrição:** processar a outbox com múltiplos workers para ganhar vazão.
- **Por que foi descartada:** perde a garantia de ordem. Preservá-la exigiria particionar por pedido ou usar lock pessimista, o que foi tratado como "problema do futuro" ([09:13] Bruno, [09:13] Diego).

## Consequências

### Positivas

- Não exige particionamento nem lock agora ([09:12] Diego, [09:13] Diego).
- Atende o que os clientes pediram: saber quando cada pedido deles mudou ([09:14] Marcos).

### Negativas

- Sem ordem global entre pedidos diferentes ([09:13] Larissa; catálogo P-04).
- **A ordem por pedido só vale enquanto não há retry.** Se a entrega de um evento falha, ele espera o backoff (de 1 minuto a 12 horas, conforme o [ADR-003](ADR-003-retry-backoff-exponencial-e-dlq.md)). Enquanto isso, o worker continua processando por ordem de criação, e um evento posterior do mesmo pedido pode chegar antes dele. Isso decorre de combinar [09:12] Diego (ordem de criação) com [09:17] Diego (backoff). A reunião não tratou desse caso; esta limitação é uma inferência registrada para revisão.
- **Questão em aberto:** quando um evento entra em retry, segurar os eventos seguintes do mesmo pedido até ele ser entregue ou ir para a DLQ (ordem estrita por pedido, ao custo de atrasar aquele pedido em até ~15 horas) ou aceitar a perda de ordem nesse caso? A favor de aceitar, os clientes querem saber se cada pedido mudou, sem que a ordem global tenha sido pedida ([09:14] Marcos). Decisão pendente com Larissa e Diego.
- A vazão fica limitada a um worker. Escalar para vários workers foi **adiado** e exigirá retrabalho ([09:13] Diego; catálogo X-04).
- A estratégia de escala (particionar por pedido ou usar lock pessimista) continua uma **questão em aberto** ([09:13] Diego; catálogo P-02).

**Trade-off:** aceitamos o teto de vazão de um único worker e a ausência de ordem global em troca de garantir, de forma simples, a ordem que importa ao cliente: a dos eventos de cada pedido, no caminho sem falhas. O comportamento com retry depende da questão em aberto acima.

## Referências

- Origem na reunião: [09:12] Larissa, [09:12] Diego, [09:13] Bruno, [09:13] Diego, [09:13] Larissa, [09:14] Marcos
- Catálogo: D-03, P-02, P-04, X-04
- Relacionados: [ADR-002](ADR-002-worker-separado-polling-2s.md), [ADR-003](ADR-003-retry-backoff-exponencial-e-dlq.md)
