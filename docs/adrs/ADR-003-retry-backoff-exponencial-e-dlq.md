# ADR-003: Fazer retry com backoff exponencial em 5 tentativas e mover falhas definitivas para uma DLQ

- **Status:** Aceito
- **Data:** não informada (a transcrição registra apenas "quinta-feira, 09:00")
- **Decisores:** Larissa (Tech Lead), Diego (Eng. Sênior, Plataforma), Bruno (Eng. Pleno, Pedidos), Marcos (PM), Sofia (Eng. Segurança)
- **Decisões do catálogo:** D-04, D-05, S-04

## Contexto

O endpoint do cliente pode estar fora do ar quando o worker tenta entregar o evento ([09:15] Diego). Um cliente já teve uma janela de indisponibilidade planejada de cerca de 2 horas ([09:16] Diego), então a política de nova tentativa precisa cobrir janelas desse tamanho sem deixar o evento pendurado para sempre ([09:15] Diego).

## Decisão

**Retry:** quando a entrega falha, o worker tenta de novo com backoff exponencial em **5 tentativas, nos intervalos 1min → 5min → 30min → 2h → 12h**, o que dá uma janela de cerca de 15 horas entre a primeira falha e a última tentativa ([09:15] Diego, [09:16] Larissa, [09:17] Diego, fechado em [09:17] Larissa).

**Timeout:** um cliente que não responde em **10 segundos** conta como falha e entra nesse mesmo fluxo de retry ([09:42] Sofia, [09:42] Diego).

**DLQ:** depois da quinta tentativa, o evento é considerado falha permanente e vai para uma **tabela de dead letter separada da outbox**, com o payload, o motivo da falha e o horário (chamada na reunião de `webhook_dead_letter`; nome final definido na especificação) ([09:17] Larissa, [09:18] Diego). O reprocessamento é **manual**, por um endpoint administrativo que devolve o evento à outbox como pendente (rota citada na reunião; definição final na especificação) ([09:18] Bruno, [09:18] Diego). Quem pode chamar esse endpoint está em [ADR-006](ADR-006-reuso-padroes-do-projeto.md).

## Alternativas consideradas

### Retry indefinido com backoff

- **Descrição:** continuar tentando sem limite de tentativas.
- **Por que foi descartada:** se o cliente sumiu, o evento fica pendurado para sempre ([09:15] Diego).

### Apenas 3 tentativas

- **Descrição:** política mais curta de retry.
- **Por que foi descartada:** cobre só cerca de 30 minutos, o que não basta para uma indisponibilidade planejada de 2 horas ([09:16] Bruno propôs; [09:16] Diego).

### Marcar como `failed` na própria outbox

- **Descrição:** manter os eventos esgotados na mesma tabela, só com outro status ([09:17] Larissa).
- **Por que foi descartada:** polui a leitura da outbox principal. Uma tabela separada deixa a leitura limpa e serve de evidência para debug ([09:18] Diego).

## Consequências

### Positivas

- Cobre janelas reais de indisponibilidade de até cerca de 15 horas ([09:17] Diego).
- Nenhum evento fica pendurado para sempre ([09:15] Diego).
- A outbox principal continua enxuta, e as falhas ficam auditáveis e reprocessáveis ([09:18] Diego).
- Um cliente lento não segura o worker por mais de 10 segundos por tentativa ([09:42] Diego).

### Negativas

- Um cliente fora do ar por mais de cerca de 15 horas perde os eventos desse período até alguém reprocessá-los manualmente, o que foi aceito ([09:17] Marcos).
- Não existe retry automático depois da DLQ; o reprocessamento depende de ação humana ([09:18] Diego).
- Mais uma tabela para modelar e manter (catálogo D-05).

**Trade-off:** aceitamos que falhas longas exijam reprocessamento manual em troca de uma política de entrega previsível, que não acumula eventos eternos na outbox.

## Ganchos com o código

- `prisma/schema.prisma`: onde entram o controle de tentativas na outbox e a tabela de dead letter.

## Referências

- Origem na reunião: [09:15] Diego, [09:15] Bruno, [09:16] Bruno, [09:16] Diego, [09:16] Larissa, [09:17] Diego, [09:17] Marcos, [09:17] Larissa, [09:18] Diego, [09:18] Bruno, [09:42] Sofia, [09:42] Diego
- Catálogo: D-04, D-05, S-04
- Relacionados: [ADR-001](ADR-001-outbox-no-mysql.md), [ADR-002](ADR-002-worker-separado-polling-2s.md), [ADR-005](ADR-005-at-least-once-x-event-id.md), [ADR-006](ADR-006-reuso-padroes-do-projeto.md)
