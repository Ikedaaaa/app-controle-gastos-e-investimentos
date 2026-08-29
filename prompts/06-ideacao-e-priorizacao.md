# Ideação e priorização das decisões de UI

No treinamento original, este passo gerava e priorizava ideias de produto a
partir de três perspectivas (startup sem legado, setor adjacente, personas),
depois pontuava por valor de negócio x viabilidade comercial. Aqui não há
decisão de produto em aberto (o `analise-requisitos.md` já fechou isso) — o
que resta é decidir **como resolver em tela** cada ponto de UI ainda aberto,
consolidado em `docs/discovery/01-pontos-abertos.md` (prompt `01`) e com
HMWs geradas em `docs/discovery/04-problema-e-hmw.md` (prompt `04`). A
priorização também muda de critério: não é valor de negócio x viabilidade
comercial, é clareza de uso x esforço de implementação.

---

## Etapa 1: Gere alternativas por ponto aberto

```
Você é um designer de produto especializado em UX de apps financeiros para
Android, com foco em interfaces simples de operar no dia a dia, não em
soluções visualmente ousadas ou inéditas.

Tenho uma lista de pontos de UI ainda abertos, com o contexto completo (a
citação original do analise-requisitos.md ou sugestoes-ui-navegacao.md) em
docs/discovery/01-pontos-abertos.md, e as perguntas HMW derivadas de cada
ponto em docs/discovery/04-problema-e-hmw.md. Leia os dois — o primeiro dá
o porquê completo, o segundo as perguntas específicas a responder. Também
tenho referências de como outros apps resolvem problemas parecidos em
docs/discovery/02-panorama-solucoes-existentes.md.

Para cada ponto aberto, gere 2-3 alternativas concretas de solução de UI.
Use estas lentes ao gerar (nem toda lente precisa gerar algo para todo
ponto — use a que fizer sentido):

- **Referência externa**: adapte algo que um app existente já faz bem
  (do panorama em docs/discovery/02-panorama-solucoes-existentes.md),
  ajustado às minhas particularidades
- **Minimalismo extremo**: qual é a versão mais simples possível, mesmo que
  pareça "óbvia demais"?
- **Minha persona**: leia reference-files/discovery/persona.md e pense como
  eu, especificamente, reagiria a cada alternativa — o que me faria usar o
  app todo dia vs. abandonar de novo, como abandonei o bloco de notas

Para cada alternativa, seja concreto: descreva o comportamento em tela, não
apenas o conceito abstrato.

Salve em docs/discovery/06-alternativas-ui.md, organizado por ponto aberto.
```

## Etapa 2: Priorize

```
Leia docs/discovery/06-alternativas-ui.md. Para cada ponto aberto, avalie as
alternativas geradas em duas dimensões, nota de 1 a 5:

- **Clareza de uso**: o quão intuitivo é para mim entender e usar sem
  precisar de explicação. 1 = exige aprender um padrão novo. 5 = óbvio no
  primeiro uso.
- **Esforço de implementação**: quanto trabalho de Android/UI isso exige de
  mim. 1 = componente customizado complexo. 5 = componente padrão do
  Jetpack Compose/Material, sem lógica extra.

Monte uma tabela por ponto aberto com as notas e recomende uma alternativa
vencedora (ou uma combinação de duas, se fizer sentido). A recomendação não
precisa ser sempre a nota mais alta em ambos os critérios — diga seu
raciocínio quando escolher algo com esforço maior por ganho grande de
clareza, ou vice-versa.

Salve as tabelas e recomendações de volta em docs/discovery/06-alternativas-ui.md.
```

## Etapa 3 (opcional): teste de estresse rápido

Se alguma decisão te deixar em dúvida, use este teste antes de seguir para
o role-play do prompt `07`:

```
Para a decisão de UI [NOME DO PONTO], imagine que eu implementei a
alternativa recomendada e, um mês depois de usar o app no dia a dia, ela me
incomoda. O que provavelmente teria dado errado? Isso muda a recomendação?
```

---

**Verificação de conclusão:** você tem, para cada ponto de UI aberto, 2-3
alternativas concretas pontuadas e uma recomendação com raciocínio explícito
— pronto para testar contra a persona no prompt `07`.
