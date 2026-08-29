# Problema central e perguntas "Como poderíamos" (HMW)

No treinamento original, este passo existia para descobrir e escopar um
problema que o grupo ainda não sabia nomear com precisão. No seu caso o
problema já está nomeado com clareza no `analise-requisitos.md` ("substituir
completamente o bloco de notas como ferramenta de controle financeiro
mensal"). Por isso, este prompt não serve para descobrir o problema — serve
para declarar o problema central numa frase (para ancorar as decisões de UI
que vêm a seguir) e para gerar perguntas HMW específicas dos pontos
consolidados em `docs/discovery/01-pontos-abertos.md` (produzido no prompt
`01`, setup).

---

## Prompt

```
Já identificamos, no setup inicial, os pontos que docs/analise-requisitos.md
e docs/sugestoes-ui-navegacao.md deixaram abertos (principalmente decisões
de UI), consolidados em docs/discovery/01-pontos-abertos.md. Quero declarar
o problema central numa frase e depois gerar perguntas "Como poderíamos"
(HMW) só para esses pontos abertos — não para redescobrir o problema geral
do app, que já está resolvido no documento.

## Etapa 1: Declare o problema central

Releia docs/analise-requisitos.md, seção "Contexto e objetivo". Escreva o
problema central numa única frase, no formato:

[Eu] preciso de uma forma de [alcançar objetivo] porque [evidência do
documento]

Isso serve só para ancorar o trabalho a seguir — não precisa de aprovação
elaborada, é uma frase de referência.

## Etapa 2: Gere HMWs para os pontos abertos

Leia docs/discovery/01-pontos-abertos.md — é a lista consolidada dos pontos
abertos, com origem rastreada a cada seção do analise-requisitos.md ou do
sugestoes-ui-navegacao.md. Use exatamente essa lista, não uma recordação
solta do que foi discutido no setup.

Para cada ponto aberto dessa lista, gere 3-5 perguntas HMW. Regras:
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
docs/discovery/04-problema-e-hmw.md.
```

---

**Verificação de conclusão:** você tem uma frase clara de problema central e
um conjunto de HMWs, cada uma amarrada a um ponto aberto específico de
`docs/discovery/01-pontos-abertos.md` — nenhuma redescobre algo que o
`analise-requisitos.md` já decidiu.

---

## Correção pontual: regenerar apenas um ponto (sem afetar o resto)

Se, ao revisar o resultado, algum ponto específico desviar de escopo ou
precisar de ajuste, não regenere o documento inteiro — use um prompt
isolado, numa sessão nova, para não carregar o contexto da conversa que já
discutiu a solução (o que poderia influenciar indevidamente a geração).

Exemplo real de uso — o ponto 6 ("Tema e paleta de cores") desviou para
alternância entre tema claro/escuro em vez de tratar da escolha de paleta:

```
Preciso regenerar apenas um ponto de um documento de HMWs já existente —
não o documento inteiro.

Leia:
- docs/discovery/01-pontos-abertos.md, ponto 6 ("Tema e paleta de cores")
- docs/sugestoes-ui-navegacao.md, seção "Tema e cores"

O ponto 6 do documento docs/discovery/04-problema-e-hmw.md atual desviou de
escopo: gerou perguntas sobre alternância entre tema claro/escuro, quando o
ponto aberto real (conforme as duas fontes acima) é sobre qual paleta de
cores usar — os valores hex sugeridos (`#000000`, `#3CBA59`, `#8300A7` no
escuro; branco + azul claro no tema claro, sem hex definido) precisam ser
avaliados, mantidos, ou substituídos.

Gere 3-5 novas perguntas "Como poderíamos" (HMW) especificamente sobre
escolha de paleta de cores — não sobre mecânica de alternância entre temas
(isso já foi tratado separadamente e está fora deste ponto). Regras:
- Não introduza uma solução na própria pergunta (ex: não proponha uma cor
  específica dentro da pergunta)
- Foque na experiência de uso que a escolha de cor afeta (legibilidade,
  identidade visual, distinção entre categorias/valores), não na
  modelagem de dados
- Use verbos positivos (ajudar, permitir, mostrar), não negativos
- Para cada HMW gerada, verifique: existe só uma resposta óbvia, ou dá para
  imaginar mais de uma abordagem de UI genuinamente diferente? Descarte ou
  reescreva as que tiverem resposta implícita

Depois de gerar, abra docs/discovery/04-problema-e-hmw.md e substitua
apenas a seção "### 6. Tema e paleta de cores" pelo novo conteúdo — mantenha
o resto do documento intacto, sem alterar numeração ou qualquer outro ponto.
```

Use este padrão (isolar o ponto problemático, apontar as fontes corretas,
pedir substituição cirúrgica) sempre que precisar corrigir um ponto
específico sem regenerar tudo.
