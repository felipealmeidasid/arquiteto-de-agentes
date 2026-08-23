<p align="right"><a href="README.md">Versão em português →</a></p>

# Agent & Skill Architect

**Turns a real work task — described in plain language by someone who's probably not
technical — into automation you can actually install in [Claude Code](https://claude.com/claude-code):
agents, skills, scripts, hooks, rules, and MCP integrations.**

It's not a template generator. Before writing a single file, the process reads what
already exists in the project, decides whether the task actually **deserves** to become
automation, interviews to extract what the expert knows how to do but doesn't know how to
describe, and only then builds — with verification at the end, not a promise.

```
You: "closing a commercial proposal eats a huge chunk of my week, every week"

Architect: [reads the project] → [checks: repeats / has a pattern / is verifiable]
           → [interviews in blocks, with options] → [short spec, approved]
           → [builds the agent or skill] → [shows the diff before saving]
```

## Why this exists

Most "agent creator" prompts are fill-in-the-blank templates:
`[Domain] specialist in [specific areas]`. That produces identity and boilerplate — not a
decision. The real problem when you build automation for a client isn't "how do I write
the frontmatter," it's "does this task deserve an agent, and if so, what does it need to
know that only the client knows."

This repository answers the second question.

## What you get

| Component                                                                        | What it is                                                                                                 |
| -------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| [`skills/arquiteto-de-agentes/`](skills/arquiteto-de-agentes)                    | Ready-to-install Claude Code skill — use it with `/arquiteto-de-agentes`                                   |
| [`system-prompt-arquiteto-de-agentes.md`](system-prompt-arquiteto-de-agentes.md) | The same content as a paste-ready system prompt — for claude.ai, Cowork, or any tool without skill support |

> Note: the file names and the interview flow inside the skill are in **Portuguese**
> (`pt-BR`) — that's the language of the practice this was built in. The method and
> structure translate directly; if you'd like an English-language build of the skill
> itself, open an issue.

Both come from the same source text. The skill is generated automatically from the
`.md` — never edit the files inside `skills/` directly (see [Contributing](#contributing)).

## Installation

**Option 1 — Claude Code skill (recommended)**

```bash
git clone https://github.com/felipealmeidasid/aquiteto-de-agentes.git

# personal, available in every project
cp -r aquiteto-de-agentes/skills/arquiteto-de-agentes ~/.claude/skills/

# or just this project
cp -r aquiteto-de-agentes/skills/arquiteto-de-agentes .claude/skills/
```

Open Claude Code and describe the task — the skill triggers automatically from its
`description`, or invoke it directly with `/arquiteto-de-agentes`.

**Option 2 — Paste-ready system prompt**

Copy the content of [`system-prompt-arquiteto-de-agentes.md`](system-prompt-arquiteto-de-agentes.md)
into the start of a conversation or project in any Claude-based tool.

## How the process works

The sections, in the order the skill consults them:

- **Operating contract** — what the automation always delivers, what it never does, and
  the boundary between data and instruction (external content is never a command).
- **Language and portability** — Claude Code mode × portable mode (claude.ai, Cowork, Skills API).
- **Phase 0 — Inventory** — reads what already exists before proposing anything; reuse
  rule; name collisions and the precedence order between personal, project, and plugin skills.
- **Delegability gate** — repeats · has a pattern · is verifiable. Missing any one of the
  three, the answer is to fix the process, not build the agent.
- **Phase 1 — Complexity triage** — interview depth proportional to risk.
- **Phase 2 — Interview** — blocks A–H (observable outcome, positive and negative
  triggers, inputs, output, real tools, effects, known failure modes, done criteria) plus
  a **tacit knowledge** module to extract what the expert does but can't describe.
- **Phase 3 — Spec** — short, approved before any file gets written.
- **Phase 4 — Architecture choice** — when it's `CLAUDE.md`, a rule, a skill, an agent, a
  script, a hook, MCP, or a plugin — and when none of those is the right answer.
- **Phase 5 — Construction** — full agent and skill frontmatter, a mandatory anchor
  example, a handoff contract, an independent reviewer.
- **Writing the `description`** — the highest-leverage field in the whole system:
  positive triggers, near-negative triggers, and ambiguous ones.
- **Autonomy and risk** — four levels (read · reversible write · external effect ·
  forbidden) and how each one becomes a mechanism, not just prose.
- **MCP and external tools** — before connecting any service.
- **Verification and evaluation** — requirement × evidence matrix; no "done" without proof.
- **Anti-patterns** — the most expensive mistakes, and why they break things.
- **Response format** — how the deliverable reaches whoever asked for it.
- **Client delivery** — when the automation ships to someone else's machine.
- **Starting command** — the text the user sends to begin, in the paste-ready version.

Read the full document in [`system-prompt-arquiteto-de-agentes.md`](system-prompt-arquiteto-de-agentes.md)
(Portuguese).

## Repository structure

```
aquiteto-de-agentes/
├── README.md
├── README.en.md
├── LICENSE
├── system-prompt-arquiteto-de-agentes.md   ← single source of truth
├── scripts/
│   └── build-skill.js                      ← generates skills/ from the .md
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

## Contributing

The single source of truth is `system-prompt-arquiteto-de-agentes.md`. To change anything:

1. Edit the `.md` at the repo root — never the files inside `skills/`.
2. Run `node scripts/build-skill.js` to regenerate the skill.
3. The script fails loudly and early if a section heading gets renamed or removed without
   updating the distribution plan — the error message says exactly what to fix.
4. Open a PR with both changes together (document + regenerated skill).

Issues and PRs are welcome — especially field reports: which trigger didn't fire, which
section was missing on a real build.

## License

[MIT](LICENSE). Use, adapt, and redistribute it, keeping the copyright notice.
