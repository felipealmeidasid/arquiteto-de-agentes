# Verificação e gate de conclusão

> Leia antes de validar e antes de declarar qualquer coisa pronta.

---

## Verificação e avaliação

Casos realistas, não demonstração trivial.

**Os casos nascem antes do artefato, não depois.** Feche a spec da Fase 3 e escreva ali
mesmo o que vai ser testado, com a redação que o cliente usaria. Caso escrito depois nasce
contaminado: quem acabou de construir escreve o teste que o que ele construiu passa. Dois
ou três casos bastam para começar — o conjunto cresce com a primeira rodada, não antes
dela.

**Disparo:** três pedidos positivos com redações diferentes · dois negativos próximos que
não devem acionar · um ambíguo que deve gerar pergunta ou rota conservadora · checagem de
conflito com o que já está instalado.

**Comportamento:** caminho feliz · entrada obrigatória ausente · dado malformado ou
contraditório · ferramenta ou MCP fora do ar · permissão negada · falha parcial depois de
uma etapa concluída · **segunda execução da mesma tarefa, para provar idempotência** ·
volume grande · instrução maliciosa embutida em conteúdo externo · tentativa de efeito
Nível C sem confirmação.

**Qualidade:** produziu o artefato real? seguiu a fonte de verdade? usou só as ferramentas
permitidas? preservou arquivos e configurações não relacionados? a saída informa evidência,
mudanças, falhas e próximo passo?

Para skill com saída objetivamente avaliável, gere `evals/evals.json` dentro da pasta da
skill. `prompt` é o pedido como a pessoa digitaria; `expected_output` descreve o sucesso em
português corrido; `files` lista as entradas, caminho relativo à pasta da skill.

```json
{
  "skill_name": "nome-da-skill",
  "evals": [
    {
      "id": 1,
      "prompt": "pedido real do usuario, na redacao dele",
      "expected_output": "descricao do que e um resultado bom",
      "files": ["evals/files/entrada.csv"],
      "assertions": [
        "afirmacao verificavel sobre a saida",
        "outra afirmacao verificavel"
      ]
    }
  ]
}
```

**`assertions` entra depois da primeira rodada, não antes.** Antes de ver a saída real você
não sabe o que checar, e afirmação inventada no vácuo sai frágil demais ou vaga demais. Boa
afirmação é observável e contável: "o arquivo de saída é JSON válido", "o gráfico tem os
dois eixos rotulados", "o relatório traz pelo menos três recomendações". Ruim é "a saída
está boa" — não dá para julgar — e "usa exatamente a frase 'Total: R$ X'" — quebra com
qualquer redação diferente que também estaria certa. Nem tudo merece afirmação: estilo de
escrita e acabamento visual se avaliam olhando, não em passa/não passa.

Quando houver como, rode os casos com e sem a skill no mesmo ciclo e compare. O que
interessa é a diferença: afirmação que passa nos dois lados não prova nada sobre a skill e
deve sair do conjunto; afirmação que só passa com ela mostra onde o valor está, e vale
entender por quê. Não ajuste a skill para decorar os exemplos: falha recorrente vira regra
geral, referência ou script.

### Gate de conclusão

Monte a matriz requisito × evidência. Cada requisito recebe um estado: **comprovado** ·
**contraditado** · **incompleto** · **evidência fraca** · **não testado**. Continue
enquanto houver requisito obrigatório não comprovado.

Arquivo que existe, teste verde e processo que subiu não comprovam o objetivo sozinhos.
Ausência de erro nunca é prova de sucesso.

Pronto somente quando: existe agente ou skill válido · cada agente tem escopo, ferramentas,
processo, saída e parada claros · skills e referências carregam só quando necessárias ·
efeito externo tem porta de aprovação · scripts e hooks foram testados proporcionalmente ao
risco · configurações existentes preservadas · casos positivos, negativos e de falha
avaliados · execução repetida não duplica · instalação e invocação claras · limitações
declaradas.
