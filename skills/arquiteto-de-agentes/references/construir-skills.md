# Construir skills

> Leia quando for escrever uma skill.

### 8.2 Skills — `.claude/skills/<nome>/SKILL.md`

```text
.claude/skills/<nome>/
  SKILL.md
  references/   # só se necessário
  scripts/      # só se necessário
  assets/       # só se necessário
  evals/        # quando houver casos de teste
```

Nada de README, changelog ou arquivo decorativo.

| Campo                        | Uso                                                                                                                                                                        |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `name`                       | Em skill de projeto ou pessoal, é só rótulo de listagem — **o comando vem do nome da pasta**. Só em plugin o `name` define o comando                                       |
| `description`                | O que faz e quando acionar. `description` + `when_to_use` são truncados em 1.536 caracteres na listagem — caso principal primeiro                                          |
| `when_to_use`                | Frases-gatilho e exemplos. Soma no mesmo teto                                                                                                                              |
| `disable-model-invocation`   | `true` para ação que só o usuário deve iniciar: enviar, publicar, deploy, comprar, excluir, commitar. Bônus: zera o custo de contexto, porque a descrição some da listagem |
| `user-invocable`             | `false` para conhecimento de apoio que não é ação útil no menu                                                                                                             |
| `allowed-tools`              | **Pré-aprova, não restringe.** Vale só no turno da invocação e expira na próxima mensagem do usuário                                                                       |
| `disallowed-tools`           | Remove ferramentas enquanto a skill está ativa. Também expira na próxima mensagem                                                                                          |
| `context: fork`              | Roda em subagente isolado. Só para skill que **é uma tarefa**; diretriz sem tarefa vira fork sem prompt acionável e volta vazia                                            |
| `agent`                      | Tipo de subagente do fork: `Explore`, `Plan`, `general-purpose` ou um agente seu                                                                                           |
| `background`                 | Só com `fork`. `false` quando precisar do conjunto completo de ferramentas ou de aprovação interativa                                                                      |
| `paths`                      | Globs que limitam o acionamento automático a arquivos relevantes                                                                                                           |
| `hooks`                      | Registrados na invocação e mantidos pelo resto da sessão                                                                                                                   |
| `model`, `effort`            | Só com benefício concreto                                                                                                                                                  |
| `arguments`, `argument-hint` | Quando a skill recebe parâmetro                                                                                                                                            |
| `shell`                      | `powershell` só quando o corpo tiver comando dinâmico de PowerShell                                                                                                        |

Fatos que mudam a escrita:

- **O conteúdo renderizado entra no contexto e fica lá pelo resto da sessão**, e o arquivo
  não é relido. Escreva como instrução permanente ("sempre que gerar X, faça Y"), não como
  passo único. Depois de compactação, cada skill é reanexada com até 5.000 tokens, dentro
  de um teto combinado de 25.000 — skills antigas caem.
- Mantenha o `SKILL.md` abaixo de 500 linhas. Documentação extensa, variantes, esquemas e
  exemplos vão para `references/`, citados a partir do `SKILL.md` com quando ler cada um.
  Evite cadeia de referência com muitos níveis.
- `scripts/` para o que deve ser reprodutível — entradas e saídas claras, erro útil,
  idempotente, não destrutivo por padrão. Não duplique a lógica do script no corpo.
- **Padrão para rodar script empacotado sem prompt de permissão:** use
  `${CLAUDE_SKILL_DIR}` nos dois lugares, no `allowed-tools` e no corpo, para que a regra
  case exatamente com o comando que o corpo manda rodar.
- **Injeção dinâmica** (`` !`comando` `` no início de linha, ou bloco cercado ` ```! `)
  roda antes de o Claude ver o conteúdo. Trate com cuidado: **se o comando falhar, a
  invocação inteira aborta** e o Claude não vê nada da skill; checagem de permissão que não
  retorne _allow_ também aborta, inclusive uma regra que só perguntaria. Comando que sai
  não-zero por natureza precisa de `|| true`. Nunca injete comando com efeito colateral.

Skill que roda script empacotado, com a variável nos dois lugares:

```yaml
---
description: Renderiza o grafico a partir do CSV
allowed-tools: Bash(${CLAUDE_SKILL_DIR}/scripts/render.sh *)
---
# corpo:
# Rode `${CLAUDE_SKILL_DIR}/scripts/render.sh <arquivo.csv>` e descreva o resultado.
```
