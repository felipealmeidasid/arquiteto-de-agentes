# CLAUDE.md — Arquiteto de Agentes e Skills

Este repositório contém a **doutrina** que ensina o Claude Code a transformar uma tarefa de
trabalho em automação instalável — agentes, skills, scripts, hooks e regras. O produto é
texto: método em markdown, não software.

Ele é distribuído. Além do autor, outras pessoas recebem uma cópia para construir as
próprias automações. Escreva e responda pensando em quem não é programador.

---

## Os dois modos desta pasta

Estabeleça qual é logo no começo da sessão. Eles têm regras opostas.

| Modo           | A pessoa está                                              | Arquivos vão para                                    |
| -------------- | ---------------------------------------------------------- | ---------------------------------------------------- |
| **Uso**        | construindo uma automação real com `/arquiteto-de-agentes` | `trabalho/` — nunca versionado                       |
| **Manutenção** | melhorando o método em si                                  | `system-prompt-arquiteto-de-agentes.md` e `scripts/` |

Na dúvida, pergunte. Trabalho real salvo como se fosse doutrina polui o produto; uma
melhoria do método salva como trabalho se perde.

---

## Modo uso

Quem chega aqui para construir uma automação normalmente quer isso, não quer editar o
método. A skill conduz o processo; este arquivo só define onde as coisas caem.

- A saída vai para `trabalho/`, que está no `.gitignore`.
- **Nunca** para `skills/` — esse diretório é gerado pelo build e não aceita arquivo à mão.
- **Nunca** commitado. Este repositório é feito para ser compartilhado e republicado.
  Qualquer coisa commitada aqui pode acabar pública, e continua no histórico mesmo depois de
  removida. Trabalho real, dado de cliente e exemplo com nome e número ficam fora.
- Antes de qualquer commit, confira o que está sendo incluído. Não use `git add -A` neste
  repositório; liste os caminhos.
- Credencial não passa por aqui nem por quem supervisiona. A conexão é feita pela própria
  pessoa, na tela de autorização do serviço.

---

## Modo manutenção

### A regra que mais quebra este repo

`system-prompt-arquiteto-de-agentes.md` é a **fonte única**. Tudo dentro de
`skills/arquiteto-de-agentes/` é **gerado** e será sobrescrito.

Nunca edite `SKILL.md` nem `references/*.md` diretamente. Edite a fonte e rode:

```bash
node scripts/build-skill.js
```

### Onde mora o conteúdo que não vem da fonte

Nem tudo no `SKILL.md` sai do documento. Três blocos vivem nas constantes do
`scripts/build-skill.js` e só existem na versão skill:

- `FRONTMATTER` — o frontmatter, o mapa do fluxo e o índice de referências.
- `ABERTURA` — a seção "Abertura", que substitui o formulário do Comando de partida por
  perguntas.
- `PLANO` — qual seção da fonte vai para qual arquivo.

Procurar essas coisas na fonte e não achar é o erro mais comum de quem mexe aqui.

### Se adicionar, renomear ou remover uma seção

Atualize o `PLANO` no mesmo commit. O build valida nos dois sentidos — toda seção do plano
tem que existir na fonte, e toda seção da fonte tem que ter destino no plano — e **falha
alto** com a lista do que divergiu. Ele nunca gera uma skill picotada em silêncio.

### Convenções da fonte

- **Títulos de seção são nomes, sem número.** O repo teve três numerações concorrentes até
  serem removidas; não reintroduza nenhuma. Remissão cruzada cita a seção pelo nome.
- Fases 0–5 são a única numeração que sobrou. Ela é vocabulário do método, não estrutura de
  documento.
- Prosa quebrada em ~90 colunas.
- `SKILL.md` abaixo de 500 linhas — o build avisa quando passa. Se passar, mova conteúdo
  para `references/`.
- Títulos dentro de bloco de código são ignorados pelo parser de propósito (o documento tem
  exemplos com `## Handoff` e `## Como revisar`).

### Verificação antes de dizer que está pronto

1. `node scripts/build-skill.js` roda sem erro.
2. `git diff` nos arquivos gerados mostra só o que era esperado.
3. Para mudança ampla, prove que nada se perdeu comparando com a versão anterior:

```bash
git show HEAD:skills/arquiteto-de-agentes/SKILL.md | diff - skills/arquiteto-de-agentes/SKILL.md
```

Build verde não prova que o conteúdo está certo — prova só que o recorte casou.

### Os dois READMEs andam juntos

`README.md` (pt-BR) e `README.en.md` são o mesmo conteúdo. Mudou um, mude o outro no mesmo
commit. Nenhum dos dois promete contagem de seções — contagem envelhece e mente.

---

## Idioma

- Doutrina, README, comentários e mensagens de commit: **português**.
- Nomes de arquivo, pasta, `name` de skill e comandos: `lowercase-hifenizado`, sem acento.

---

## Este repositório não é um projeto de código

Não aplique aqui o fluxo de engenharia de software: não existe `docs/specs.md`, nem
`docs/progress.md`, nem lint, nem suíte de testes, nem `package.json`. A única verificação é
o build rodar e o diff sair como esperado.

Isso vale para **esta pasta**. Os agentes que este projeto produz rodam na pasta de destino,
sob o `CLAUDE.md` de lá — e muitos deles vão escrever código. Este arquivo não os governa.

---

## Um hook de formatação pode reescrever markdown

Em algumas máquinas há um hook que reformata arquivos markdown depois da gravação,
principalmente re-alinhando padding de tabela. Ver um arquivo mudar sozinho logo após uma
edição é esperado, não é bug.
