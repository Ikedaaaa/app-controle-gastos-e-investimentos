# Persona

> **Atenção com privacidade.** Este prompt gera dois arquivos que **não devem
> ser versionados**: suas mensagens brutas e a persona derivada delas. Ambos
> vivem em `reference-files/discovery/`, que já está fora do controle de
> versão. Isso não é sobre dado financeiro sensível — é sobre exposição da
> sua forma de pensar e falar, que te identifica mesmo sem nome atrelado.
> Veja o README desta pasta para o raciocínio completo.

No treinamento original, a persona nasce de entrevistas com clientes reais —
a voz genuína de quem vive o problema é o que torna a persona útil para o
role-play do prompt `07` (sem ela, a persona vira arquétipo genérico, sem
força). Aqui você é o único usuário do produto, então a "entrevista" é
substituída por mensagens suas — de outra conversa, escritas no seu tom
natural, sem terem sido reprocessadas por uma IA — sobre o problema que o
app resolve e como você pensa sobre dinheiro.

O documento `analise-requisitos.md` já tem os fatos e decisões, mas é texto
já filtrado por uma IA — perdeu sua pessoalidade. As mensagens brutas
recuperam isso.

---

## Etapa 1: Reúna suas mensagens brutas (sem IA)

Junte mensagens suas (de outro chat, anotações, o que for) onde você fala,
com suas próprias palavras, sobre:
- Como você se sente em relação a controlar dinheiro (frustração, alívio,
  ansiedade, indiferença)
- Por que o bloco de notas te incomoda de verdade (não a lista técnica de
  problemas — o que te dá trabalho ou irrita na prática)
- Alguma vez que você adiou ou evitou atualizar as anotações, e por quê
- O que você quer sentir ao abrir o app (controle, tranquilidade, clareza)

Cole tudo, sem editar o tom, em
`reference-files/discovery/persona-mensagens-brutas.md`. Não peça para a IA
reescrever ou resumir nesta etapa — o valor está no texto não filtrado.

## Etapa 2: Gere a persona

```
Preciso construir uma persona para o meu próprio app pessoal de controle
financeiro. Esta persona deve ser realista o suficiente para que uma IA
consiga simular de forma convincente uma conversa como essa pessoa — ela
vai servir de base para um role-play de teste de estresse mais adiante
(prompt 07). Tenha esse objetivo em mente desde já, não só como reforço de
tom ao final, mas como algo a operacionalizar em cada seção da estrutura
abaixo.

Diferente de uma persona de cliente comum, esta representa a mim
mesmo — o único usuário do produto — então a fonte de evidência não é
entrevista com terceiros, é:

- reference-files/discovery/persona-mensagens-brutas.md — minhas próprias
  mensagens, no meu tom natural, sobre como me sinto em relação a controlar
  dinheiro e por que o método atual (bloco de notas) me incomoda
- docs/analise-requisitos.md — fatos e decisões já consolidados sobre o
  problema e o comportamento real documentado nas notas mensais

Leia os dois, foque principalmente no persona-mensagens-brutas.md e nas mensagens enumeradas, mesmo que pareça uma simples explicação, tente ver a forma como algo é explicado e a escolha de palavras, e como um tema realmente é pensado.

Construa a persona seguindo esta estrutura:

1. Como o problema aparece na minha vida real (não a lista de requisitos —
   a experiência de viver com ela)
2. Motivações centrais ao controlar minhas finanças
3. Frustrações reais com o método atual, citando trechos literais das minhas
   mensagens brutas sempre que possível
4. Contornos e estratégias que já uso hoje (o próprio bloco de notas e seus
   padrões, documentados em docs/estrutura-anotacoes.md se for útil)
5. Como tomo decisões sobre dinheiro (o que me faz hesitar, o que me faz
   agir sem pensar)
6. Mapa de empatia DIZ / PENSA / SENTE / FAZ, formatado como uma tabela
   markdown 2x2 (DIZ ao lado de PENSA na mesma linha, SENTE ao lado de FAZ
   na linha seguinte) — não como quatro listas separadas. A justaposição
   lado a lado é o que revela a tensão entre o que eu digo e o que
   realmente penso, ou entre o que sinto e o que de fato faço.
7. Estilo de comunicação — como eu de fato escrevo e argumento, baseado nas
   mensagens brutas, não num tom genérico de "usuário satisfeito"
8. Citações literais das minhas mensagens brutas (preserve exatamente como
   escrito, incluindo informalidade)
9. Uma seção final "Para Simulação de Conversa (Guia para IA)": uma lista
   de orientações diretas para quem for simular esta persona no role-play
   do prompt 07 — como reagir a uma ideia ruim, o que faria hesitar, o que
   faria se entusiasmar, que tom de resposta usar, o que nunca diria
   abertamente (dizendo de outra forma em vez disso). Baseie isso nos
   padrões já observados nas mensagens brutas, não em suposição genérica.

IMPORTANTE: mantenha meu tom real das mensagens brutas — não suavize,
não torne "profissional", não remova informalidade ou hesitação. Se a
persona ficar genérica, o role-play do prompt 07 não vai ter valor.

Salve em reference-files/discovery/persona.md.
```

## Etapa 3: Checagem rápida de qualidade

```
Releia a persona em reference-files/discovery/persona.md contra as mensagens
brutas de origem em reference-files/discovery/persona-mensagens-brutas.md.

Responda:
1. Alguma frase da persona poderia se aplicar a qualquer usuário de app
   financeiro, sem nenhuma conexão com as minhas mensagens específicas? Se
   sim, aponte e reescreva usando algo mais específico das mensagens.
2. O estilo de comunicação descrito reflete de fato como eu escrevo nas
   mensagens brutas, ou ficou um tom neutro de IA?
3. Existe alguma citação literal preservada, ou tudo foi paraphraseado?

Corrija o que estiver genérico demais.
```

---

**Verificação de conclusão:** você tem `persona.md` e
`persona-mensagens-brutas.md` em `reference-files/discovery/` (não
versionados). A persona tem pelo menos algumas citações literais suas e um
estilo de comunicação reconhecível como seu, não um tom neutro de app de
produto.
