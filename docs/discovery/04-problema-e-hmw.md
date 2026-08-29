# Problema central e HMWs dos pontos abertos

## Problema central

Eu preciso de uma forma de substituir completamente o bloco de notas como
ferramenta de controle financeiro mensal, porque ele exige todos os
cálculos feitos à mão, duplica dados mês a mês, não oferece nenhuma visão
analítica dos dados e mantém risco constante de inconsistência.

*(Fonte: `docs/analise-requisitos.md`, seção "Contexto e objetivo")*

Esta frase ancora as decisões de UI a seguir — não é o objeto de discussão
deste documento, que trata apenas dos pontos abertos listados em
`docs/discovery/01-pontos-abertos.md`.

---

## HMWs por ponto aberto

### 1. Ordem de exibição dos três valores no quadro de resumo do período
*(Origem: `analise-requisitos.md`, Seção 4 — Planejado inicial / Realizado / Previsto)*

- Como poderíamos ajudar o usuário a distinguir rapidamente o valor
  planejado inicialmente do valor previsto atual, mesmo quando os dois
  números estão próximos?
- Como poderíamos mostrar a evolução entre o que foi planejado e o que foi
  realizado sem exigir que o usuário compare os três números manualmente?
- Como poderíamos ajudar o usuário a identificar de imediato qual dos três
  valores é o mais relevante no momento em que ele olha para o quadro?
- Como poderíamos apresentar os três valores de forma que a leitura sirva
  tanto para quem quer uma visão geral rápida quanto para quem quer
  comparar em detalhe?

### 2. Forma de apresentação do breakdown de totais do período
*(Origem: `analise-requisitos.md`, Seção 4 — Total no Crédito, Meu/Terceiro)*

- Como poderíamos mostrar o breakdown de totais (crédito, meu/terceiro) sem
  sobrecarregar a tela principal do período?
- Como poderíamos ajudar o usuário a alternar entre a visão resumida e a
  visão detalhada do breakdown sem perder o contexto de onde estava?
- Como poderíamos permitir que o usuário acesse o detalhamento a partir de
  diferentes pontos da tela do período, e não só de um único lugar fixo?
- Como poderíamos mostrar quais valores pertencem a terceiros de forma que
  isso fique claro numa leitura rápida, sem exigir atenção linha a linha?

### 3. Visão de navegação por calendário
*(Origem: `analise-requisitos.md`, Seção 4 — terceira visão, opcional)*

- Como poderíamos ajudar o usuário a localizar um item específico por data
  sem precisar rolar toda a lista do período?
- Como poderíamos mostrar a distribuição dos gastos ao longo dos dias do
  período de forma visualmente rápida de entender?
- Como poderíamos permitir que o usuário alterne entre a visão de lista e
  uma visão temporal sem perder a posição em que estava?
- Como poderíamos ajudar o usuário a perceber dias com concentração de
  gastos sem exigir que ele abra uma tela de calendário completa?

### 4. Ordenação da lista de carteiras por proximidade de prazo/meta
*(Origem: `analise-requisitos.md`, Seção 7 — meta de prazo e quantia)*

- Como poderíamos ajudar o usuário a identificar rapidamente quais
  carteiras estão mais próximas de atingir a meta?
- Como poderíamos mostrar a proximidade do prazo de cada carteira sem
  exigir que o usuário calcule mentalmente a diferença de datas?
- Como poderíamos permitir que o usuário escolha entre diferentes critérios
  de organização das carteiras (prazo, distância da meta, criação) de forma
  simples?
- Como poderíamos ajudar o usuário a diferenciar uma carteira perto do
  prazo de uma carteira perto de bater a meta de valor, quando os dois
  critérios não coincidem?

### 5. Lista do que pode ser excluído dentro de um período
*(Origem: `analise-requisitos.md`, Seção 22 — exclusão permanente de dados)*

- Como poderíamos ajudar o usuário a entender quais itens podem ser
  excluídos com segurança e quais merecem mais atenção antes da exclusão?
- Como poderíamos comunicar as consequências de excluir um item antes de a
  exclusão ser confirmada?
- Como poderíamos permitir que o usuário reverta uma exclusão recente sem
  precisar recriar o item manualmente do zero?
- Como poderíamos diferenciar visualmente itens de exclusão simples de
  itens que exigem uma confirmação mais cuidadosa?

### 6. Tema e paleta de cores
*(Origem: `analise-requisitos.md`, `docs/discovery/01-pontos-abertos.md` ponto 6;
`sugestoes-ui-navegacao.md` — "Tema e cores")*

- Como poderíamos escolher uma paleta que mantenha os valores (positivos e
  negativos) legíveis mesmo para quem tem dificuldade de percepção de cor?
- Como poderíamos escolher cores que reforcem uma identidade visual própria
  do app sem comprometer o contraste de leitura dos números e textos?
- Como poderíamos ajudar o usuário a distinguir categorias diferentes de
  gasto através da cor, sem exigir memorização de um código extenso de
  cores?
- Como poderíamos manter a paleta reconhecível como a mesma identidade
  visual ao migrar do tema claro para o escuro, mesmo com tons ajustados
  entre os dois?
- Como poderíamos escolher uma paleta que funcione bem tanto em elementos
  de dados (gráficos, categorias, valores) quanto em elementos de
  interface (fundo, botões, navegação), sem que um grupo de cores compita
  visualmente com o outro?

### 7. Diferenciação visual entre faturas de cartões diferentes
*(Origem: `sugestoes-ui-navegacao.md` — "Ícones genéricos por tipo de gasto")*

- Como poderíamos ajudar o usuário a diferenciar visualmente a fatura de um
  cartão da fatura de outro dentro da mesma lista de itens?
- Como poderíamos mostrar qual cartão está associado a cada fatura sem
  exigir que o usuário abra o detalhe do item?
- Como poderíamos ajudar o usuário a reconhecer o cartão certo rapidamente
  mesmo tendo vários cartões cadastrados?

### 8. Prioridade do ícone de compra no crédito: método de pagamento vs. destino
*(Origem: `sugestoes-ui-navegacao.md` — "Ícones genéricos por tipo de gasto")*

- Como poderíamos ajudar o usuário a identificar que uma compra foi feita
  no crédito, mesmo quando o ícone também representa a loja/destino?
- Como poderíamos mostrar tanto o método de pagamento quanto o destino da
  compra sem que um disputa espaço visual com o outro na linha do item?
- Como poderíamos ajudar o usuário a reconhecer o tipo de gasto numa
  leitura rápida da lista, sem precisar abrir o detalhe do item?

### 9. Tela dedicada por classe de ativo vs. tudo dentro do gráfico de composição
*(Origem: `sugestoes-ui-navegacao.md` — "Tela dedicada por classe de ativo")*

- Como poderíamos ajudar o usuário a explorar o desempenho de uma classe de
  ativo específica (ex: exterior) sem perder a visão do todo da carteira?
- Como poderíamos permitir que o usuário aprofunde numa classe de ativo a
  partir do gráfico de composição, sem sair do contexto da carteira atual?
- Como poderíamos mostrar o detalhe de uma classe de ativo de forma
  proporcional à importância dela dentro da carteira do usuário?

### 10. Tela inicial ao abrir o app
*(Origem: `sugestoes-ui-navegacao.md` — "Tela inicial / o que aparece ao abrir o app")*

- Como poderíamos ajudar o usuário a chegar direto na informação mais
  relevante do momento assim que abre o app?
- Como poderíamos mostrar tanto o período atual quanto uma visão
  consolidada do patrimônio sem forçar uma escolha única na abertura?
- Como poderíamos permitir que o usuário ajuste o que vê primeiro ao abrir
  o app, de acordo com o que costuma consultar com mais frequência?

### 11. Comportamento de gráficos em tela pequena
*(Origem: `sugestoes-ui-navegacao.md` — "Comportamento de gráficos em tela pequena")*

- Como poderíamos ajudar o usuário a visualizar um gráfico complexo com
  clareza mesmo numa tela pequena?
- Como poderíamos permitir que o usuário explore um gráfico com mais
  detalhe sem perder o contexto da tela/formulário onde estava?
- Como poderíamos mostrar a informação essencial de um gráfico mesmo antes
  de o usuário decidir expandir ou navegar para uma tela dedicada?

---

**Nota sobre a Etapa 3 (descarte do óbvio):** HMWs cuja única resposta
plausível seria a decisão já sugerida no documento original (ex.
"como poderíamos deixar a ordem configurável" — resposta óbvia e já
implícita na própria observação do documento) foram descartadas ou
reescritas para focar na experiência subjacente (o que o usuário precisa
perceber ou fazer), abrindo espaço para mais de uma abordagem de UI.
