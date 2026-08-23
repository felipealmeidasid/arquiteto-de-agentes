# Redação do `description` e anti-padrões

> Leia ao redigir o `description` de qualquer agente ou skill, e de novo antes de
> fechar a arquitetura, para conferir que nenhum anti-padrão entrou.

---

## Redação do `description`

É o campo de maior alavancagem do sistema inteiro. Um agente excelente com descrição
ruim nunca é chamado.

Método:

1. Gere cerca de vinte consultas de gatilho: **positivas** com redações bem diferentes,
   **negativas próximas** (o caso vizinho que não deve acionar) e **ambíguas**.
2. Escreva a descrição respondendo duas coisas: o que faz e quando acionar. Caso principal
   primeiro, porque o texto é truncado em 1.536 caracteres somando `when_to_use` no Claude
   Code — e em 1.024 no modo portável, onde o excesso derruba o empacotamento.
3. **Terceira pessoa e voz de instrução. Nunca primeira, nunca segunda.** "Revisa código em
   busca de falhas de segurança. Use proativamente depois de qualquer alteração" está
   certo. "Eu reviso código" e "revisa o seu código" estão errados — quem lê a descrição é
   o chamador decidindo delegar, não o usuário. Para skill, a forma que dispara melhor é a
   instrução direta: "use esta skill quando…", não "esta skill faz…".
4. Inclua as frases reais do bloco B da entrevista, no vocabulário do cliente e em
   português. "Quando o usuário mandar link de notícia, print ou disser 'vira post'" vale
   mais que "conteúdo para redes sociais".
5. **Todo exemplo de gatilho precisa mostrar o momento da delegação**, não só a situação:
   contexto → pedido do usuário → o chamador decidindo acionar este agente — nunca um
   exemplo em que o assistente resolve direto sem delegar. Exemplo que não mostra a
   delegação ensina o padrão errado de disparo.
6. Seja levemente insistente — subdisparo é o modo de falha mais comum. Para agentes, o
   padrão da documentação é o tom de "use proativamente quando…".
7. Delimite pelo negativo quando houver vizinho perigoso: "não use para X; para isso, use
   Y".
8. Confirme que não colide com a descrição de nada instalado (Fase 0).

---

## Anti-padrões

| Sintoma                                     | Por que quebra                                          | Faça                                         |
| ------------------------------------------- | ------------------------------------------------------- | -------------------------------------------- |
| Criar agente porque a tarefa é importante   | Importância não pede isolamento de contexto             | Skill, ou o próprio fluxo principal          |
| Orquestrador com um especialista só         | Camada sem roteamento, só custo e latência              | Um agente                                    |
| Agente que reescreve o pedido e delega      | Não produz artefato                                     | Corte a camada                               |
| Skill que só dá um cargo ao modelo          | Sem procedimento nem conhecimento, não muda nada        | Só crie skill com conteúdo reutilizável      |
| `context: fork` em skill que é diretriz     | Fork sem tarefa acionável volta vazio                   | Rode inline                                  |
| Regra repetida em quatro camadas            | Divergem e ninguém sabe qual vale                       | Dono único + referência                      |
| `bypassPermissions` por conveniência        | Remove a proteção inteira, inclusive `.git` e `.claude` | Regras `allow` específicas                   |
| Descrição genérica                          | Nunca dispara, ou dispara errado                        | Método da seção Redação do `description`     |
| "Nunca faça X" só no corpo do agente        | É pedido, não garantia                                  | `deny` ou hook `PreToolUse`                  |
| Herdar todas as ferramentas por padrão      | Superfície de risco e de erro maior que o necessário    | Allowlist mínima                             |
| Memória de agente sem curadoria             | Acumula erro e vira fonte de verdade falsa              | Só com política de revisão                   |
| Agente para o que a pessoa decide de cabeça | Devolve o que ela já sabe, com passo a mais no caminho  | Deixe fora do time e diga por quê            |
| Agente que produz e revisa a própria saída  | Já aceitou as próprias premissas; não enxerga o erro    | Revisor separado, só leitura (seção Revisor) |
| Descrever um agente usando "e"              | Duas responsabilidades disfarçadas de uma               | Dois agentes                                 |
