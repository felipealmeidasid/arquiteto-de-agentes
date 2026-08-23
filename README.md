<p align="right"><a href="README.en.md">English version →</a></p>

# Arquiteto de Agentes e Skills

**Transforma uma tarefa real de trabalho — descrita em linguagem natural por alguém que
provavelmente não é técnico — em automação instalável no [Claude Code](https://claude.com/claude-code):
agentes, skills, scripts, hooks, regras e integrações MCP.**

Não é um gerador de template. Antes de escrever qualquer arquivo, o processo lê o que já
existe no projeto, decide se a tarefa **merece** virar automação, entrevista para extrair o
que o especialista sabe fazer mas não sabe descrever, e só então constrói — com verificação
no final, não promessa.

```
Você: "meu processo de fechar proposta comercial toma um tempo enorme toda semana"

Arquiteto: [lê o projeto] → [checa se repete / tem padrão / é verificável]
           → [entrevista por blocos, com opções] → [spec curta, aprovada]
           → [constrói agente ou skill] → [mostra o diff antes de salvar]
```

## Por que isto existe

A maioria dos prompts de "criador de agente" é um preenchedor de lacunas:
`[Domain] especialista em [specific areas]`. Isso produz identidade e boilerplate — não
decisão. O problema real de quem atende cliente não é "como escrevo o frontmatter", é
"essa tarefa vale um agente, e se sim, o que ele precisa saber que só o cliente sabe".

Este repositório resolve a segunda pergunta.

## O que você recebe

| Componente                                                                       | O que é                                                                                                        |
| -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| [`skills/arquiteto-de-agentes/`](skills/arquiteto-de-agentes)                    | Skill pronta para o Claude Code — instale e use com `/arquiteto-de-agentes`                                    |
| [`system-prompt-arquiteto-de-agentes.md`](system-prompt-arquiteto-de-agentes.md) | O mesmo conteúdo, em versão colável — para quem usa claude.ai, Cowork ou outra ferramenta sem suporte a skills |

Os dois vêm do mesmo texto. A skill é gerada automaticamente a partir do `.md` — nunca
edite os arquivos dentro de `skills/` diretamente (veja [Como contribuir](#como-contribuir)).

## Instalação

**Opção 1 — Skill do Claude Code (recomendado)**

```bash
git clone https://github.com/felipealmeidasid/aquiteto-de-agentes.git

# pessoal, disponível em todos os projetos
cp -r aquiteto-de-agentes/skills/arquiteto-de-agentes ~/.claude/skills/

# ou só neste projeto
cp -r aquiteto-de-agentes/skills/arquiteto-de-agentes .claude/skills/
```

Abra o Claude Code e descreva a tarefa — a skill dispara automaticamente pela
`description`, ou chame direto com `/arquiteto-de-agentes`.

**Opção 2 — System prompt colável**

Copie o conteúdo de [`system-prompt-arquiteto-de-agentes.md`](system-prompt-arquiteto-de-agentes.md)
para o início de uma conversa ou projeto em qualquer ferramenta baseada em Claude.

## Como o processo funciona

As seções, na ordem em que a skill as consulta:

- **Contrato de operação** — o que a automação sempre entrega, o que nunca faz, e a
  fronteira entre dado e instrução (conteúdo externo nunca é comando).
- **Idioma e portabilidade** — modo Claude Code × modo portável (claude.ai, Cowork, Skills API).
- **Fase 0 — Inventário** — lê o que já existe antes de propor; regra de reuso; conflito
  de nome e ordem de precedência entre skill pessoal, de projeto e de plugin.
- **Portão de delegabilidade** — repete · tem padrão · é verificável. Faltando um dos
  três, a resposta é consertar o processo, não construir o agente.
- **Fase 1 — Triagem de complexidade** — profundidade de entrevista proporcional ao risco.
- **Fase 2 — Entrevista** — blocos A–H (resultado observável, gatilhos positivos e
  negativos, entradas, saída, ferramentas reais, efeitos, falhas conhecidas, critério de
  pronto) mais um módulo de **conhecimento tácito** para extrair o que o especialista sabe
  fazer mas não sabe descrever.
- **Fase 3 — Spec** — curta, aprovada antes de escrever qualquer arquivo.
- **Fase 4 — Escolha de arquitetura** — quando é `CLAUDE.md`, regra, skill, agente,
  script, hook, MCP ou plugin — e quando nenhum deles é a resposta certa.
- **Fase 5 — Construção** — frontmatter completo de agentes e skills, exemplo âncora
  obrigatório, contrato de handoff, revisor independente.
- **Redação do `description`** — o campo de maior alavancagem do sistema: gatilhos
  positivos, negativos próximos e ambíguos.
- **Autonomia e risco** — quatro níveis (leitura · escrita reversível · efeito externo ·
  proibido) e como cada um vira mecanismo, não só texto.
- **MCP e ferramentas externas** — antes de conectar qualquer serviço.
- **Verificação e avaliação** — matriz requisito × evidência; nenhum "está pronto" sem prova.
- **Anti-padrões** — os erros mais caros e por que quebram.
- **Formato de resposta** — como a entrega chega para quem pediu.
- **Entrega para cliente** — quando a automação vai para a máquina de outra pessoa.
- **Comando de partida** — o texto que o usuário envia para começar, na versão colável.

Leia o documento completo em [`system-prompt-arquiteto-de-agentes.md`](system-prompt-arquiteto-de-agentes.md).

## Estrutura do repositório

```
aquiteto-de-agentes/
├── README.md
├── README.en.md
├── LICENSE
├── system-prompt-arquiteto-de-agentes.md   ← fonte única de verdade
├── scripts/
│   └── build-skill.js                      ← gera skills/ a partir do .md
└── skills/
    └── arquiteto-de-agentes/
        ├── SKILL.md
        └── references/
            ├── construir-agentes.md
            ├── construir-skills.md
            ├── hooks-permissoes-mcp.md
            ├── descricao-e-anti-padroes.md
            ├── verificacao.md
            └── entrega-cliente.md
```

## Como contribuir

A fonte única é `system-prompt-arquiteto-de-agentes.md`. Para mudar qualquer coisa:

1. Edite o `.md` na raiz — nunca os arquivos dentro de `skills/`.
2. Rode `node scripts/build-skill.js` para regenerar a skill.
3. O script falha alto e cedo se um título de seção for renomeado ou removido sem que o
   plano de distribuição seja atualizado — a mensagem de erro diz exatamente o que ajustar.
4. Abra um PR com as duas mudanças juntas (documento + skill regenerada).

Issues e PRs são bem-vindos — em especial relatos de campo (que gatilho não disparou, que
seção faltou numa automação real).

## Licença

[MIT](LICENSE). Use, adapte e redistribua, mantendo o aviso de copyright.
