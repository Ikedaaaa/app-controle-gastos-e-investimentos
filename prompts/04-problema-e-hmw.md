# Problema central e perguntas "Como poderíamos" (HMW)

No treinamento original, este passo existia para descobrir e escopar um
problema que o grupo ainda não sabia nomear com precisão. No seu caso o
problema já está nomeado com clareza no `analise-requisitos.md` ("substituir
completamente o bloco de notas como ferramenta de controle financeiro
mensal"). Por isso, este prompt não serve para descobrir o problema — serve
para declarar o problema central numa frase (para ancorar as decisões de UI
que vêm a seguir) e para gerar perguntas HMW específicas dos pontos que o
prompt `01` (setup) já listou como abertos.

---

## Prompt

```
Já identificamos, no setup inicial, os pontos que o docs/analise-requisitos.md
deixou abertos (principalmente decisões de UI). Quero declarar o problema
central numa frase e depois gerar perguntas "Como poderíamos" (HMW) só para
esses pontos abertos — não para redescobrir o problema geral do app, que já
está resolvido no documento.

## Etapa 1: Declare o problema central

Releia docs/analise-requisitos.md, seção "Contexto e objetivo". Escreva o
problema central numa única frase, no formato:

[Eu] preciso de uma forma de [alcançar objetivo] porque [evidência do
documento ou da persona em reference-files/discovery/persona.md]

Isso serve só para ancorar o trabalho a seguir — não precisa de aprovação
elaborada, é uma frase de referência.

## Etapa 2: Gere HMWs para os pontos abertos

Para cada ponto aberto identificado no setup (prompt 01), gere 3-5 perguntas
HMW. Regras:
- Não introduza uma solução na própria pergunta
- Foque na experiência de uso, não na modelagem de dados (essa já está
  decidida no documento)
- Use verbos positivos (ajudar, permitir, mostrar), não negativos

Ex. de formato: "Como poderíamos mostrar o saldo em cascata sem sobrecarregar
a lista de itens?"

Agrupe as HMWs por ponto aberto (mesma seção de origem do documento).

## Etapa 3: Descarte o óbvio

Para cada HMW gerada, verifique: existe só uma resposta óbvia, ou dá para
imaginar mais de uma abordagem de UI genuinamente diferente? Descarte ou
reescreva as que têm resposta implícita.

Salve o problema central e as HMWs agrupadas em
docs/discovery/02-problema-e-hmw.md.
```

---

**Verificação de conclusão:** você tem uma frase clara de problema central e
um conjunto de HMWs, cada uma amarrada a um ponto aberto específico do
`analise-requisitos.md` — nenhuma redescobre algo que o documento já decidiu.
