# Role-play com a persona

> **Atenção com privacidade.** O diálogo gerado neste prompt é uma extensão
> dramatizada da persona — fica em `reference-files/discovery/`, **não
> versionado**. Só as conclusões objetivas (o que mudou de decisão, o que foi
> confirmado) vão para o arquivo versionado em `docs/discovery/`.

No treinamento original, o role-play testava se uma ideia de produto
resolvia o problema de uma persona de cliente. Aqui o teste é mais direto:
você mesmo, simulado pela IA a partir da sua persona, reagindo às decisões
de UI recomendadas no prompt `06`, antes de gastar esforço construindo o
protótipo.

---

## Prompt

```
Leia reference-files/discovery/persona.md por completo. A partir de agora,
você é a pessoa descrita nesse arquivo. Permaneça no personagem durante
toda esta conversa — use o estilo de comunicação e o tom descritos ali,
incluindo informalidade se for o caso.

Depois, leia docs/discovery/04-alternativas-ui.md, seção de recomendações —
são as decisões de UI recomendadas para o app de controle financeiro que
vou te apresentar uma por uma nesta conversa.

Para cada decisão, na ordem em que aparecem no arquivo, reaja como você
mesmo reagiria de verdade: faça as perguntas que você faria, levante as
preocupações que você levantaria, e se anime com o que genuinamente
resolveria sua frustração com o bloco de notas. Espere minha confirmação
antes de passar para a próxima decisão.

Depois que terminarmos todas, salve o resultado em
reference-files/discovery/roleplay-dialogo.md — crie o arquivo se ele ainda
não existir. Adicione uma seção "## Reação — [ponto de UI]" para cada
decisão testada, capturando o diálogo completo.
```

### Perguntas para guiar o teste, ponto por ponto

- Isso realmente resolveria a frustração que você tem hoje com o bloco de
  notas, ou só muda o formato do mesmo trabalho manual?
- Qual seria sua primeira reação ao ver essa tela? Confusão, alívio, ou
  indiferença?
- O que faria você abandonar essa tela depois de uma semana de uso, como
  você abandonou a seção "Previsto Rendimentos" das anotações?
- Falta alguma coisa antes de você confiar nessa solução para decisões
  financeiras reais?

### Teste uma alternativa descartada, de propósito

Para calibrar se a persona está reagindo de forma específica (e não só
concordando com tudo), teste também uma alternativa que foi descartada no
prompt `06`:

```
Antes de você achar que só existe uma resposta certa: também tínhamos
considerado [ALTERNATIVA DESCARTADA]. Por que isso não funcionaria tão bem
para você quanto a decisão recomendada? Seja direto.
```

Se a persona não conseguir articular uma diferença real entre as duas, a
decisão do prompt `06` provavelmente não estava tão clara quanto parecia —
volte lá antes de seguir.

---

## Depois do role-play: extraia as conclusões

```
Leia todo o diálogo em reference-files/discovery/roleplay-dialogo.md.

Para cada ponto de UI testado, resuma em 2-3 frases objetivas, sem citação
literal e sem tom pessoal:
- A decisão recomendada foi confirmada, ajustada ou descartada?
- Se ajustada ou descartada, qual é a nova direção?

Salve esse resumo objetivo em docs/discovery/05-conclusoes-roleplay.md — este
arquivo é versionado, então não deve conter diálogo literal nem detalhes que
revelem tom pessoal, só a conclusão prática de produto.
```

---

**Verificação de conclusão:** você tem o diálogo completo (não versionado)
em `reference-files/discovery/roleplay-dialogo.md`, e um resumo objetivo
(versionado) em `docs/discovery/05-conclusoes-roleplay.md` dizendo o que foi
confirmado, ajustado ou descartado para cada decisão de UI.
