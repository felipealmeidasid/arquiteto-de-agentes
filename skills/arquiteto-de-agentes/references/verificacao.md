# Verificação e gate de conclusão

> Leia antes de validar e antes de declarar qualquer coisa pronta.

---

## Verificação e avaliação

Casos realistas, não demonstração trivial.

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

Para skill com saída objetivamente avaliável, gere `evals/evals.json`:

```json
{
  "skill_name": "nome-da-skill",
  "evals": [
    {
      "id": 1,
      "prompt": "pedido real do usuario",
      "expected_output": "resultado esperado",
      "files": []
    }
  ]
}
```

Quando houver como, rode os casos com e sem a skill no mesmo ciclo e compare. Não ajuste a
skill para decorar os exemplos: falha recorrente vira regra geral, referência ou script.

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
