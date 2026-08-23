# Construir agentes

> Leia quando for escrever um agente, um revisor ou um contrato de handoff.

---

## Fase 5 — Construção

### Agentes — `.claude/agents/<nome>.md`

Frontmatter YAML válido, depois o system prompt em markdown.

| Campo             | Obrigatório | Uso                                                                                             |
| ----------------- | ----------- | ----------------------------------------------------------------------------------------------- |
| `name`            | sim         | `lowercase-hifenizado`, sem `:`. Específico                                                     |
| `description`     | sim         | Quando delegar e quando não. É o que dispara a delegação automática                             |
| `tools`           | não         | Allowlist. Sem ele, herda tudo — herança precisa de justificativa                               |
| `disallowedTools` | não         | Denylist, aplicada antes de `tools`. Aceita `mcp__servidor` e `mcp__*`                          |
| `model`           | não         | Default já é `inherit`. Só declare outro com razão de qualidade, latência ou custo              |
| `permissionMode`  | não         | `default`/`manual`, `acceptEdits`, `auto`, `plan`, `dontAsk`, `bypassPermissions`               |
| `maxTurns`        | não         | Teto contra laço infinito e custo                                                               |
| `effort`          | não         | `low`…`max`. Sobrepõe o da sessão                                                               |
| `skills`          | não         | Pré-carga integral no contexto — só o que for realmente necessário                              |
| `mcpServers`      | não         | Só os servidores que o agente precisa                                                           |
| `hooks`           | não         | Política própria do agente. Ignorado em agente de plugin                                        |
| `memory`          | não         | `user`, `project` ou `local`. Só com política de curadoria — memória sem curadoria acumula erro |
| `background`      | não         | Só quando o trabalho termina sem pergunta interativa                                            |
| `isolation`       | não         | `worktree`, só em repositório Git com edição paralela                                           |
| `color`           | não         | `red`, `blue`, `green`, `yellow`, `purple`, `orange`, `pink`, `cyan`                            |
| `initialPrompt`   | não         | Primeiro turno automático quando o agente roda como sessão principal                            |

Fatos que mudam o desenho:

- **Removidas de todo subagente:** `AskUserQuestion`, `EnterPlanMode`, `EndConversation`,
  `ScheduleWakeup`, entre outras. Agente não pergunta ao usuário — se falta informação, ele
  para e devolve a pergunta ao chamador.
- **Agente em background** fica com um conjunto reduzido de ferramentas. Se o processo
  depende de algo fora desse conjunto, não use background.
- `permissionMode: plan` para análise somente leitura. `dontAsk` quando qualquer pedido de
  permissão deve falhar em vez de travar esperando. `bypassPermissions` só em ambiente
  isolado — nunca por conveniência.

**Corpo do agente.** É o system prompt dele e precisa ser autossuficiente: o agente não
recebe o histórico da conversa. Elemento implícito é lacuna que o modelo preenche por conta
própria — e lacuna preenchida em silêncio é o erro que ninguém revisa. Nesta ordem:

1. Papel e especialidade — profissão nomeada, nunca "assistente". Inclua o tom de voz e
   **de que lado o agente está**: quem é favorecido quando dois interesses legítimos
   colidirem (fechar a venda ou proteger a margem; agradar o cliente ou apurar o número;
   velocidade ou precisão). Sem essa linha o modelo escolhe sozinho — e ele escolhe agradar.
2. Missão e resultado observável
3. Quando atuar e quando devolver ao chamador. **Assuma o escopo mais estreito plausível**
   — um agente de revisão parte de "o que foi alterado agora", não da base inteira; um
   agente de proposta parte de "este lead", não do funil todo — salvo o usuário pedir o
   escopo largo explicitamente. Escopo largo por padrão é o motivo mais comum de agente
   lento e caro sem ninguém ter pedido isso.
4. Entradas obrigatórias e como validar cada uma
5. Fontes de verdade, em ordem de prioridade
6. Limites explícitos
7. Ferramentas permitidas e finalidade de cada uma
8. Processo passo a passo
9. Aprovações e segurança
10. Erros, dado ausente, conflito, ferramenta fora do ar
11. Critérios de qualidade e verificação
12. Contrato de saída — o que devolve ao chamador
13. Critérios de parada e escalonamento
14. Curadoria de memória, se habilitada

**Exemplo âncora.** Todo agente que produz conteúdo, análise ou documento carrega no corpo
**um caso real resolvido**: a entrada como ela chega de verdade e a saída aprovada que ela
deve produzir. Descrição de qualidade não transfere padrão; exemplo transfere. Tire-o do
módulo de conhecimento tácito e marque o que nele é obrigatório (estrutura, nível de
detalhe, voz) e o que é circunstancial (os números daquele caso).

Escreva em segunda pessoa e no imperativo. Explique o porquê das restrições importantes em
uma linha: modelo que entende a razão generaliza para o caso que você não previu; modelo
que decora regra falha nele. Evite persona teatral, motivação vazia, adjetivo sem teste e
instrução genérica do tipo "faça com qualidade".

**Quatro regras fixas que todo agente que produz conteúdo deve carregar no corpo**,
adaptadas ao domínio:

1. A regra que impede o erro mais caro daquela função específica. Ela sai do módulo de
   conhecimento tácito — é a resposta para "o que você percebeu tarde demais".
2. **Nunca invente número, nome, data, prazo ou preço.** Todo dado precisa de origem
   rastreável: um arquivo desta pasta ou algo que o usuário disse. Sem origem, escreva
   `[[CONFIRMAR: <o quê>]]` no lugar do dado e siga. Número inventado sai assinado pelo
   usuário e o custo cai nele, não no agente.
3. **Nunca envie nada para fora** — e-mail, post, mensagem, upload, commit. Entregue
   rascunho em arquivo; o envio é decisão do usuário.
4. **Informação contraditória: pare.** Mostre as duas versões com o arquivo de origem de
   cada uma e pergunte. Escolher em silêncio esconde o conflito e propaga o erro.

**Quatro regras fixas que todo agente que produz código deve carregar no corpo.** O erro
caro aqui é diferente do de conteúdo: texto errado alguém lê e percebe; código errado passa
na revisão porque ninguém rodou.

1. **Nunca declare que funciona sem ter rodado.** Diga qual dos quatro estados vale:
   escrito · validado estaticamente · simulado · executado de verdade. "Deve funcionar" não
   é nenhum dos quatro.
2. **Rode o comando de verificação do projeto e cole a saída real.** Qual é o comando você
   descobriu na Fase 0 — teste, lint ou build. Se não houver, diga que não há em vez de
   inventar um. Resumo da saída esconde o que falhou; a saída inteira, não.
3. **Menor diff possível.** Não toque em arquivo que o pedido não citou, não reformate o que
   estava do lado, não "aproveite para arrumar". Diff largo faz a revisão ser abandonada no
   meio, e é aí que o erro passa.
4. **Não commite, não faça push, não faça deploy.** Entregue o diff e diga o que ele muda.
   Publicar é decisão de quem assina.

O agente também precisa saber onde parar de ler: **assuma o escopo mais estreito plausível**
— um revisor parte do que foi alterado agora, não da base inteira.

**Handoff.** Com dois ou mais agentes, defina o contrato:

```markdown
## Handoff

- agente_origem:
- agente_destino:
- id_tarefa:
- objetivo:
- entradas:
- evidencias:
- decisoes_tomadas:
- questoes_abertas:
- riscos:
- saida_esperada:
- validacao_exigida:
```

Passe só o necessário. O orquestrador guarda o objetivo global, valida o handoff e resolve
conflito. Especialista não reimplementa orquestração.

### Revisor — inclua sempre que o time tiver dois ou mais agentes

Agente que produz não é bom juiz do que produziu: ele já aceitou as próprias premissas. Um
revisor separado, **que lê e não conserta**, é o componente que mais melhora a qualidade do
conjunto por unidade de esforço. Se ele editasse, o usuário receberia o conserto em
silêncio e nunca saberia qual erro o time comete de forma recorrente — e é essa informação
que faz o time melhorar.

Adapte os critérios ao domínio; a mecânica de nota é o que não muda.

```markdown
---
name: revisor
description: Use SEMPRE que outro agente terminar uma entrega, antes de o usuário ver o
  resultado. Gatilhos: "revisa isso", "passa no revisor", "isso tá bom?", "pode mandar?".
tools: Read, Grep, Glob
---

# Revisor

Você revisa o trabalho dos outros agentes. Você NÃO conserta nada — quem conserta é o
agente que fez.

## Por que você só lê

Se você editar o arquivo, a revisão perde o sentido: o usuário precisa saber o que estava
errado, não receber o conserto sem ver.

## Como revisar

Dê nota de 0 a 10 em CADA critério, separadamente. Nunca dê nota geral — nota geral
esconde qual dimensão falhou.

1. Fez o que foi pedido (nem mais, nem menos)
2. Todo dado tem origem rastreável
3. Está pronto para uso (formato, arquivo, lugar certo)
4. Português de gente (sem enrolação, sem tom de robô)

## Regras da nota

- Toda nota abaixo de 10 cita o TRECHO EXATO que a derrubou, com o número da linha. Sem
  trecho, a nota não vale.
- Não arredonde para cima. 7 é 7.
- Critério que você não conseguiu verificar: escreva NÃO VERIFIQUEI e diga o que faltou.
  Nota chutada é pior que nota ausente, porque parece informação.
- Feche com o pior problema, em uma frase, e o que fazer.
- Se estiver tudo certo, diga que está certo. Não invente problema para parecer útil.
```

Ligue o revisor ao fluxo: entrega com nota abaixo de 8 em qualquer critério volta ao agente
que a produziu, com o trecho apontado. Use `Read, Grep, Glob` e nada mais — sem `Edit` e
sem `Write`, a restrição de "não conserta" vira mecanismo em vez de pedido.
