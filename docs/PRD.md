# PRD: Webhooks de notificação de mudança de status de pedidos

| Campo | Valor |
| --- | --- |
| **Autor** | `<autor>` |
| **Status** | Em revisão |
| **Data** | não informada (a reunião de origem registra apenas "quinta-feira, 09:00") |
| **Stakeholders** | Larissa (Tech Lead), Marcos (Product Manager), Bruno (Eng. Pleno, Pedidos), Diego (Eng. Sênior, Plataforma), Sofia (Eng. de Segurança) |
| **Documentos relacionados** | [RFC](RFC.md) · [FDD](FDD.md) · [ADRs](adrs/README.md) |

> **Legenda de origem:**
> - `([hh:mm] Nome)`: dito na reunião ([TRANSCRICAO.md](../TRANSCRICAO.md)).
> - Link: documento técnico.
> - `(estimativa do autor)` ou `(proposta)`: a validar na revisão.

## 1. Resumo e contexto

A plataforma passa a **avisar automaticamente os clientes B2B**, por uma chamada HTTP para uma URL que eles cadastram, sempre que o status de um pedido deles muda. Hoje eles precisam consultar a API repetidamente para descobrir mudanças.

O pedido veio formalmente de três clientes: Atlas Comercial, MaxDistribuição e Nova Cargo ([09:00] Marcos). A proposta técnica está no [RFC](RFC.md), e a especificação, no [FDD](FDD.md).

## 2. Problema e motivação

- **A integração atual é lenta e cara.** Os clientes consultam `GET /orders` de tempos em tempos para ver se algo mudou ([09:00] Marcos).
- **O problema tem peso comercial.** A Atlas sinalizou que pode migrar para um concorrente se a entrega não sair até o fim do trimestre ([09:00] Marcos).
- **O que os clientes pedem é tempo real**, que para eles significa qualquer latência abaixo de 10 segundos: "o importante é que não fique pendurado" ([09:02] Marcos).
- **Eles querem saber quando cada pedido muda.** Ordem global entre pedidos diferentes não foi pedida ([09:14] Marcos).

## 3. Público-alvo e cenários de uso

### Público-alvo

| Público | Necessidade | Origem |
| --- | --- | --- |
| **Clientes B2B integrados por API** (inicialmente Atlas, MaxDistribuição e Nova Cargo) | Receber as mudanças de status dos seus pedidos sem fazer polling | [09:00] Marcos |
| **Usuários que representam o cliente na API** | Cadastrar e gerenciar os próprios webhooks, autenticados com o JWT da plataforma | [09:32] Marcos |
| **Administradores da plataforma** | Reprocessar entregas que falharam definitivamente | [09:18] Diego, [09:36] Sofia |
| **Time de integração do cliente** | Validar que a notificação veio da plataforma e deduplicar repetições | [09:19] Sofia, [09:25] Diego |

### Cenários de uso

1. **Acompanhar entregas:** a Nova Cargo quer saber só quando um pedido é enviado ou entregue. Ela cadastra um webhook ouvindo SHIPPED e DELIVERED e passa a receber só esses eventos ([09:33] Marcos).
2. **Cliente em manutenção:** o endpoint de um cliente fica fora do ar por 2 horas numa manutenção planejada. A plataforma continua tentando entregar e conclui a entrega quando o endpoint volta, sem ação manual ([09:16] Diego).
3. **Falha prolongada:** um cliente fica fora por mais de ~15 horas. Os eventos vão para uma fila de falhas, e um administrador os reprocessa quando o cliente avisa que voltou ([09:17] Marcos, [09:18] Diego).
4. **Secret vazada:** um cliente percebe que vazou a secret em log. Ele pede uma secret nova e tem 24 horas para atualizar os sistemas, sem perder notificações ([09:21] Sofia, [09:22] Diego).
5. **Diagnóstico pelo próprio cliente:** o cliente consulta o histórico das últimas entregas para entender por que um evento não foi processado do lado dele ([09:34] Marcos).

## 4. Objetivos e métricas de sucesso

| ID | Objetivo | Métrica | Meta | Origem |
| --- | --- | --- | --- | --- |
| PRD-OBJ-01 | Notificar em "tempo real" | Tempo entre a mudança de status e a primeira tentativa de entrega | **< 10 s em 95% dos eventos**. O teto é de 2s no pior caso, mais o tempo de envio | Meta de 10s: [09:02] Marcos; percentil p95: (estimativa do autor) |
| PRD-OBJ-02 | Entregar no prazo comercial | Data de liberação em produção | **Até o fim de novembro, em 3 sprints**, com a revisão de segurança incluída | [09:45] Marcos, [09:46] Larissa |
| PRD-OBJ-03 | Entregas confiáveis | % de eventos entregues com sucesso, considerando as retentativas, sem cair na fila de falhas | **≥ 99%** | (estimativa do autor: as falhas definitivas devem ser exceção, restrita a clientes fora do ar por mais de ~15h) |
| PRD-OBJ-04 | Reduzir o polling dos clientes que pediram a feature | Volume de chamadas de listagem de pedidos feitas por esses clientes | **Queda de ≥ 50% em 60 dias** após o início do uso | (estimativa do autor: a dor citada é o polling ([09:00] Marcos); a meta precisa ser validada pelo PM) |
| PRD-OBJ-05 | Adoção pelos solicitantes | Clientes solicitantes com ao menos um webhook ativo | **3 de 3 em até 30 dias** após o lançamento | (estimativa do autor) |

As métricas técnicas que alimentam a PRD-OBJ-01 e a PRD-OBJ-03 estão no [FDD §10](FDD.md#10-observabilidade).

## 5. Escopo

### Incluso

- Cadastro, edição, remoção e listagem de webhooks por cliente, com escolha dos status de interesse.
- Notificação automática de mudança de status de pedido, assinada e com identificador único.
- Retentativas automáticas, fila de falhas definitivas e reprocessamento manual por administrador.
- Rotação de secret.
- Histórico de entregas consultável pelo cliente.

### Fora de escopo

| Item | Situação | Origem |
| --- | --- | --- |
| Webhooks **inbound** (o cliente enviar dados para a plataforma) | Descartado | [09:02] Marcos, [09:03] Sofia |
| **E-mail** avisando o cliente quando o webhook dele falha repetidamente | Próxima fase, depois de medir o impacto | [09:37] Larissa, [09:38] Marcos |
| **Painel visual** para o cliente gerenciar webhooks | Descartado nesta feature (projeto separado do time de frontend) | [09:40] Larissa |
| **Rate limiting** de envio por cliente | Observar e decidir depois | [09:39] Diego, [09:39] Larissa |
| Arquivamento das notificações já entregues | Adiado; fora desta feature | [09:08] Diego |
| Escala para vários processadores em paralelo | Adiado ("problema do futuro") | [09:13] Diego |
| Restringir quem pode configurar webhooks (hoje: qualquer usuário autenticado) | Adiado ("mais pra frente") | [09:37] Sofia |

## 6. Requisitos funcionais

| ID | Requisito | Origem | Especificação |
| --- | --- | --- | --- |
| PRD-FR-01 | O cliente cadastra um webhook informando a URL de destino e os status que quer receber. A plataforma gera a secret e a devolve na criação | [09:31] Marcos | [FDD §7.1](FDD.md#71-post-apiv1webhooks-cadastrar-webhook) |
| PRD-FR-02 | O cliente edita um webhook cadastrado | [09:33] Bruno | [FDD §7.4](FDD.md#74-patch-apiv1webhooksid-editar-webhook) |
| PRD-FR-03 | O cliente remove um webhook cadastrado | [09:33] Bruno | [FDD §7.5](FDD.md#75-delete-apiv1webhooksid-remover-webhook) |
| PRD-FR-04 | O cliente lista os webhooks cadastrados para ele | [09:33] Bruno | [FDD §7.2](FDD.md#72-get-apiv1customerscustomeridwebhooks-listar-webhooks-de-um-cliente) |
| PRD-FR-05 | Cada webhook recebe apenas as mudanças para os status escolhidos no cadastro | [09:33] Marcos, [09:34] Bruno | [FDD §6.1](FDD.md#61-criação-do-evento-na-outbox) |
| PRD-FR-06 | Toda mudança de status de um pedido gera uma notificação para os webhooks interessados do cliente dono do pedido | [09:00] Marcos, [09:06] Diego | [FDD §6.1](FDD.md#61-criação-do-evento-na-outbox), [FDD §6.2](FDD.md#62-processamento-pelo-worker) |
| PRD-FR-07 | A notificação traz o essencial da mudança: pedido, número, status anterior e novo, cliente, valor total e momento. Os itens do pedido não vão; o cliente consulta o pedido se quiser detalhes | [09:43] Diego, [09:44] Bruno | [FDD §7.10](FDD.md#710-contrato-de-saída-o-request-que-o-cliente-recebe) |
| PRD-FR-08 | Toda notificação é assinada, para que o cliente valide a origem e a integridade | [09:19] Sofia, [09:20] Sofia | [ADR-004](adrs/ADR-004-hmac-sha256-secret-por-endpoint.md) |
| PRD-FR-09 | Toda notificação leva um identificador único do evento, para o cliente descartar repetições, e o identificador do webhook que a originou | [09:25] Diego, [09:44] Sofia | [ADR-005](adrs/ADR-005-at-least-once-x-event-id.md), [FDD §7.10](FDD.md#710-contrato-de-saída-o-request-que-o-cliente-recebe) |
| PRD-FR-10 | Se o endpoint do cliente falha ou não responde, a plataforma tenta de novo automaticamente | [09:14] Larissa, [09:15] Diego | [ADR-003](adrs/ADR-003-retry-backoff-exponencial-e-dlq.md) |
| PRD-FR-11 | Notificações que esgotam as tentativas ficam guardadas numa fila de falhas, com o motivo | [09:17] Larissa, [09:18] Diego | [FDD §6.4](FDD.md#64-dlq) |
| PRD-FR-12 | Um administrador reprocessa manualmente uma notificação da fila de falhas, e a ação fica registrada para auditoria | [09:18] Diego, [09:36] Sofia | [FDD §6.5](FDD.md#65-replay-manual) |
| PRD-FR-13 | O cliente pede uma secret nova a qualquer momento. A antiga continua aceita por 24 horas | [09:21] Sofia | [FDD §6.6](FDD.md#66-rotação-de-secret) |
| PRD-FR-14 | O cliente consulta o histórico das entregas de um webhook: sucesso ou falha, conteúdo enviado, resposta e tempo de resposta | [09:34] Marcos | [FDD §7.7](FDD.md#77-get-apiv1webhooksiddeliveries-histórico-de-entregas) |

O FDD também especifica `GET /webhooks/:id` e a listagem da fila de falhas. São **propostas técnicas** do FDD e não requisitos discutidos na reunião, por isso não aparecem aqui.

## 7. Requisitos não funcionais

| ID | Categoria | Requisito | Origem |
| --- | --- | --- | --- |
| PRD-NFR-01 | Latência | A notificação sai em menos de 10 s depois da mudança de status; o polling interno leva até 2 s | [09:02] Marcos, [ADR-002](adrs/ADR-002-worker-separado-polling-2s.md) |
| PRD-NFR-02 | Consistência | Toda mudança de status gera notificação, e nenhuma notificação existe sem a mudança. Se o registro da notificação falhar, a mudança de status não acontece | [ADR-001](adrs/ADR-001-outbox-no-mysql.md), [ADR-007](adrs/ADR-007-integracao-changestatus-transacional.md) |
| PRD-NFR-03 | Garantia de entrega | Entrega **pelo menos uma vez**: o cliente pode receber repetições e deve descartá-las pelo identificador | [ADR-005](adrs/ADR-005-at-least-once-x-event-id.md) |
| PRD-NFR-04 | Resiliência | 5 retentativas, com intervalos de 1 min, 5 min, 30 min, 2 h e 12 h (~15 h no total); depois, a fila de falhas | [ADR-003](adrs/ADR-003-retry-backoff-exponencial-e-dlq.md) |
| PRD-NFR-05 | Resiliência | Um cliente que não responde em 10 s conta como falha | [09:42] Diego |
| PRD-NFR-06 | Ordem | Os eventos de um mesmo pedido chegam na ordem em que aconteceram, enquanto não há retentativa. Não há ordem garantida entre pedidos diferentes | [ADR-008](adrs/ADR-008-ordering-single-worker-por-order-id.md) |
| PRD-NFR-07 | Segurança | A assinatura usa HMAC-SHA256, com secret exclusiva por webhook e nunca global | [ADR-004](adrs/ADR-004-hmac-sha256-secret-por-endpoint.md) |
| PRD-NFR-08 | Segurança | Só são aceitas URLs HTTPS | [09:23] Sofia |
| PRD-NFR-09 | Segurança | Só administradores reprocessam a fila de falhas, com registro de quem executou | [09:36] Sofia, [09:36] Larissa |
| PRD-NFR-10 | Limites | Notificações acima de 64 KB não são enviadas: dão erro, sem truncamento | [09:23] Sofia, [09:24] Larissa |
| PRD-NFR-11 | Disponibilidade | Reiniciar a API não interrompe o envio de notificações | [09:11] Diego |
| PRD-NFR-12 | Manutenibilidade | A feature não adiciona infraestrutura nova e segue os padrões do projeto | [09:07] Diego, [ADR-006](adrs/ADR-006-reuso-padroes-do-projeto.md) |

## 8. Decisões e trade-offs principais

| Decisão | Trade-off aceito | ADR |
| --- | --- | --- |
| Registrar a notificação no próprio banco, junto com a mudança de status | Uma tabela a mais para manter, em troca de nunca perder nem inventar uma notificação | [ADR-001](adrs/ADR-001-outbox-no-mysql.md) |
| Processo de envio separado, verificando a cada 2 s | Até 2 s de atraso e um processo a mais para operar, em troca de simplicidade e independência da API | [ADR-002](adrs/ADR-002-worker-separado-polling-2s.md) |
| 5 retentativas em ~15 h, depois fila de falhas com reprocessamento manual | Falhas longas exigem ação humana, em troca de uma política previsível | [ADR-003](adrs/ADR-003-retry-backoff-exponencial-e-dlq.md) |
| Assinatura com secret por webhook e rotação com 24 h | Mais gestão de secrets, em troca de limitar um vazamento a um único cliente | [ADR-004](adrs/ADR-004-hmac-sha256-secret-por-endpoint.md) |
| Entrega pelo menos uma vez | O cliente precisa descartar repetições, em troca de entrega confiável e simples | [ADR-005](adrs/ADR-005-at-least-once-x-event-id.md) |
| Seguir os padrões existentes do projeto | Herdar as limitações atuais, em troca de consistência e velocidade | [ADR-006](adrs/ADR-006-reuso-padroes-do-projeto.md) |
| Falha ao registrar a notificação impede a mudança de status | Uma mudança de status pode falhar por causa da notificação, em troca de nunca haver mudança sem aviso | [ADR-007](adrs/ADR-007-integracao-changestatus-transacional.md) |
| Um único processador, com ordem por pedido | Vazão limitada e ordem que só vale sem retentativas, em troca de simplicidade | [ADR-008](adrs/ADR-008-ordering-single-worker-por-order-id.md) |

## 9. Dependências

| Dependência | Responsável | Origem |
| --- | --- | --- |
| Revisão de segurança do código (assinatura e geração de secret), com pelo menos 2 dias úteis antes do deploy | Sofia | [09:46] Sofia, [09:49] Sofia |
| Documentação no portal do desenvolvedor, com destaque para o descarte de repetições e para como integrar | Marcos | [09:26] Marcos, [09:40] Marcos |
| Confirmação do prazo com a Atlas e comunicação aos clientes | Marcos | [09:47] Marcos, [09:49] Marcos |
| Sessão de revisão do desenho com Bruno e Diego antes de codar | Larissa | [09:50] Larissa |
| Clientes: endpoint HTTPS, validação da assinatura e descarte de repetições | Clientes | [09:23] Sofia, [09:20] Sofia, [09:25] Diego |
| Operação de um processo novo (o processo de envio) em produção | Time de Plataforma | (proposta), derivada do [ADR-002](adrs/ADR-002-worker-separado-polling-2s.md) |

## 10. Riscos e mitigação

Na tabela, a probabilidade e o impacto são **estimativas do autor**, porque a reunião não classificou os riscos. Os riscos e as mitigações têm a origem indicada.

| ID | Risco | Probabilidade | Impacto | Mitigação | Origem |
| --- | --- | --- | --- | --- | --- |
| PRD-RISK-01 | Não entregar até o fim de novembro, e a Atlas migrar para o concorrente | Média | Alto | Estimativa de 3 sprints com a revisão incluída; o PM confirma o prazo com o cliente | [09:00] Marcos, [09:45] Marcos, [09:46] Larissa, [09:47] Marcos |
| PRD-RISK-02 | A revisão de segurança encontra problemas e atrasa o deploy | Média | Médio | Reservar pelo menos 2 dias úteis de revisão, já dentro da estimativa | [09:46] Sofia, [09:47] Larissa |
| PRD-RISK-03 | Um cliente não descarta repetições e processa o mesmo evento duas vezes | Média | Médio | Documentação destacada no portal | [09:25] Sofia, [09:26] Marcos |
| PRD-RISK-04 | Um cliente vaza a própria secret | Média (já aconteceu antes) | Alto | Secret por webhook e rotação com 24 h de convivência | [09:21] Sofia, [09:22] Diego |
| PRD-RISK-05 | Um cliente fica fora do ar por mais de ~15 h e as notificações não chegam | Baixa | Médio | Fila de falhas com reprocessamento manual | [09:17] Marcos, [09:18] Diego |
| PRD-RISK-06 | Uma rajada de mudanças de status sobrecarrega o endpoint de um cliente | Baixa | Médio | Observar e implementar limite se virar problema | [09:38] Diego, [09:39] Larissa |
| PRD-RISK-07 | Com retentativas, eventos do mesmo pedido chegam fora de ordem | Média | Baixo | Decisão em aberto (Q5); a notificação traz o status anterior e o novo, o que permite reconciliar | [ADR-008](adrs/ADR-008-ordering-single-worker-por-order-id.md) |

## 11. Critérios de aceitação

- [ ] **PRD-CA-01:** um cliente cadastra um webhook com URL HTTPS e status de interesse, e recebe a secret uma única vez (PRD-FR-01, PRD-NFR-08).
- [ ] **PRD-CA-02:** o cliente lista, edita e remove os próprios webhooks (PRD-FR-02, PRD-FR-03, PRD-FR-04).
- [ ] **PRD-CA-03:** ao mudar o status de um pedido para um status escolhido, o endpoint do cliente recebe a notificação em menos de 10 s. Para status não escolhidos, nada é enviado (PRD-FR-05, PRD-FR-06, PRD-NFR-01).
- [ ] **PRD-CA-04:** o cliente valida a assinatura com a secret dele e identifica o evento e o webhook de origem (PRD-FR-08, PRD-FR-09).
- [ ] **PRD-CA-05:** com o endpoint do cliente fora do ar por 2 h, a notificação é entregue depois que ele volta, sem ação manual (PRD-FR-10, PRD-NFR-04).
- [ ] **PRD-CA-06:** depois de esgotadas as tentativas, a notificação aparece na fila de falhas. Um administrador a reprocessa, e um operador não consegue (PRD-FR-11, PRD-FR-12, PRD-NFR-09).
- [ ] **PRD-CA-07:** depois de pedir uma secret nova, o cliente continua validando notificações com a antiga por 24 h (PRD-FR-13).
- [ ] **PRD-CA-08:** o cliente vê no histórico cada tentativa de entrega, com resultado, conteúdo, resposta e tempo (PRD-FR-14).
- [ ] **PRD-CA-09:** uma falha ao registrar a notificação impede a mudança de status (PRD-NFR-02).
- [ ] **PRD-CA-10:** os critérios técnicos do [FDD §13](FDD.md#13-critérios-de-aceite-técnicos) passam.

## 12. Estratégia de testes e validação

**Antes do deploy:**
- Testes automatizados de API e do processo de envio, com cada critério técnico do [FDD §13](FDD.md#13-critérios-de-aceite-técnicos) virando teste no padrão dos testes existentes do projeto.
- Testes ponta a ponta da integração com a mudança de status, já previstos na estimativa ([09:46] Larissa).
- Revisão do desenho por Bruno e Diego antes de começar a codar ([09:50] Larissa).
- Revisão de segurança da Sofia, com pelo menos 2 dias úteis, como etapa obrigatória antes do deploy ([09:46] Sofia, [09:47] Larissa).

**No lançamento (proposta):**
- Piloto com os três clientes solicitantes, que cadastram endpoints de homologação.
- Validação dos cenários 1, 2 e 4 da seção 3 junto com eles.

**Depois do lançamento:**
- Acompanhar as métricas do [FDD §10](FDD.md#10-observabilidade): idade da notificação pendente mais antiga (PRD-OBJ-01), taxa de sucesso e volume da fila de falhas (PRD-OBJ-03), e volume de envios por cliente, que serve de base para decidir o rate limiting.
- Medir o polling dos clientes para a PRD-OBJ-04 **(proposta)**.

## 13. Questões em aberto

As questões vêm do [RFC](RFC.md#questões-em-aberto) e do [FDD](FDD.md#15-questões-em-aberto).

| # | Questão | Origem |
| --- | --- | --- |
| Q1 | Limitar a taxa de envio por cliente? | [09:38] Diego, [09:39] Larissa |
| Q2 | Como escalar o processo de envio sem perder a ordem por pedido? | [09:13] Diego |
| Q3 | Por quanto tempo guardar as notificações já entregues? | [09:08] Diego |
| Q4 | Quando restringir quem pode configurar webhooks? | [09:37] Sofia |
| Q5 | Com retentativas, segurar os eventos seguintes do pedido (ordem estrita, com atraso de até ~15 h) ou aceitar a inversão? | [ADR-008](adrs/ADR-008-ordering-single-worker-por-order-id.md) |
| Q6 | O prazo é "fim do trimestre" ([09:00] Marcos) ou "fim de novembro" ([09:45] Marcos)? Este PRD usa a fala mais recente | Divergência na reunião |
