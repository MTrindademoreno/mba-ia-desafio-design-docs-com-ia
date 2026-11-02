# Da Reunião ao Documento: Design Docs Gerados por IA

> O enunciado original do desafio está no [repositório base](https://github.com/devfullcycle/mba-ia-desafio-design-docs-com-ia). Este README descreve o processo que segui para produzir a entrega.

## Sobre o desafio

A partir da transcrição de uma reunião técnica, é gerado um pacote de design docs com a IA e seus recursos como principal ferramenta de produção. O ponto de partida é sempre o mesmo: um time discutiu e fechou as decisões de uma nova feature numa call, mas não sobrou nenhum registro além da transcrição. A documentação é construída a partir da transcrição e do código existente, num nível de detalhe que permita ao time de engenharia implementar a feature (aqui, um sistema de webhooks de notificação de mudança de status de pedidos num OMS em Node.js/TypeScript com Prisma e MySQL).

O pacote de documentos não deve repetir conteúdo. A regra que sustenta tudo é a rastreabilidade: nada entra na documentação sem uma fonte identificável, seja o trecho da reunião, seja o repositório. Parte do trabalho é mapear o que foi decidido, o que foi descartado e o que foi adiado.

## Ferramentas de IA utilizadas

- **Claude Code (modelo Claude Sonnet):** a ferramenta principal. Usada para ler o código da aplicação em `src/` e `prisma/`, analisar a `TRANSCRICAO.md`, estruturar os documentos e redigir o conteúdo. Toda a interação aconteceu no terminal, com o modelo lendo os arquivos direto do repositório.
- **Skill `skill-creator` (Anthropic):** usada para escrever as skills próprias abaixo, uma por tipo de documento.
- **Skills customizadas**, mantidas localmente em `.claude/skills/` e não versionadas neste repositório. Os trechos mais relevantes estão em [Prompts customizados](#prompts-customizados). Todas são agnósticas de projeto: não citam nomes de arquivo nem a stack deste repositório, só caminhos padrão que podem ser trocados.
  - **`extrair-decisoes-transcricao`:** lê a transcrição no formato `[hh:mm] Nome: fala` e produz um catálogo classificado e rastreável em `docs/_work/decisoes.md` (artefato local, não versionado). O catálogo separa decisões (candidatas a ADR), exclusões (o que foi descartado ou adiado) e pontos de atenção (questões em aberto, limitações, riscos, premissas). Funciona como camada anti-alucinação: nenhuma linha entra sem apontar para um timestamp da reunião ou para um caminho de arquivo real.
  - **`gerar-adrs`:** transforma o catálogo em ADRs no formato MADR, cada afirmação com âncora.
  - **`gerar-rfc`:** consolida a proposta em nível de arquitetura, linkando os ADRs em vez de repeti-los.
  - **`gerar-fdd`:** escreve a especificação de implementação. Separa o que veio da reunião, o que vem das convenções do código e o que é proposta do autor, esta última marcada `(proposta)`.
  - **`gerar-prd`:** traduz tudo para a linguagem de produto e marca as estimativas do autor.
  - **`gerar-tracker`:** monta a matriz de rastreabilidade e inclui um script Python de verificação que confere se cada `[hh:mm] Nome` existe na transcrição e se cada caminho existe no repositório.

## Workflow adotado

Organizei o trabalho em camadas, sempre partindo da fonte para o documento, nunca o contrário.

1. **Contextualização.** Primeiro dei à IA acesso ao repositório inteiro e pedi uma leitura rasa do código: estrutura dos módulos em `src/`, máquina de estados do pedido, tratamento de erros, middlewares de autenticação e validação, logger e pontos de entrada da aplicação. Em paralelo, fiz a leitura completa da `TRANSCRICAO.md` para entender do que a reunião tratou antes de tentar extrair qualquer coisa.
2. **Catálogo de decisões.** Em vez de pedir "gere um PRD a partir da transcrição", criei uma skill dedicada (`extrair-decisoes-transcricao`). Ela varre a transcrição e classifica cada tópico em decisão, exclusão ou ponto de atenção, com origem rastreável em `[hh:mm] Nome` ou em caminho de arquivo. O resultado é o `docs/_work/decisoes.md`: um artefato de trabalho, não um entregável, que serve de espinha dorsal para todo o resto.
3. **Revisão humana das lacunas.** A skill termina listando as decisões ambíguas ou incompletas. Resolvi cada uma manualmente:
   - o que vira ADR próprio;
   - o que entra como seção de outro ADR;
   - o que fica só na especificação de implementação;
   - quais nomes de tabela ou de arquivo ainda não devem ser fixados.
4. **Um documento por vez, cada um com sua skill.** Segui a ordem sugerida: **ADRs → RFC → FDD → PRD → Tracker → README**. Para cada documento, o ciclo foi o mesmo:
   1. decidir se valia uma skill ou só um prompt; a escolha foi sempre a skill, porque as regras anti-alucinação se repetem em todos os documentos;
   2. criar a skill com o `skill-creator`;
   3. rodar a skill;
   4. revisar o resultado contra a transcrição e o código;
   5. corrigir;
   6. fazer o commit isolado na branch `docs/design-docs`.
5. **Cada documento parte dos anteriores.** O RFC linka os ADRs, o FDD é construído sobre os ADRs e o RFC, o PRD consolida os três, e o Tracker varre todos. A cada etapa, a revisão também olhava para trás: quando o FDD revelou uma lacuna nos ADRs, os ADRs foram corrigidos (ver "Iterações e ajustes").
6. **Revisão final pela checklist.** Antes de fechar, conferi item por item os critérios de aceite do enunciado, com scripts de verificação: seções obrigatórias, caminhos de código citados, links entre documentos e a validação do tracker.

## Prompts customizados

Cada documento foi gerado por uma skill própria. Abaixo está a descrição de cada uma, ou seja, o texto que define o que a skill faz e quando ela deve ser acionada.

**1. `extrair-decisoes-transcricao`**

```text
Lê a transcrição de uma reunião técnica (formato tipo `[hh:mm] Nome: fala`) e
produz um catálogo estruturado e rastreável, classificando cada item em DECISÃO
(candidata a ADR), EXCLUSÃO (descartado / adiado / não-decisão) ou PONTO DE ATENÇÃO
(questão em aberto, limitação conhecida, risco, premissa). Serve de insumo para
gerar ADRs e outros documentos de design sem alucinação. Use quando o pedido
envolver "extrair decisões da reunião/transcrição", "o que entra e o que não
entra", "classificar as decisões", "preparar ADRs a partir da call".
```

**2. `gerar-adrs`**

```text
Gera ADRs (Architecture Decision Records) em formato MADR a partir de um catálogo
de decisões rastreável (como o produzido pela skill `extrair-decisoes-transcricao`)
e do código do repositório. Escreve um arquivo por decisão, com Status, Contexto,
Decisão, Alternativas Consideradas e Consequências, cada afirmação ancorada em
`[hh:mm] Nome` da reunião ou num caminho de arquivo real, sem inventar nada. Use
sempre que o pedido envolver "gerar/escrever os ADRs", "registrar as decisões
arquiteturais", "transformar o catálogo/as decisões da reunião em ADR", "docs/adrs",
"MADR", mesmo que o usuário não cite o catálogo explicitamente.
```

**3. `gerar-rfc`**

```text
Gera um RFC (Request for Comments) conciso, em nível de arquitetura, consolidando a
proposta técnica a partir de um catálogo de decisões rastreável (como o produzido pela
skill `extrair-decisoes-transcricao`), dos ADRs já escritos e do código do repositório.
Organiza abordagem proposta, alternativas descartadas com o trade-off do descarte,
questões em aberto, impacto e riscos, linkando os ADRs em vez de repeti-los, e ancora
cada afirmação em `[hh:mm] Nome` da reunião ou num caminho de arquivo real, sem
inventar nada. Use sempre que o pedido envolver "escrever/gerar o RFC", "proposta
técnica para revisão", "consolidar a proposta em cima das decisões/ADRs", "request for
comments", "alternativas e questões em aberto da reunião", mesmo que o usuário não cite
o catálogo ou os ADRs explicitamente.
```

**4. `gerar-fdd`**

```text
Gera um FDD (Feature Design Document), a especificação de implementação de uma
feature, construída em cima das decisões já formalizadas (catálogo de decisões, ADRs,
RFC) e do código real do repositório. Detalha fluxos, contratos HTTP com exemplos,
modelo de dados, matriz de erros, resiliência, observabilidade, critérios de aceite e
uma seção de integração com o sistema existente que nomeia arquivos reais e diz como
cada um muda. Separa o que veio da reunião, o que vem das convenções do código e o que
é proposta do autor, sem apresentar invenção como fato. Use sempre que o pedido envolver
"escrever/gerar o FDD", "feature design document", "especificação técnica/de
implementação", "desenho técnico da feature", "contratos e fluxos", "documento para o
dev começar a codar", mesmo que o usuário não cite os ADRs ou o RFC.
```

**5. `gerar-prd`**

```text
Gera um PRD (Product Requirements Document) consolidando, em linguagem de produto,
o que já foi decidido e especificado nos documentos técnicos (ADRs, RFC, FDD) e no
catálogo de decisões da reunião. Cobre contexto, problema, público e cenários de uso,
objetivos com métricas e metas, escopo e fora de escopo, requisitos funcionais e não
funcionais com IDs rastreáveis, decisões e trade-offs, dependências, riscos com
probabilidade/impacto/mitigação, critérios de aceitação e estratégia de testes. Cada
item é ancorado na reunião (`[hh:mm] Nome`) ou no documento de onde veio, e o que for
estimativa do autor é marcado como tal. Use sempre que o pedido envolver "escrever/gerar
o PRD", "product requirements", "documento de produto", "requisitos da feature",
"consolidar os documentos num PRD", "por que e o quê da feature", mesmo que o usuário
não cite os outros documentos.
```

**6. `gerar-tracker`**

```text
Gera um tracker de rastreabilidade: uma tabela Markdown que liga cada item dos
documentos de design (PRD, RFC, FDD, ADRs, especificações) à sua origem na transcrição
da reunião (`[hh:mm] Nome`) ou num arquivo real do código. Varre os documentos prontos,
inventaria requisitos, decisões, alternativas, restrições, riscos, contratos e pontos
de integração, atribui IDs estáveis, confere cada âncora contra a fonte e mede a
cobertura com um script de verificação. Itens sem origem real são listados à parte em
vez de receberem âncora forçada. Use sempre que o pedido envolver "tracker",
"rastreabilidade", "matriz de rastreabilidade", "de onde veio cada requisito/decisão",
"referência cruzada dos documentos", "mapear itens para a transcrição ou o código",
mesmo que o usuário não use a palavra tracker.
```

## Iterações e ajustes

Foram **6 iterações principais**, além dos ajustes menores em cada documento:

1. **Atribuição errada de falas nos ADRs.** A primeira versão dos ADRs passou na checagem de formato, mas, ao reler os trechos citados na transcrição, apareceram atribuições erradas:
   - a frase da indisponibilidade de 2h era do Diego, e não do Bruno ([09:16]);
   - o `PrismaClient` separado foi dito pelo Bruno ([09:30]);
   - o ponto de entrada novo foi proposto pela Larissa ([09:11]).

   Também saíram afirmações que ninguém fez, como "o payload ocupa mais espaço" e "o time não precisa aprender nada novo". A correção foi além dos ADRs: a checklist da skill passou a exigir reler cada trecho citado, e não só conferir que o timestamp existe.
2. **Arquivo de código inexistente.** Um ADR citava, como se fosse código real, o arquivo de ponto de entrada do worker, que ainda não existe. Troquei por "ponto de entrada novo, análogo ao `src/server.ts`".
3. **Skills presas ao projeto.** A primeira skill tinha nomes de arquivo e detalhes da stack deste repositório. Defini como regra que toda skill deve ser agnóstica e reaproveitável, com caminhos apenas como padrão, e as seguintes já nasceram assim.
4. **Uma regra inventada no FDD.** O FDD especificava que o worker seguraria os eventos seguintes de um pedido enquanto um evento anterior estivesse em retry, para preservar a ordem. Perguntei se aquilo estava embasado na transcrição, e não estava. A reunião decidiu a ordem por pedido ([09:12] Diego) e o retry ([09:17] Diego) em momentos separados, sem cruzar os dois temas, e a solução tinha um custo (até ~15h de atraso) que ninguém avaliou. Resultado: a regra saiu do FDD, e a limitação ("a ordem só vale enquanto não há retry") foi registrada como inferência e questão em aberto no ADR-008, no ADR-003, no RFC e no FDD.
5. **Desvios da reunião encontrados na checklist final.** Conferindo os critérios de aceite, encontrei dois pontos em que o FDD se afastava do que foi dito:
   - o `customerId` ia na query, mas a reunião disse "body ou path";
   - a validação de HTTPS ficava no service, mas a Sofia disse que era "só uma validação no schema Zod".

   Os dois foram alinhados com a reunião, aceitando que a URL sem HTTPS retorne `VALIDATION_ERROR`, que é o código produzido pelo middleware existente.
6. **Validação do tracker com script.** O script de verificação foi testado com um tracker cheio de erros de propósito (âncora inexistente, ID duplicado, caminho falso). O teste revelou dois bugs do próprio script, que foram corrigidos antes de usá-lo no tracker real. A primeira versão listava 8 itens sem origem na reunião nem no código (uma meta de 99% de sucesso, um piloto com clientes, três métricas e dois comportamentos de resiliência). Esses itens foram removidos dos documentos, e não apenas sinalizados. A versão final passou sem erros: 100% de cobertura, 81,4% das linhas com fonte `TRANSCRICAO` e 61 com fonte `CODIGO`.

Menores, mas recorrentes: metas e probabilidades de risco que a reunião não forneceu entraram marcadas como `(estimativa do autor)` em vez de omitidas ou disfarçadas, e uma divergência da própria transcrição sobre o prazo ("fim do trimestre" em [09:00] e "fim de novembro" em [09:45]) foi registrada como questão em aberto.

## Como navegar a entrega

Ordem sugerida de leitura, do "por quê" ao "como":

| # | Arquivo | O que é |
| --- | --- | --- |
| 1 | [docs/PRD.md](docs/PRD.md) | Problema, público, objetivos, requisitos e escopo, na linguagem de produto |
| 2 | [docs/RFC.md](docs/RFC.md) | Proposta técnica para revisão: abordagem, alternativas descartadas, questões em aberto |
| 3 | [docs/adrs/](docs/adrs/README.md) | 8 ADRs, uma decisão por arquivo, com contexto, alternativas e trade-off |
| 4 | [docs/FDD.md](docs/FDD.md) | Especificação de implementação: fluxos, contratos, erros, resiliência, observabilidade, integração com o código |
| 5 | [docs/TRACKER.md](docs/TRACKER.md) | De onde veio cada item: fala da reunião ou arquivo do código |

Apoio ao processo (não são entregáveis):

- [TRANSCRICAO.md](TRANSCRICAO.md): a fonte primária, sem alterações.
- `docs/_work/decisoes.md`: o catálogo de decisões que alimentou todos os documentos. É um artefato local e não está versionado.
