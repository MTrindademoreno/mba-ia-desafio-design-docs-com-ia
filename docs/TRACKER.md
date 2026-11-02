# Tracker de rastreabilidade

Este tracker liga cada item identificável dos documentos ([PRD](PRD.md), [RFC](RFC.md), [FDD](FDD.md) e [ADRs](adrs/README.md)) à sua origem. A origem é uma fala da reunião ([TRANSCRICAO.md](../TRANSCRICAO.md)) ou um arquivo real do código.

**Como ler a tabela:**
- **IDs:** os que já existem nos documentos foram mantidos (`PRD-FR-01`, `ADR-002`). Os demais foram atribuídos aqui, no padrão `<DOC>-<TIPO>-NN`, numerados na ordem em que o item aparece no documento:
  - `CTX`: contexto
  - `PROP`: proposta
  - `ALT`: alternativa
  - `ESC`: fora de escopo
  - `Q`: questão em aberto
  - `IMP`: impacto
  - `OT`: objetivo técnico
  - `DADOS`: modelo de dados
  - `FLUXO`: fluxo
  - `CONTRATO`: contrato público
  - `ERRO`: erro
  - `RES`: resiliência
  - `OBS`: observabilidade
  - `INTEG`: integração
  - `NOVO`: arquivo novo
  - `DEP`: dependência
  - `CA`: critério de aceite
  - `RISK`: risco
  - `DEC`: decisão
  - `TEST`: teste
  - `TO`: trade-off
  - `COD`: gancho com o código
- **IDs do FDD:** os itens que já têm ID curto no FDD ganharam o prefixo `FDD-` aqui, para não se confundirem com os do PRD. Assim, `FDD-OT-01` corresponde a OT-1, `FDD-CA-01` a CA-01, `FDD-RISK-01` a R-1 e `FDD-Q-01` a Q1.
- **`(proposta derivada)`:** o item foi especificado pelo autor do documento, não pela reunião. A linha aponta para a decisão ou para a convenção de código da qual ele deriva.
- **`(inferência)`:** consequência lógica de duas falas que a reunião não discutiu em conjunto.
- **Itens sem origem:** os que não têm origem nenhuma não recebem âncora e estão listados em [Itens sem origem direta](#itens-sem-origem-direta).

| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |
| --- | --- | --- | --- | --- | --- |
| PRD-OBJ-01 | docs/PRD.md | Objetivo | Notificar em < 10 s (meta da reunião; o recorte p95 é proposta derivada) | TRANSCRICAO | [09:02] Marcos |
| PRD-OBJ-02 | docs/PRD.md | Objetivo | Entregar até o fim de novembro, em 3 sprints | TRANSCRICAO | [09:45] Marcos; [09:46] Larissa |
| PRD-OBJ-04 | docs/PRD.md | Objetivo | Reduzir o polling dos solicitantes em ≥ 50% (proposta derivada da dor citada) | TRANSCRICAO | [09:00] Marcos |
| PRD-OBJ-05 | docs/PRD.md | Objetivo | 3 de 3 solicitantes com webhook ativo em 30 dias (proposta derivada) | TRANSCRICAO | [09:00] Marcos |
| PRD-ESC-01 | docs/PRD.md | Fora de Escopo | Webhooks inbound: descartado | TRANSCRICAO | [09:02] Marcos; [09:03] Sofia |
| PRD-ESC-02 | docs/PRD.md | Fora de Escopo | E-mail de alerta de falha: próxima fase | TRANSCRICAO | [09:37] Larissa; [09:38] Marcos |
| PRD-ESC-03 | docs/PRD.md | Fora de Escopo | Painel visual: projeto separado do frontend | TRANSCRICAO | [09:40] Larissa |
| PRD-ESC-04 | docs/PRD.md | Fora de Escopo | Rate limiting: observar e decidir depois | TRANSCRICAO | [09:39] Diego; [09:39] Larissa |
| PRD-ESC-05 | docs/PRD.md | Fora de Escopo | Arquivamento das notificações entregues: adiado | TRANSCRICAO | [09:08] Diego |
| PRD-ESC-06 | docs/PRD.md | Fora de Escopo | Vários processadores em paralelo: adiado | TRANSCRICAO | [09:13] Diego |
| PRD-ESC-07 | docs/PRD.md | Fora de Escopo | Restringir quem configura webhooks: adiado | TRANSCRICAO | [09:37] Sofia |
| PRD-FR-01 | docs/PRD.md | Requisito Funcional | Cadastrar webhook (URL + status); a secret é gerada pela plataforma | TRANSCRICAO | [09:31] Marcos |
| PRD-FR-02 | docs/PRD.md | Requisito Funcional | Editar webhook | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-03 | docs/PRD.md | Requisito Funcional | Remover webhook | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-04 | docs/PRD.md | Requisito Funcional | Listar os webhooks do cliente | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-05 | docs/PRD.md | Requisito Funcional | Receber apenas os status escolhidos | TRANSCRICAO | [09:33] Marcos; [09:34] Bruno |
| PRD-FR-06 | docs/PRD.md | Requisito Funcional | Toda mudança de status gera notificação aos webhooks interessados | TRANSCRICAO | [09:00] Marcos; [09:06] Diego |
| PRD-FR-07 | docs/PRD.md | Requisito Funcional | Notificação com o essencial do pedido, sem itens | TRANSCRICAO | [09:43] Diego; [09:44] Bruno |
| PRD-FR-08 | docs/PRD.md | Requisito Funcional | Notificação assinada (origem e integridade) | TRANSCRICAO | [09:19] Sofia; [09:20] Sofia |
| PRD-FR-09 | docs/PRD.md | Requisito Funcional | Identificador único do evento e do webhook de origem | TRANSCRICAO | [09:25] Diego; [09:44] Sofia |
| PRD-FR-10 | docs/PRD.md | Requisito Funcional | Retentativa automática em caso de falha | TRANSCRICAO | [09:14] Larissa; [09:15] Diego |
| PRD-FR-11 | docs/PRD.md | Requisito Funcional | Fila de falhas guardando o motivo | TRANSCRICAO | [09:17] Larissa; [09:18] Diego |
| PRD-FR-12 | docs/PRD.md | Requisito Funcional | Reprocessamento manual por admin, com auditoria | TRANSCRICAO | [09:18] Diego; [09:36] Sofia |
| PRD-FR-13 | docs/PRD.md | Requisito Funcional | Rotação de secret, com a antiga válida por 24 h | TRANSCRICAO | [09:21] Sofia |
| PRD-FR-14 | docs/PRD.md | Requisito Funcional | Histórico de entregas do webhook | TRANSCRICAO | [09:34] Marcos |
| PRD-NFR-01 | docs/PRD.md | Requisito Não Funcional | Latência < 10 s; polling de 2 s | TRANSCRICAO | [09:02] Marcos; [09:09] Diego; [09:10] Larissa |
| PRD-NFR-02 | docs/PRD.md | Requisito Não Funcional | Nenhuma mudança de status sem notificação, e vice-versa | TRANSCRICAO | [09:06] Diego; [09:40] Bruno; [09:41] Diego |
| PRD-NFR-03 | docs/PRD.md | Requisito Não Funcional | Entrega pelo menos uma vez | TRANSCRICAO | [09:24] Diego; [09:26] Larissa |
| PRD-NFR-04 | docs/PRD.md | Requisito Não Funcional | 5 retentativas com intervalos de 1m/5m/30m/2h/12h | TRANSCRICAO | [09:17] Diego; [09:17] Larissa |
| PRD-NFR-05 | docs/PRD.md | Requisito Não Funcional | Timeout de 10 s | TRANSCRICAO | [09:42] Diego |
| PRD-NFR-06 | docs/PRD.md | Requisito Não Funcional | Ordem por pedido sem retentativa; sem ordem global | TRANSCRICAO | [09:12] Diego; [09:13] Larissa |
| PRD-NFR-07 | docs/PRD.md | Requisito Não Funcional | HMAC-SHA256 com secret por webhook | TRANSCRICAO | [09:20] Sofia; [09:21] Sofia |
| PRD-NFR-08 | docs/PRD.md | Requisito Não Funcional | Apenas URLs HTTPS | TRANSCRICAO | [09:23] Sofia |
| PRD-NFR-09 | docs/PRD.md | Requisito Não Funcional | Reprocessamento só por admin, com registro | TRANSCRICAO | [09:36] Sofia; [09:36] Larissa |
| PRD-NFR-10 | docs/PRD.md | Requisito Não Funcional | Limite de 64 KB, com erro e sem truncar | TRANSCRICAO | [09:23] Sofia; [09:24] Larissa |
| PRD-NFR-11 | docs/PRD.md | Requisito Não Funcional | Reiniciar a API não interrompe envios | TRANSCRICAO | [09:11] Diego |
| PRD-NFR-12 | docs/PRD.md | Requisito Não Funcional | Sem infraestrutura nova; padrões do projeto | TRANSCRICAO | [09:07] Diego; [09:30] Larissa |
| PRD-DEC-01 | docs/PRD.md | Decisão | Registrar a notificação no banco, junto com a mudança (ADR-001) | TRANSCRICAO | [09:08] Larissa |
| PRD-DEC-02 | docs/PRD.md | Decisão | Processo separado com polling de 2 s (ADR-002) | TRANSCRICAO | [09:10] Larissa; [09:11] Diego |
| PRD-DEC-03 | docs/PRD.md | Decisão | Retentativas + fila de falhas com reprocessamento manual (ADR-003) | TRANSCRICAO | [09:17] Larissa; [09:18] Diego |
| PRD-DEC-04 | docs/PRD.md | Decisão | Secret por webhook com rotação de 24 h (ADR-004) | TRANSCRICAO | [09:22] Sofia |
| PRD-DEC-05 | docs/PRD.md | Decisão | Entrega pelo menos uma vez (ADR-005) | TRANSCRICAO | [09:26] Larissa |
| PRD-DEC-06 | docs/PRD.md | Decisão | Seguir os padrões do projeto (ADR-006) | TRANSCRICAO | [09:30] Larissa |
| PRD-DEC-07 | docs/PRD.md | Decisão | Falha ao registrar impede a mudança de status (ADR-007) | TRANSCRICAO | [09:40] Bruno; [09:41] Diego |
| PRD-DEC-08 | docs/PRD.md | Decisão | Um processador, com ordem por pedido (ADR-008) | TRANSCRICAO | [09:12] Diego; [09:13] Larissa |
| PRD-DEP-01 | docs/PRD.md | Dependência | Revisão de segurança, ≥ 2 dias úteis antes do deploy | TRANSCRICAO | [09:46] Sofia; [09:49] Sofia |
| PRD-DEP-02 | docs/PRD.md | Dependência | Documentação no portal do desenvolvedor | TRANSCRICAO | [09:26] Marcos; [09:40] Marcos |
| PRD-DEP-03 | docs/PRD.md | Dependência | Confirmação do prazo com a Atlas e comunicação aos clientes | TRANSCRICAO | [09:47] Marcos; [09:49] Marcos |
| PRD-DEP-04 | docs/PRD.md | Dependência | Revisão do desenho com Bruno e Diego antes de codar | TRANSCRICAO | [09:50] Larissa |
| PRD-DEP-05 | docs/PRD.md | Dependência | Clientes: HTTPS, validar a assinatura, descartar repetições | TRANSCRICAO | [09:23] Sofia; [09:20] Sofia; [09:25] Diego |
| PRD-DEP-06 | docs/PRD.md | Dependência | Operar um processo novo em produção (proposta derivada) | TRANSCRICAO | [09:11] Diego |
| PRD-RISK-01 | docs/PRD.md | Risco | Perder o prazo e a Atlas migrar | TRANSCRICAO | [09:00] Marcos; [09:45] Marcos; [09:46] Larissa |
| PRD-RISK-02 | docs/PRD.md | Risco | Revisão de segurança atrasar o deploy | TRANSCRICAO | [09:46] Sofia; [09:47] Larissa |
| PRD-RISK-03 | docs/PRD.md | Risco | Cliente não descartar repetições | TRANSCRICAO | [09:25] Sofia; [09:26] Marcos |
| PRD-RISK-04 | docs/PRD.md | Risco | Cliente vazar a secret | TRANSCRICAO | [09:21] Sofia; [09:22] Diego |
| PRD-RISK-05 | docs/PRD.md | Risco | Cliente fora do ar por > 15 h | TRANSCRICAO | [09:17] Marcos; [09:18] Diego |
| PRD-RISK-06 | docs/PRD.md | Risco | Rajada sobrecarregar o endpoint do cliente | TRANSCRICAO | [09:38] Diego; [09:39] Larissa |
| PRD-RISK-07 | docs/PRD.md | Risco | Eventos fora de ordem com retentativa (inferência) | TRANSCRICAO | [09:12] Diego; [09:17] Diego |
| PRD-CA-01 | docs/PRD.md | Critério de Aceite | Cadastro com HTTPS; secret entregue uma vez | TRANSCRICAO | [09:31] Marcos; [09:23] Sofia |
| PRD-CA-02 | docs/PRD.md | Critério de Aceite | Listar, editar e remover webhooks | TRANSCRICAO | [09:33] Bruno |
| PRD-CA-03 | docs/PRD.md | Critério de Aceite | Notificação em < 10 s só para os status escolhidos | TRANSCRICAO | [09:33] Marcos; [09:02] Marcos |
| PRD-CA-04 | docs/PRD.md | Critério de Aceite | Validar a assinatura e identificar evento e webhook | TRANSCRICAO | [09:20] Sofia; [09:25] Diego; [09:44] Sofia |
| PRD-CA-05 | docs/PRD.md | Critério de Aceite | Entrega concluída depois de 2 h fora do ar | TRANSCRICAO | [09:16] Diego; [09:17] Diego |
| PRD-CA-06 | docs/PRD.md | Critério de Aceite | Fila de falhas; reprocessamento só por admin | TRANSCRICAO | [09:18] Diego; [09:36] Sofia |
| PRD-CA-07 | docs/PRD.md | Critério de Aceite | Secret antiga válida por 24 h após a rotação | TRANSCRICAO | [09:21] Sofia |
| PRD-CA-08 | docs/PRD.md | Critério de Aceite | Histórico mostra cada tentativa | TRANSCRICAO | [09:34] Marcos |
| PRD-CA-09 | docs/PRD.md | Critério de Aceite | Falha ao registrar impede a mudança de status | TRANSCRICAO | [09:40] Bruno |
| PRD-TEST-01 | docs/PRD.md | Estratégia de Teste | Testes automatizados no padrão dos testes existentes (proposta derivada) | CODIGO | `tests/orders.test.ts`; `tests/setup.ts` |
| PRD-TEST-02 | docs/PRD.md | Estratégia de Teste | Testes ponta a ponta da integração | TRANSCRICAO | [09:46] Larissa |
| PRD-TEST-03 | docs/PRD.md | Estratégia de Teste | Revisão do desenho antes de codar | TRANSCRICAO | [09:50] Larissa |
| PRD-TEST-04 | docs/PRD.md | Estratégia de Teste | Revisão de segurança obrigatória antes do deploy | TRANSCRICAO | [09:46] Sofia; [09:47] Larissa |
| PRD-TEST-06 | docs/PRD.md | Estratégia de Teste | Monitorar as métricas pós-lançamento, inclusive para o rate limiting (proposta derivada) | TRANSCRICAO | [09:39] Diego |
| PRD-Q-01 | docs/PRD.md | Questão em Aberto | Limitar a taxa de envio por cliente? | TRANSCRICAO | [09:38] Diego; [09:39] Larissa |
| PRD-Q-02 | docs/PRD.md | Questão em Aberto | Como escalar sem perder a ordem? | TRANSCRICAO | [09:13] Diego |
| PRD-Q-03 | docs/PRD.md | Questão em Aberto | Retenção das notificações entregues | TRANSCRICAO | [09:08] Diego |
| PRD-Q-04 | docs/PRD.md | Questão em Aberto | Quando restringir a configuração? | TRANSCRICAO | [09:37] Sofia |
| PRD-Q-05 | docs/PRD.md | Questão em Aberto | Ordem com retentativa: segurar ou aceitar a inversão (inferência) | TRANSCRICAO | [09:12] Diego; [09:17] Diego; [09:14] Marcos |
| PRD-Q-06 | docs/PRD.md | Questão em Aberto | Prazo: fim do trimestre ou fim de novembro? | TRANSCRICAO | [09:00] Marcos; [09:45] Marcos |
| RFC-CTX-01 | docs/RFC.md | Contexto | Clientes fazem polling em GET /orders; a integração é lenta e cara | TRANSCRICAO | [09:00] Marcos |
| RFC-CTX-02 | docs/RFC.md | Premissa | "Tempo real" = abaixo de 10 s | TRANSCRICAO | [09:02] Marcos |
| RFC-CTX-03 | docs/RFC.md | Restrição | Somente outbound | TRANSCRICAO | [09:02] Marcos; [09:03] Sofia |
| RFC-CTX-04 | docs/RFC.md | Restrição | A transação de mudança de status já é pesada | TRANSCRICAO | [09:04] Bruno |
| RFC-CTX-05 | docs/RFC.md | Restrição | A transação é o `$transaction` de `changeStatus` | CODIGO | `src/modules/orders/order.service.ts` |
| RFC-CTX-06 | docs/RFC.md | Restrição | Time pequeno; infraestrutura nova é exagero | TRANSCRICAO | [09:07] Diego |
| RFC-CTX-07 | docs/RFC.md | Restrição | Seguir os padrões estabelecidos do projeto | TRANSCRICAO | [09:27] Bruno; [09:30] Larissa |
| RFC-PROP-01 | docs/RFC.md | Proposta | Outbox transacional com snapshot do payload | TRANSCRICAO | [09:06] Diego; [09:08] Larissa; [09:52] Diego |
| RFC-PROP-02 | docs/RFC.md | Proposta | Gravação por função pura recebendo o client da transação | TRANSCRICAO | [09:41] Bruno; [09:41] Diego |
| RFC-PROP-03 | docs/RFC.md | Proposta | Worker em processo próprio com polling de 2 s | TRANSCRICAO | [09:09] Diego; [09:11] Diego |
| RFC-PROP-04 | docs/RFC.md | Proposta | Um worker; ordem por pedido no caminho sem falhas | TRANSCRICAO | [09:12] Diego; [09:13] Larissa |
| RFC-PROP-05 | docs/RFC.md | Proposta | Timeout de 10 s, 5 retentativas, DLQ e replay ADMIN | TRANSCRICAO | [09:42] Diego; [09:17] Larissa; [09:36] Sofia |
| RFC-PROP-06 | docs/RFC.md | Proposta | HMAC-SHA256 com secret por endpoint e rotação de 24 h | TRANSCRICAO | [09:22] Sofia |
| RFC-PROP-07 | docs/RFC.md | Proposta | At-least-once com X-Event-Id | TRANSCRICAO | [09:25] Diego; [09:26] Larissa |
| RFC-PROP-08 | docs/RFC.md | Proposta | Módulo segue os padrões; CRUD aceita qualquer autenticado | TRANSCRICAO | [09:30] Larissa; [09:37] Sofia |
| RFC-RNF-01 | docs/RFC.md | Requisito Não Funcional | URL HTTPS obrigatória | TRANSCRICAO | [09:23] Sofia |
| RFC-RNF-02 | docs/RFC.md | Requisito Não Funcional | Limite de 64 KB, com erro | TRANSCRICAO | [09:24] Larissa |
| RFC-ESC-01 | docs/RFC.md | Fora de Escopo | Webhooks inbound | TRANSCRICAO | [09:02] Marcos |
| RFC-ESC-02 | docs/RFC.md | Fora de Escopo | Painel visual | TRANSCRICAO | [09:40] Larissa |
| RFC-ESC-03 | docs/RFC.md | Fora de Escopo | E-mail de alerta | TRANSCRICAO | [09:37] Larissa |
| RFC-ESC-04 | docs/RFC.md | Fora de Escopo | Arquivamento da outbox | TRANSCRICAO | [09:08] Diego |
| RFC-ESC-05 | docs/RFC.md | Fora de Escopo | Vários workers em paralelo | TRANSCRICAO | [09:13] Diego |
| RFC-ALT-01 | docs/RFC.md | Alternativa Descartada | Disparo síncrono no service de pedidos | TRANSCRICAO | [09:04] Bruno; [09:06] Diego |
| RFC-ALT-02 | docs/RFC.md | Alternativa Descartada | Fila dedicada (Redis Streams) | TRANSCRICAO | [09:07] Larissa; [09:07] Diego |
| RFC-ALT-03 | docs/RFC.md | Alternativa Descartada | Trigger de banco reativo | TRANSCRICAO | [09:09] Diego |
| RFC-ALT-04 | docs/RFC.md | Alternativa Descartada | Worker dentro do processo da API | TRANSCRICAO | [09:11] Diego |
| RFC-ALT-05 | docs/RFC.md | Alternativa Descartada | Entrega exactly-once | TRANSCRICAO | [09:25] Diego |
| RFC-Q-01 | docs/RFC.md | Questão em Aberto | Rate limiting por cliente | TRANSCRICAO | [09:38] Diego; [09:39] Larissa |
| RFC-Q-02 | docs/RFC.md | Questão em Aberto | Escala: particionar por pedido ou lock pessimista | TRANSCRICAO | [09:13] Bruno; [09:13] Diego |
| RFC-Q-03 | docs/RFC.md | Questão em Aberto | Política de arquivamento da outbox | TRANSCRICAO | [09:08] Diego |
| RFC-Q-04 | docs/RFC.md | Questão em Aberto | Restringir a autorização do CRUD | TRANSCRICAO | [09:36] Marcos; [09:37] Sofia |
| RFC-Q-05 | docs/RFC.md | Questão em Aberto | Ordem por pedido com retry (inferência) | TRANSCRICAO | [09:12] Diego; [09:17] Diego |
| RFC-IMP-01 | docs/RFC.md | Integração | `changeStatus` chama a gravação do evento na transação | CODIGO | `src/modules/orders/order.service.ts` |
| RFC-IMP-02 | docs/RFC.md | Integração | Novas tabelas no schema | CODIGO | `prisma/schema.prisma` |
| RFC-IMP-03 | docs/RFC.md | Integração | Ponto de entrada novo, análogo ao servidor, com client próprio | CODIGO | `src/server.ts`; `src/config/database.ts`; `package.json` |
| RFC-IMP-04 | docs/RFC.md | Integração | Módulo espelhando orders; middlewares e logger sem mudança | CODIGO | `src/modules/orders/`; `src/middlewares/error.middleware.ts`; `src/shared/logger/index.ts`; `src/middlewares/auth.middleware.ts` |
| RFC-IMP-05 | docs/RFC.md | Impacto | Um processo a mais para operar | TRANSCRICAO | [09:11] Diego |
| RFC-RISK-01 | docs/RFC.md | Risco | Prazo da Atlas / migração para o concorrente | TRANSCRICAO | [09:00] Marcos; [09:45] Marcos; [09:46] Larissa; [09:47] Marcos |
| RFC-RISK-02 | docs/RFC.md | Risco | Revisão de segurança bloqueia o deploy | TRANSCRICAO | [09:46] Sofia; [09:47] Larissa |
| RFC-RISK-03 | docs/RFC.md | Risco | Evento duplicado no cliente | TRANSCRICAO | [09:25] Sofia; [09:26] Marcos |
| RFC-RISK-04 | docs/RFC.md | Risco | Cliente fora por mais de ~15 h | TRANSCRICAO | [09:17] Marcos; [09:18] Diego |
| RFC-RISK-05 | docs/RFC.md | Risco | Vazamento de secret pelo cliente | TRANSCRICAO | [09:21] Sofia; [09:22] Diego |
| RFC-RISK-06 | docs/RFC.md | Risco | Rajadas de envio | TRANSCRICAO | [09:38] Diego; [09:39] Larissa |
| RFC-RISK-07 | docs/RFC.md | Risco | Eventos fora de ordem com retry (inferência) | TRANSCRICAO | [09:12] Diego; [09:17] Diego |
| RFC-RISK-08 | docs/RFC.md | Risco | Vazão limitada a um worker | TRANSCRICAO | [09:13] Diego |
| FDD-OT-01 | docs/FDD.md | Objetivo | Primeira tentativa em < 10 s | TRANSCRICAO | [09:02] Marcos; [09:09] Diego |
| FDD-OT-02 | docs/FDD.md | Objetivo | Atomicidade entre status e evento | TRANSCRICAO | [09:06] Diego; [09:40] Bruno |
| FDD-OT-03 | docs/FDD.md | Objetivo | At-least-once com X-Event-Id estável | TRANSCRICAO | [09:24] Diego; [09:25] Diego |
| FDD-OT-04 | docs/FDD.md | Objetivo | Ordem por pedido no caminho sem falhas | TRANSCRICAO | [09:12] Diego; [09:13] Larissa |
| FDD-OT-05 | docs/FDD.md | Objetivo | Retry, timeout, DLQ e replay | TRANSCRICAO | [09:17] Diego; [09:42] Diego; [09:18] Diego |
| FDD-OT-06 | docs/FDD.md | Objetivo | HMAC com secret por endpoint e rotação | TRANSCRICAO | [09:20] Sofia; [09:21] Sofia |
| FDD-OT-07 | docs/FDD.md | Objetivo | Sem alteração nos middlewares existentes | TRANSCRICAO | [09:29] Bruno |
| FDD-ESC-01 | docs/FDD.md | Fora de Escopo | Webhooks inbound | TRANSCRICAO | [09:02] Marcos |
| FDD-ESC-02 | docs/FDD.md | Fora de Escopo | E-mail de alerta | TRANSCRICAO | [09:37] Larissa |
| FDD-ESC-03 | docs/FDD.md | Fora de Escopo | Painel visual | TRANSCRICAO | [09:40] Larissa |
| FDD-ESC-04 | docs/FDD.md | Fora de Escopo | Arquivamento da outbox | TRANSCRICAO | [09:08] Diego |
| FDD-ESC-05 | docs/FDD.md | Fora de Escopo | Vários workers | TRANSCRICAO | [09:13] Diego |
| FDD-ESC-06 | docs/FDD.md | Fora de Escopo | Rate limiting de saída | TRANSCRICAO | [09:39] Larissa |
| FDD-ESC-07 | docs/FDD.md | Fora de Escopo | Autorização mais restrita do CRUD | TRANSCRICAO | [09:37] Sofia |
| FDD-DADOS-01 | docs/FDD.md | Restrição | Convenções de modelagem: uuid Char(36), camelCase, @@map | CODIGO | `prisma/schema.prisma` |
| FDD-DADOS-02 | docs/FDD.md | Decisão | Estados da outbox: pendente, processando, entregue, falhou | TRANSCRICAO | [09:08] Diego |
| FDD-DADOS-03 | docs/FDD.md | Decisão | Config. de webhook com url, secret, customer_id e estado ativo | TRANSCRICAO | [09:21] Bruno; [09:21] Sofia |
| FDD-DADOS-04 | docs/FDD.md | Decisão | Outbox com payload snapshot e id UUID = X-Event-Id | TRANSCRICAO | [09:25] Diego; [09:51] Larissa; [09:52] Larissa |
| FDD-DADOS-05 | docs/FDD.md | Modelo de Dados | Tabela de histórico de entregas (proposta derivada) | TRANSCRICAO | [09:34] Marcos |
| FDD-DADOS-06 | docs/FDD.md | Decisão | Dead letter com payload, motivo e horário | TRANSCRICAO | [09:18] Diego |
| FDD-DADOS-07 | docs/FDD.md | Modelo de Dados | Índices em status e created_at | TRANSCRICAO | [09:08] Diego |
| FDD-DADOS-08 | docs/FDD.md | Modelo de Dados | Uma linha de outbox por endpoint (proposta derivada) | TRANSCRICAO | [09:44] Sofia |
| FDD-DADOS-09 | docs/FDD.md | Modelo de Dados | `events` como Json, sem array de enum no MySQL (proposta derivada) | CODIGO | `prisma/schema.prisma` |
| FDD-DADOS-10 | docs/FDD.md | Modelo de Dados | Linha FAILED mantida para o replay reaproveitar o id (proposta derivada) | TRANSCRICAO | [09:18] Diego |
| FDD-FLUXO-01 | docs/FDD.md | Fluxo | `publishWebhookEvent(tx, ...)` após o histórico em `changeStatus` | TRANSCRICAO | [09:40] Bruno; [09:41] Bruno |
| FDD-FLUXO-02 | docs/FDD.md | Fluxo | Filtro por status na inserção | TRANSCRICAO | [09:34] Bruno; [09:34] Diego |
| FDD-FLUXO-03 | docs/FDD.md | Fluxo | Falha na outbox desfaz a mudança de status | TRANSCRICAO | [09:40] Bruno; [09:41] Diego |
| FDD-FLUXO-04 | docs/FDD.md | Fluxo | Worker: polling de 2 s em ordem de created_at | TRANSCRICAO | [09:09] Diego; [09:12] Diego |
| FDD-FLUXO-05 | docs/FDD.md | Fluxo | Recuperação de linhas presas em PROCESSING (proposta derivada do at-least-once) | TRANSCRICAO | [09:24] Diego |
| FDD-FLUXO-06 | docs/FDD.md | Fluxo | Payload > 64 KB vai direto para a DLQ (proposta derivada) | TRANSCRICAO | [09:23] Sofia; [09:24] Larissa |
| FDD-FLUXO-07 | docs/FDD.md | Fluxo | Retry com backoff 1m/5m/30m/2h/12h | TRANSCRICAO | [09:17] Diego; [09:17] Larissa |
| FDD-FLUXO-08 | docs/FDD.md | Fluxo | Interpretação: tentativa inicial + 5 retentativas (~14h36) | TRANSCRICAO | [09:17] Diego |
| FDD-FLUXO-09 | docs/FDD.md | Fluxo | Movimentação para a DLQ | TRANSCRICAO | [09:18] Diego |
| FDD-FLUXO-10 | docs/FDD.md | Fluxo | Replay: ADMIN, volta para pendente, log de auditoria | TRANSCRICAO | [09:18] Diego; [09:36] Sofia; [09:36] Larissa |
| FDD-FLUXO-11 | docs/FDD.md | Fluxo | Rotação de secret com 24 h de convivência | TRANSCRICAO | [09:21] Sofia |
| FDD-FLUXO-12 | docs/FDD.md | Fluxo | Secret gerada pela plataforma e devolvida (geração via crypto: proposta derivada) | TRANSCRICAO | [09:31] Marcos |
| FDD-FLUXO-13 | docs/FDD.md | Fluxo | Desligamento do worker no padrão do servidor | CODIGO | `src/server.ts` |
| FDD-CONTRATO-01 | docs/FDD.md | Contrato | POST /webhooks: cadastrar | TRANSCRICAO | [09:31] Marcos |
| FDD-CONTRATO-02 | docs/FDD.md | Contrato | GET /customers/:customerId/webhooks: listar, com customerId no path | TRANSCRICAO | [09:33] Bruno; [09:32] Larissa |
| FDD-CONTRATO-03 | docs/FDD.md | Contrato | GET /webhooks/:id (proposta derivada do CRUD) | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-04 | docs/FDD.md | Contrato | PATCH /webhooks/:id | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-05 | docs/FDD.md | Contrato | DELETE /webhooks/:id | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-06 | docs/FDD.md | Contrato | POST /webhooks/:id/rotate-secret (rota: proposta derivada) | TRANSCRICAO | [09:21] Sofia |
| FDD-CONTRATO-07 | docs/FDD.md | Contrato | GET /webhooks/:id/deliveries | TRANSCRICAO | [09:34] Marcos |
| FDD-CONTRATO-08 | docs/FDD.md | Contrato | GET /admin/webhooks/dead-letter (proposta derivada do replay) | TRANSCRICAO | [09:18] Diego |
| FDD-CONTRATO-09 | docs/FDD.md | Contrato | POST /admin/webhooks/dead-letter/:id/replay | TRANSCRICAO | [09:18] Diego; [09:35] Diego |
| FDD-CONTRATO-10 | docs/FDD.md | Contrato | Request de saída: headers e payload enxuto | TRANSCRICAO | [09:43] Diego; [09:44] Diego; [09:44] Sofia |
| FDD-CONTRATO-11 | docs/FDD.md | Restrição | Convenções: prefixo /api/v1, corpo de erro, paginação | CODIGO | `src/app.ts`; `src/middlewares/error.middleware.ts`; `src/shared/http/response.ts` |
| FDD-CONTRATO-12 | docs/FDD.md | Restrição | customerId não vem do JWT | TRANSCRICAO | [09:32] Bruno; [09:32] Larissa |
| FDD-ERRO-01 | docs/FDD.md | Erro | WEBHOOK_NOT_FOUND (404) | TRANSCRICAO | [09:28] Bruno |
| FDD-ERRO-02 | docs/FDD.md | Erro | URL sem HTTPS validada no Zod → VALIDATION_ERROR; WEBHOOK_INVALID_URL não é usado | TRANSCRICAO | [09:23] Sofia; [09:28] Bruno |
| FDD-ERRO-03 | docs/FDD.md | Erro | WEBHOOK_CUSTOMER_NOT_FOUND (proposta derivada do prefixo) | TRANSCRICAO | [09:29] Larissa |
| FDD-ERRO-04 | docs/FDD.md | Erro | WEBHOOK_SECRET_ROTATION_IN_PROGRESS (proposta derivada) | TRANSCRICAO | [09:21] Sofia |
| FDD-ERRO-05 | docs/FDD.md | Erro | WEBHOOK_DEAD_LETTER_NOT_FOUND (proposta derivada do prefixo) | TRANSCRICAO | [09:29] Larissa |
| FDD-ERRO-06 | docs/FDD.md | Erro | WEBHOOK_DEAD_LETTER_ALREADY_REPLAYED (proposta derivada) | TRANSCRICAO | [09:18] Diego |
| FDD-ERRO-07 | docs/FDD.md | Erro | WEBHOOK_INACTIVE (proposta derivada do prefixo) | TRANSCRICAO | [09:29] Larissa |
| FDD-ERRO-08 | docs/FDD.md | Erro | VALIDATION_ERROR reaproveitado | CODIGO | `src/middlewares/validate.middleware.ts` |
| FDD-ERRO-09 | docs/FDD.md | Erro | UNAUTHORIZED reaproveitado | CODIGO | `src/middlewares/auth.middleware.ts` |
| FDD-ERRO-10 | docs/FDD.md | Erro | FORBIDDEN via requireRole | CODIGO | `src/middlewares/auth.middleware.ts` |
| FDD-ERRO-11 | docs/FDD.md | Erro | 500 genérico quando a outbox falha | CODIGO | `src/middlewares/error.middleware.ts` |
| FDD-ERRO-12 | docs/FDD.md | Erro | WEBHOOK_SECRET_REQUIRED como motivo interno | TRANSCRICAO | [09:28] Bruno |
| FDD-ERRO-13 | docs/FDD.md | Erro | WEBHOOK_DELIVERY_TIMEOUT | TRANSCRICAO | [09:42] Diego |
| FDD-ERRO-14 | docs/FDD.md | Erro | WEBHOOK_DELIVERY_HTTP_ERROR (proposta derivada) | TRANSCRICAO | [09:15] Diego |
| FDD-ERRO-15 | docs/FDD.md | Erro | WEBHOOK_DELIVERY_NETWORK_ERROR (proposta derivada) | TRANSCRICAO | [09:15] Diego |
| FDD-ERRO-16 | docs/FDD.md | Erro | WEBHOOK_MAX_ATTEMPTS_EXCEEDED | TRANSCRICAO | [09:17] Diego |
| FDD-ERRO-17 | docs/FDD.md | Erro | WEBHOOK_PAYLOAD_TOO_LARGE | TRANSCRICAO | [09:23] Sofia; [09:24] Larissa |
| FDD-ERRO-18 | docs/FDD.md | Restrição | Classes novas estendem as existentes e saem pelo barrel | CODIGO | `src/shared/errors/http-errors.ts`; `src/shared/errors/index.ts` |
| FDD-RES-01 | docs/FDD.md | Resiliência | Polling de 2 s | TRANSCRICAO | [09:09] Diego |
| FDD-RES-02 | docs/FDD.md | Resiliência | Timeout de 10 s | TRANSCRICAO | [09:42] Diego |
| FDD-RES-03 | docs/FDD.md | Resiliência | Backoff de 1m/5m/30m/2h/12h | TRANSCRICAO | [09:17] Diego |
| FDD-RES-05 | docs/FDD.md | Resiliência | Crash do worker → reenvio coberto pelo at-least-once (proposta derivada) | TRANSCRICAO | [09:24] Diego |
| FDD-RES-06 | docs/FDD.md | Resiliência | Worker fora: eventos acumulam na outbox | TRANSCRICAO | [09:06] Diego |
| FDD-RES-08 | docs/FDD.md | Resiliência | API reinicia sem afetar o worker | TRANSCRICAO | [09:11] Diego |
| FDD-RES-09 | docs/FDD.md | Resiliência | Falha de envio não afeta a mudança de status | TRANSCRICAO | [09:04] Bruno |
| FDD-RES-10 | docs/FDD.md | Resiliência | HMAC sobre o corpo bruto | TRANSCRICAO | [09:20] Sofia |
| FDD-RES-11 | docs/FDD.md | Resiliência | Duas assinaturas durante a rotação (proposta derivada) | TRANSCRICAO | [09:21] Sofia |
| FDD-RES-12 | docs/FDD.md | Resiliência | Processamento sequencial preserva a ordem sem falhas | TRANSCRICAO | [09:12] Diego |
| FDD-RES-13 | docs/FDD.md | Restrição | Limitação: ordem por pedido pode se inverter com retry (inferência) | TRANSCRICAO | [09:12] Diego; [09:17] Diego |
| FDD-RES-14 | docs/FDD.md | Resiliência | Sem fallback automático; e-mail fora de escopo | TRANSCRICAO | [09:18] Diego; [09:37] Larissa |
| FDD-OBS-01 | docs/FDD.md | Observabilidade | Logs Pino com mensagens em snake_case | CODIGO | `src/shared/logger/index.ts`; `src/server.ts` |
| FDD-OBS-02 | docs/FDD.md | Observabilidade | Log de auditoria do replay, com userId | TRANSCRICAO | [09:36] Sofia |
| FDD-OBS-03 | docs/FDD.md | Observabilidade | Redaction de secret e assinatura (proposta derivada) | CODIGO | `src/shared/logger/index.ts` |
| FDD-OBS-04 | docs/FDD.md | Observabilidade | Métrica: idade do pendente mais antigo, alerta acima de 10 s (proposta derivada) | TRANSCRICAO | [09:02] Marcos |
| FDD-OBS-05 | docs/FDD.md | Observabilidade | Métrica: eventos por cliente por minuto (proposta derivada) | TRANSCRICAO | [09:38] Diego |
| FDD-OBS-09 | docs/FDD.md | Observabilidade | Métrica: contador da DLQ (proposta derivada) | TRANSCRICAO | [09:18] Diego |
| FDD-OBS-10 | docs/FDD.md | Restrição | O projeto não tem infraestrutura de métricas | CODIGO | `package.json` |
| FDD-OBS-11 | docs/FDD.md | Observabilidade | Tracing: só existe requestId; correlação por eventId | CODIGO | `src/middlewares/request-logger.middleware.ts` |
| FDD-INTEG-01 | docs/FDD.md | Integração | `changeStatus` chama `publishWebhookEvent` após o histórico (L159–167) | CODIGO | `src/modules/orders/order.service.ts` |
| FDD-INTEG-02 | docs/FDD.md | Integração | Novos modelos + relação em Customer | CODIGO | `prisma/schema.prisma` |
| FDD-INTEG-03 | docs/FDD.md | Integração | Nova migration aditiva | CODIGO | `prisma/migrations/` |
| FDD-INTEG-04 | docs/FDD.md | Integração | Classes Webhook*Error | CODIGO | `src/shared/errors/http-errors.ts` |
| FDD-INTEG-05 | docs/FDD.md | Integração | Exportar as classes novas | CODIGO | `src/shared/errors/index.ts` |
| FDD-INTEG-06 | docs/FDD.md | Integração | AppError como base, sem mudança | CODIGO | `src/shared/errors/app-error.ts` |
| FDD-INTEG-07 | docs/FDD.md | Integração | Middleware de erro sem mudança | CODIGO | `src/middlewares/error.middleware.ts` |
| FDD-INTEG-08 | docs/FDD.md | Integração | `authenticate` e `requireRole('ADMIN')` | CODIGO | `src/middlewares/auth.middleware.ts` |
| FDD-INTEG-09 | docs/FDD.md | Integração | `validate` com os schemas novos | CODIGO | `src/middlewares/validate.middleware.ts` |
| FDD-INTEG-10 | docs/FDD.md | Integração | `buildControllers` instancia o módulo | CODIGO | `src/app.ts` |
| FDD-INTEG-11 | docs/FDD.md | Integração | Rotas `/webhooks`, `/customers/:customerId/webhooks` e `/admin/webhooks` | CODIGO | `src/routes/index.ts` |
| FDD-INTEG-12 | docs/FDD.md | Integração | `paginated()` nas listas | CODIGO | `src/shared/http/response.ts` |
| FDD-INTEG-13 | docs/FDD.md | Integração | Novos redactPaths | CODIGO | `src/shared/logger/index.ts` |
| FDD-INTEG-14 | docs/FDD.md | Integração | Variáveis de ambiente opcionais | CODIGO | `src/config/env.ts` |
| FDD-INTEG-15 | docs/FDD.md | Integração | O worker usa `prisma` próprio por processo | CODIGO | `src/config/database.ts` |
| FDD-INTEG-16 | docs/FDD.md | Integração | Servidor como molde do worker | CODIGO | `src/server.ts` |
| FDD-INTEG-17 | docs/FDD.md | Integração | Máquina de estados define os status emitidos | CODIGO | `src/modules/orders/order.status.ts` |
| FDD-INTEG-18 | docs/FDD.md | Integração | Schemas de orders como referência | CODIGO | `src/modules/orders/order.schemas.ts` |
| FDD-INTEG-19 | docs/FDD.md | Integração | Scripts do worker | CODIGO | `package.json` |
| FDD-INTEG-20 | docs/FDD.md | Integração | Testes existentes como referência | CODIGO | `tests/orders.test.ts`; `tests/setup.ts` |
| FDD-NOVO-01 | docs/FDD.md | Integração | Rotas do novo módulo espelham as rotas de orders | CODIGO | `src/modules/orders/order.routes.ts` |
| FDD-NOVO-02 | docs/FDD.md | Integração | Controller do novo módulo espelha o controller de orders | CODIGO | `src/modules/orders/order.controller.ts` |
| FDD-NOVO-03 | docs/FDD.md | Integração | Service do novo módulo espelha o service de orders | CODIGO | `src/modules/orders/order.service.ts` |
| FDD-NOVO-04 | docs/FDD.md | Integração | Repository do novo módulo espelha o repository de orders | CODIGO | `src/modules/orders/order.repository.ts` |
| FDD-NOVO-05 | docs/FDD.md | Integração | Schemas do novo módulo espelham os schemas de orders | CODIGO | `src/modules/orders/order.schemas.ts` |
| FDD-NOVO-06 | docs/FDD.md | Integração | Publicador de eventos com a função pura | TRANSCRICAO | [09:41] Bruno; [09:41] Diego |
| FDD-NOVO-07 | docs/FDD.md | Integração | Processador do worker em arquivo próprio no módulo | TRANSCRICAO | [09:28] Bruno |
| FDD-NOVO-08 | docs/FDD.md | Integração | Componente de assinatura HMAC (proposta derivada) | TRANSCRICAO | [09:20] Sofia |
| FDD-NOVO-09 | docs/FDD.md | Integração | Ponto de entrada novo para o worker | TRANSCRICAO | [09:11] Larissa; [09:28] Bruno |
| FDD-NOVO-10 | docs/FDD.md | Integração | Testes do novo módulo (proposta derivada) | CODIGO | `tests/orders.test.ts` |
| FDD-DEP-01 | docs/FDD.md | Dependência | Nenhuma dependência npm nova | CODIGO | `package.json` |
| FDD-DEP-02 | docs/FDD.md | Dependência | Migration aditiva | CODIGO | `prisma/migrations/` |
| FDD-DEP-03 | docs/FDD.md | Dependência | Variáveis WEBHOOK_* com padrões dos ADRs (proposta derivada) | CODIGO | `src/config/env.ts` |
| FDD-DEP-04 | docs/FDD.md | Dependência | Segundo processo para operar | TRANSCRICAO | [09:11] Diego |
| FDD-DEP-05 | docs/FDD.md | Restrição | Mudança de status pode retornar 500 se a outbox falhar | TRANSCRICAO | [09:40] Bruno |
| FDD-DEP-06 | docs/FDD.md | Dependência | Contrato público documentado no portal | TRANSCRICAO | [09:26] Marcos; [09:40] Marcos |
| FDD-CA-01 | docs/FDD.md | Critério de Aceite | Uma linha PENDING por webhook interessado, na mesma transação | TRANSCRICAO | [09:40] Bruno |
| FDD-CA-02 | docs/FDD.md | Critério de Aceite | Status sem interessados não gera linha | TRANSCRICAO | [09:34] Bruno |
| FDD-CA-03 | docs/FDD.md | Critério de Aceite | Erro na outbox → status, histórico e estoque intactos | TRANSCRICAO | [09:40] Bruno |
| FDD-CA-04 | docs/FDD.md | Critério de Aceite | Payload não muda depois da inserção | TRANSCRICAO | [09:52] Larissa |
| FDD-CA-05 | docs/FDD.md | Critério de Aceite | Envio em até 2 s + processamento | TRANSCRICAO | [09:09] Diego |
| FDD-CA-06 | docs/FDD.md | Critério de Aceite | Headers e assinatura conferem | TRANSCRICAO | [09:20] Sofia; [09:44] Diego |
| FDD-CA-07 | docs/FDD.md | Critério de Aceite | Timeout reagenda para 1 min | TRANSCRICAO | [09:42] Diego; [09:17] Diego |
| FDD-CA-08 | docs/FDD.md | Critério de Aceite | Esgotadas as tentativas → FAILED + DLQ | TRANSCRICAO | [09:17] Diego; [09:18] Diego |
| FDD-CA-09 | docs/FDD.md | Critério de Aceite | Replay: OPERATOR 403; ADMIN 202, mesmo id, log | TRANSCRICAO | [09:36] Sofia |
| FDD-CA-10 | docs/FDD.md | Critério de Aceite | Segundo replay → 409 (proposta derivada) | TRANSCRICAO | [09:18] Diego |
| FDD-CA-11 | docs/FDD.md | Critério de Aceite | URL http → VALIDATION_ERROR no campo url | TRANSCRICAO | [09:23] Sofia |
| FDD-CA-12 | docs/FDD.md | Critério de Aceite | Secret só na criação e na rotação, nunca em log (proposta derivada) | TRANSCRICAO | [09:31] Marcos |
| FDD-CA-13 | docs/FDD.md | Critério de Aceite | Duas assinaturas por 24 h após a rotação | TRANSCRICAO | [09:21] Sofia |
| FDD-CA-14 | docs/FDD.md | Critério de Aceite | Ordem de criação no caminho sem falhas | TRANSCRICAO | [09:12] Diego |
| FDD-CA-15 | docs/FDD.md | Critério de Aceite | > 64 KB vai direto para a DLQ | TRANSCRICAO | [09:24] Larissa |
| FDD-CA-16 | docs/FDD.md | Critério de Aceite | PROCESSING preso é reprocessado (proposta derivada) | TRANSCRICAO | [09:24] Diego |
| FDD-CA-17 | docs/FDD.md | Critério de Aceite | Reiniciar a API não interrompe o worker | TRANSCRICAO | [09:11] Diego |
| FDD-CA-18 | docs/FDD.md | Critério de Aceite | Erros novos no formato padrão, sem mudar o middleware | TRANSCRICAO | [09:29] Bruno |
| FDD-RISK-01 | docs/FDD.md | Risco | Revisão de segurança bloqueia o deploy | TRANSCRICAO | [09:46] Sofia; [09:47] Larissa |
| FDD-RISK-02 | docs/FDD.md | Risco | Prazo da Atlas | TRANSCRICAO | [09:45] Marcos; [09:46] Larissa |
| FDD-RISK-03 | docs/FDD.md | Risco | Cliente não deduplica | TRANSCRICAO | [09:25] Sofia; [09:26] Marcos |
| FDD-RISK-04 | docs/FDD.md | Risco | Outbox cresce sem arquivamento | TRANSCRICAO | [09:08] Diego |
| FDD-RISK-05 | docs/FDD.md | Risco | Inversão de ordem com retry (inferência) | TRANSCRICAO | [09:12] Diego; [09:17] Diego |
| FDD-RISK-06 | docs/FDD.md | Risco | Transação de changeStatus estoura o timeout do Prisma (proposta derivada) | CODIGO | `src/modules/orders/order.service.ts` |
| FDD-RISK-07 | docs/FDD.md | Risco | Secret em texto no banco (proposta derivada) | TRANSCRICAO | [09:21] Bruno |
| FDD-RISK-08 | docs/FDD.md | Risco | Worker parado sem ninguém perceber (proposta derivada) | TRANSCRICAO | [09:02] Marcos |
| FDD-RISK-09 | docs/FDD.md | Risco | Rajada para um mesmo cliente | TRANSCRICAO | [09:38] Diego; [09:39] Larissa |
| FDD-Q-01 | docs/FDD.md | Questão em Aberto | Rate limiting | TRANSCRICAO | [09:38] Diego; [09:39] Larissa |
| FDD-Q-02 | docs/FDD.md | Questão em Aberto | Estratégia de escala | TRANSCRICAO | [09:13] Diego |
| FDD-Q-03 | docs/FDD.md | Questão em Aberto | Arquivamento da outbox | TRANSCRICAO | [09:08] Diego |
| FDD-Q-04 | docs/FDD.md | Questão em Aberto | Autorização do CRUD | TRANSCRICAO | [09:37] Sofia |
| FDD-Q-05 | docs/FDD.md | Questão em Aberto | Como expor métricas e tracing | CODIGO | `package.json` |
| FDD-Q-06 | docs/FDD.md | Questão em Aberto | Cifrar a secret em repouso (proposta derivada da revisão) | TRANSCRICAO | [09:46] Sofia |
| FDD-Q-07 | docs/FDD.md | Questão em Aberto | Contagem de tentativas | TRANSCRICAO | [09:17] Diego |
| FDD-Q-08 | docs/FDD.md | Questão em Aberto | DELETE físico ou soft delete (padrão atual: remoção física) | CODIGO | `src/modules/customers/customer.routes.ts` |
| FDD-Q-09 | docs/FDD.md | Questão em Aberto | Assinar também o X-Timestamp | TRANSCRICAO | [09:44] Diego |
| FDD-Q-10 | docs/FDD.md | Questão em Aberto | Formato de X-Signature durante a rotação (proposta derivada) | TRANSCRICAO | [09:21] Sofia |
| FDD-Q-11 | docs/FDD.md | Questão em Aberto | Ordem por pedido com retry | TRANSCRICAO | [09:12] Diego; [09:17] Diego; [09:14] Marcos |
| ADR-001 | docs/adrs/ADR-001-outbox-no-mysql.md | Decisão | Outbox no MySQL na mesma transação, com snapshot do payload | TRANSCRICAO | [09:06] Diego; [09:08] Larissa; [09:52] Diego |
| ADR-001-ALT-01 | docs/adrs/ADR-001-outbox-no-mysql.md | Alternativa Descartada | Disparo síncrono | TRANSCRICAO | [09:04] Bruno |
| ADR-001-ALT-02 | docs/adrs/ADR-001-outbox-no-mysql.md | Alternativa Descartada | Redis Streams | TRANSCRICAO | [09:07] Larissa; [09:07] Diego |
| ADR-001-ALT-03 | docs/adrs/ADR-001-outbox-no-mysql.md | Alternativa Descartada | Outbox guardando só o id do pedido | TRANSCRICAO | [09:51] Bruno; [09:52] Larissa |
| ADR-001-TO | docs/adrs/ADR-001-outbox-no-mysql.md | Trade-off | Uma tabela a mais em troca de consistência sem infraestrutura nova | TRANSCRICAO | [09:07] Diego; [09:08] Diego |
| ADR-001-COD | docs/adrs/ADR-001-outbox-no-mysql.md | Integração | Transação de changeStatus e schema | CODIGO | `src/modules/orders/order.service.ts`; `prisma/schema.prisma` |
| ADR-002 | docs/adrs/ADR-002-worker-separado-polling-2s.md | Decisão | Worker em processo separado, polling de 2 s | TRANSCRICAO | [09:09] Diego; [09:10] Larissa; [09:11] Diego |
| ADR-002-ALT-01 | docs/adrs/ADR-002-worker-separado-polling-2s.md | Alternativa Descartada | Trigger de banco | TRANSCRICAO | [09:09] Diego |
| ADR-002-ALT-02 | docs/adrs/ADR-002-worker-separado-polling-2s.md | Alternativa Descartada | Worker dentro da API | TRANSCRICAO | [09:11] Diego |
| ADR-002-TO | docs/adrs/ADR-002-worker-separado-polling-2s.md | Trade-off | Até 2 s de atraso e um processo a mais | TRANSCRICAO | [09:10] Larissa |
| ADR-002-COD | docs/adrs/ADR-002-worker-separado-polling-2s.md | Integração | Ponto de entrada análogo e client por processo | CODIGO | `src/server.ts`; `src/config/database.ts` |
| ADR-003 | docs/adrs/ADR-003-retry-backoff-exponencial-e-dlq.md | Decisão | 5 tentativas 1m/5m/30m/2h/12h, timeout de 10 s, DLQ separada, replay manual | TRANSCRICAO | [09:17] Larissa; [09:42] Diego; [09:18] Diego |
| ADR-003-ALT-01 | docs/adrs/ADR-003-retry-backoff-exponencial-e-dlq.md | Alternativa Descartada | Retry indefinido | TRANSCRICAO | [09:15] Diego |
| ADR-003-ALT-02 | docs/adrs/ADR-003-retry-backoff-exponencial-e-dlq.md | Alternativa Descartada | Apenas 3 tentativas | TRANSCRICAO | [09:16] Bruno; [09:16] Diego |
| ADR-003-ALT-03 | docs/adrs/ADR-003-retry-backoff-exponencial-e-dlq.md | Alternativa Descartada | Marcar como failed na própria outbox | TRANSCRICAO | [09:17] Larissa; [09:18] Diego |
| ADR-003-TO | docs/adrs/ADR-003-retry-backoff-exponencial-e-dlq.md | Trade-off | Reprocessamento manual em troca de uma política previsível | TRANSCRICAO | [09:17] Marcos; [09:18] Diego |
| ADR-003-COD | docs/adrs/ADR-003-retry-backoff-exponencial-e-dlq.md | Integração | Controle de tentativas e dead letter no schema | CODIGO | `prisma/schema.prisma` |
| ADR-004 | docs/adrs/ADR-004-hmac-sha256-secret-por-endpoint.md | Decisão | HMAC-SHA256, secret por endpoint, rotação de 24 h | TRANSCRICAO | [09:20] Sofia; [09:21] Sofia; [09:22] Sofia |
| ADR-004-ALT-01 | docs/adrs/ADR-004-hmac-sha256-secret-por-endpoint.md | Alternativa Descartada | Secret global | TRANSCRICAO | [09:21] Sofia; [09:22] Diego |
| ADR-004-ALT-02 | docs/adrs/ADR-004-hmac-sha256-secret-por-endpoint.md | Alternativa Descartada | Outro algoritmo de assinatura | TRANSCRICAO | [09:20] Sofia |
| ADR-004-TO | docs/adrs/ADR-004-hmac-sha256-secret-por-endpoint.md | Trade-off | Gestão de secrets em troca de isolar vazamentos | TRANSCRICAO | [09:21] Sofia |
| ADR-005 | docs/adrs/ADR-005-at-least-once-x-event-id.md | Decisão | At-least-once com X-Event-Id; dedupe no cliente | TRANSCRICAO | [09:25] Diego; [09:26] Larissa |
| ADR-005-ALT-01 | docs/adrs/ADR-005-at-least-once-x-event-id.md | Alternativa Descartada | Exactly-once | TRANSCRICAO | [09:25] Diego |
| ADR-005-TO | docs/adrs/ADR-005-at-least-once-x-event-id.md | Trade-off | Dedupe no cliente em troca de entrega simples | TRANSCRICAO | [09:25] Sofia; [09:25] Diego |
| ADR-006 | docs/adrs/ADR-006-reuso-padroes-do-projeto.md | Decisão | Reuso dos padrões: módulo, AppError, WEBHOOK_, Pino, Zod, requireRole | TRANSCRICAO | [09:27] Bruno; [09:29] Larissa; [09:30] Larissa |
| ADR-006-AUTH | docs/adrs/ADR-006-reuso-padroes-do-projeto.md | Decisão | Replay exige ADMIN + auditoria; CRUD aceita qualquer autenticado | TRANSCRICAO | [09:36] Sofia; [09:36] Larissa; [09:37] Sofia |
| ADR-006-ALT-01 | docs/adrs/ADR-006-reuso-padroes-do-projeto.md | Alternativa Descartada | Estruturas próprias no módulo (não debatida; proposta derivada) | TRANSCRICAO | [09:29] Larissa; [09:30] Larissa |
| ADR-006-ALT-02 | docs/adrs/ADR-006-reuso-padroes-do-projeto.md | Alternativa Descartada | Replay liberado para OPERATOR | TRANSCRICAO | [09:36] Sofia |
| ADR-006-TO | docs/adrs/ADR-006-reuso-padroes-do-projeto.md | Trade-off | Herdar limitações em troca de consistência | TRANSCRICAO | [09:30] Larissa |
| ADR-006-COD-01 | docs/adrs/ADR-006-reuso-padroes-do-projeto.md | Integração | Classe base e erros específicos | CODIGO | `src/shared/errors/app-error.ts`; `src/shared/errors/http-errors.ts` |
| ADR-006-COD-02 | docs/adrs/ADR-006-reuso-padroes-do-projeto.md | Integração | Middleware de erro centralizado | CODIGO | `src/middlewares/error.middleware.ts` |
| ADR-006-COD-03 | docs/adrs/ADR-006-reuso-padroes-do-projeto.md | Integração | Validação com Zod | CODIGO | `src/middlewares/validate.middleware.ts` |
| ADR-006-COD-04 | docs/adrs/ADR-006-reuso-padroes-do-projeto.md | Integração | Logger Pino | CODIGO | `src/shared/logger/index.ts` |
| ADR-006-COD-05 | docs/adrs/ADR-006-reuso-padroes-do-projeto.md | Integração | requireRole e estrutura de módulo | CODIGO | `src/middlewares/auth.middleware.ts`; `src/modules/orders/` |
| ADR-007 | docs/adrs/ADR-007-integracao-changestatus-transacional.md | Decisão | Evento gravado na transação de changeStatus, por função pura | TRANSCRICAO | [09:40] Bruno; [09:41] Diego; [09:41] Bruno |
| ADR-007-ALT-01 | docs/adrs/ADR-007-integracao-changestatus-transacional.md | Alternativa Descartada | Gravar fora da transação | TRANSCRICAO | [09:41] Diego |
| ADR-007-ALT-02 | docs/adrs/ADR-007-integracao-changestatus-transacional.md | Alternativa Descartada | Injetar o repository de webhooks | TRANSCRICAO | [09:41] Diego |
| ADR-007-TO | docs/adrs/ADR-007-integracao-changestatus-transacional.md | Trade-off | Falha do evento bloqueia o status em troca de garantia | TRANSCRICAO | [09:40] Bruno |
| ADR-007-COD-01 | docs/adrs/ADR-007-integracao-changestatus-transacional.md | Integração | Ponto de inserção em changeStatus | CODIGO | `src/modules/orders/order.service.ts` |
| ADR-007-COD-02 | docs/adrs/ADR-007-integracao-changestatus-transacional.md | Integração | Máquina de estados que gera os eventos | CODIGO | `src/modules/orders/order.status.ts` |
| ADR-008 | docs/adrs/ADR-008-ordering-single-worker-por-order-id.md | Decisão | Um único worker; ordem só por pedido; sem ordem global | TRANSCRICAO | [09:12] Diego; [09:13] Larissa; [09:14] Marcos |
| ADR-008-ALT-01 | docs/adrs/ADR-008-ordering-single-worker-por-order-id.md | Alternativa Descartada | Vários workers em paralelo agora | TRANSCRICAO | [09:13] Bruno; [09:13] Diego |
| ADR-008-TO | docs/adrs/ADR-008-ordering-single-worker-por-order-id.md | Trade-off | Teto de vazão e sem ordem global, em troca de simplicidade | TRANSCRICAO | [09:13] Larissa |
| ADR-008-LIM | docs/adrs/ADR-008-ordering-single-worker-por-order-id.md | Restrição | A ordem só vale sem retry; questão em aberto (inferência) | TRANSCRICAO | [09:12] Diego; [09:17] Diego |

## Itens sem origem direta

Estes itens aparecem nos documentos como estimativa ou proposta do autor e não derivam de nenhuma fala nem de convenção do código. Por isso **não têm linha na tabela**: precisam de validação na revisão.

| Item | Documento | Motivo |
| --- | --- | --- |
| PRD-OBJ-03: ≥ 99% das entregas sem cair na DLQ | docs/PRD.md | Meta numérica estimada pelo autor; a reunião não definiu taxa de sucesso |
| PRD-CA-10: critérios técnicos do FDD passam | docs/PRD.md | Critério de ligação entre documentos, sem origem própria |
| PRD-TEST-05: piloto com os três clientes solicitantes | docs/PRD.md | Proposta do autor; a reunião não falou em piloto |
| FDD-RES-04: o backoff conta a partir do fim da tentativa que falhou | docs/FDD.md | Detalhe de implementação não discutido |
| FDD-RES-07: falha do banco não derruba o worker | docs/FDD.md | Comportamento proposto pelo autor |
| FDD-OBS-06: métrica `webhook_outbox_pending_total` | docs/FDD.md | Métrica proposta, sem fala associada |
| FDD-OBS-07: métrica `webhook_deliveries_total` | docs/FDD.md | Métrica proposta, sem fala associada |
| FDD-OBS-08: métrica `webhook_delivery_duration_ms` | docs/FDD.md | Métrica proposta, sem fala associada |

## Resumo de cobertura

| Indicador | Valor |
| --- | --- |
| Itens inventariados | 336 (PRD 81, RFC 45, FDD 166, ADRs 44) |
| Itens com linha no tracker | 328 (97,6%) |
| Itens sem origem direta | 8 |
| Linhas com Fonte = TRANSCRICAO | 267 (81,4%) |
| Linhas com Fonte = CODIGO | 61 |

Todas as âncoras `[hh:mm] Nome` existem como fala na transcrição, e todos os caminhos de código existem no repositório. A conferência foi feita com um script de verificação próprio, usado localmente e não versionado neste repositório.
