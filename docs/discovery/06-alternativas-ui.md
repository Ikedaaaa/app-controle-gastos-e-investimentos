# Alternativas de UI para os pontos abertos

> Gerado a partir de `docs/discovery/01-pontos-abertos.md` (o quê e por quê),
> `docs/discovery/04-problema-e-hmw.md` (perguntas HMW a responder) e
> `docs/discovery/02-panorama-solucoes-existentes.md` (referências de
> mercado). Para os pontos que têm divisão "Resumo neutro" / "Contexto
> completo" no documento de origem (pontos 1, 6, 7, 9 e 10), as alternativas
> abaixo foram desenhadas a partir apenas do resumo neutro — nenhuma delas
> reaproveita as sugestões específicas que estavam registradas na parte de
> contexto completo (hex de cor, ordem sugerida, ideias já citadas etc.).
>
> Cada ponto traz 2-3 alternativas concretas, usando as lentes: referência
> externa (adaptação de um padrão já validado no panorama), minimalismo
> extremo (a versão mais simples possível) e persona (como o uso real,
> descrito em `reference-files/discovery/persona.md`, reage a cada opção).

---

## 1. Ordem de exibição dos três valores no quadro de resumo do período

**Problema (resumo neutro):** o quadro de resumo do período precisa exibir
Planejado inicialmente, Realizado e Previsto (vivo). A ordem está em aberto.

### Alternativa A — Destaque no valor de ação imediata (referência externa)
Inspirada na separação "Home vs. Reflect" da YNAB: o valor **Previsto
(vivo)** ganha destaque — fonte maior, no topo do quadro, cor reativa
(verde/vermelho conforme sinal). Planejado inicial e Realizado aparecem
abaixo, lado a lado, em fonte menor e cor neutra, como dados de apoio. A
lógica: o número que muda em tempo real é o que importa pra decisão do dia
a dia; os outros dois são referência histórica.

### Alternativa B — Um número por padrão, três sob demanda (minimalismo extremo)
Na tela do período, só aparece **um** valor grande (o Previsto vivo). Um
toque nele (ou um botão "detalhar") expande uma segunda linha revelando os
outros dois, Planejado e Realizado. A tela nunca mostra os três de uma vez
sem pedir — reduz a carga de leitura no caso comum (só quero saber quanto
tenho agora), sem eliminar o detalhe pra quando for preciso comparar.

### Alternativa C — Sequência causal com setas (persona)
Ordem fixa Planejado → Realizado → Previsto, com uma seta fina entre cada
par e um valor de diferença (delta) sobre a seta (ex: "Planejado R$2.000 →
+R$180 → Realizado R$2.180 → -R$95 → Previsto R$2.085"). Isso responde
diretamente ao HMW de "não exigir comparação manual": o delta já vem
calculado. Encaixa com o jeito do usuário de pensar em cascata (a mesma
lógica da subtração item a item do bloco de notas) e com a preferência dele
por entender a origem de cada número, não só o resultado final.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Destaque no Previsto | 4 | 5 |
| B — Um número, três sob demanda | 3 | 4 |
| C — Sequência causal com setas | 3 | 2 |

### Recomendação

**Alternativa A.** É a que empata melhor as duas dimensões: um número grande
em destaque com dois números de apoio menores é um padrão já familiar (não
exige aprendizado) e é só composição de `Text` com estilos diferentes, sem
lógica de estado ou desenho customizado. A Alternativa C tem o maior
potencial de "match" com o raciocínio em cascata do usuário, mas o esforço
de desenhar setas e deltas customizados não se paga frente ao ganho — o
mesmo raciocínio de cascata já é atendido, de forma mais simples, pela
Alternativa 1 do ponto 2 (breakdown com Meu/Terceiro/Crédito).

---

## 2. Forma de apresentação do breakdown de totais do período

**Problema:** o breakdown (Total no Crédito, Meu/Terceiro) é dado derivado;
falta decidir a forma de apresentação (seção fixa, aba própria, popup).

### Alternativa A — Bottom sheet ao tocar no resumo (referência externa)
Tocar no quadro de resumo do período abre um painel deslizante de baixo
(bottom sheet) com o breakdown completo. O fundo da tela do período continua
visível, escurecido, atrás do painel — o usuário nunca perde a noção de
onde estava. Fecha arrastando pra baixo ou tocando fora.

### Alternativa B — Accordion inline, sem tela nova (minimalismo extremo)
Nenhum modal, nenhuma aba nova. O próprio número do total no quadro de
resumo expande verticalmente ao ser tocado, revelando as linhas do
breakdown (Meu / Terceiro / Crédito) empilhadas ali mesmo, empurrando o
resto do conteúdo da tela pra baixo. Toque de novo recolhe. Zero navegação.

### Alternativa C — Aba fixa "Detalhes" ao lado da lista (persona)
Dentro da mesma tela do período, uma barra de abas simples (ex: "Lista" |
"Detalhes") alterna entre a lista de itens e o breakdown completo, sem
modal e sem gesto que precise ser lembrado depois. Dado que o usuário
valoriza previsibilidade e não gosta de esconder informação atrás de
interações que pode esquecer (ele mesmo relata perder rastreabilidade em
outros contextos), uma aba sempre visível bate melhor com o padrão dele do
que um popup ou um accordion que pode passar despercebido.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Bottom sheet | 4 | 4 |
| B — Accordion inline | 4 | 3 |
| C — Aba fixa "Detalhes" | 5 | 5 |

### Recomendação

**Alternativa C.** Abas são o componente mais padronizado do Compose
(`TabRow`/`ScrollableTabRow`) e não introduzem gesto nenhum a ser aprendido
— é literalmente trocar de aba, algo que qualquer usuário de Android já
sabe fazer. Além disso, bate com a preferência do usuário por informação
sempre visível e não escondida atrás de uma interação que pode ser
esquecida. O accordion (B) tem esforço parecido mas empurra o conteúdo da
tela, o que pode incomodar numa tela que já tem lista longa; o bottom sheet
(A) exige gerenciar estado de abertura/fechamento sem ganho real de clareza
sobre a aba.

---

## 3. Visão de navegação por calendário

**Problema:** terceira visão (opcional, baixa prioridade) para localizar
itens por data; não descartada, mas sem prioridade fechada.

### Alternativa A — Faixa de heatmap no topo da lista (referência externa)
Adaptação do heatmap de contribuições do GitHub: uma faixa horizontal fina
acima da lista do período, com um marcador por dia do período — mais escuro
quanto mais valor/itens naquele dia. Tocar num marcador rola a lista até o
primeiro item daquele dia. Não é uma tela nova, é um elemento dentro da
tela que já existe.

### Alternativa B — Índice de dias na borda (minimalismo extremo)
Sem heatmap, sem cor: uma coluna fina de números (1 a 31) fixada na borda
direita da tela, no estilo do índice alfabético de contatos do iOS. Tocar
ou arrastar o dedo sobre os números pula a lista direto para aquele dia.
Interação mínima, zero elemento visual novo além de texto pequeno.

### Alternativa C — Não construir calendário dedicado agora (persona)
Dado que o próprio documento de origem já classifica isso como baixa
prioridade, e o usuário evita manter telas extras que competem pela atenção
dele ("não quero dispersar... focado no contexto"), a recomendação é reduzir
para a Alternativa A (heatmap fino) e não avançar para uma tela de
calendário completa — ela resolve "perceber concentração de gastos" sem
exigir uma nova superfície de navegação para ele lembrar de usar.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Heatmap no topo | 3 | 2 |
| B — Índice de dias na borda | 2 | 2 |
| C — Não construir calendário agora | 5 | 5 |

### Recomendação

**Alternativa C**, ou seja, não construir nenhuma visão de calendário nesta
fase. O próprio documento de origem já marca este ponto como baixa
prioridade, e as duas alternativas de UI (A e B) exigem desenho de
componente customizado (nenhuma delas existe pronta no Material/Compose) —
esforço médio para um ganho que o usuário não pediu como prioridade. Vale
revisitar A (heatmap) só se, na prática de uso, a rolagem manual da lista
por data se mostrar um problema real.

---

## 4. Ordenação da lista de carteiras por proximidade de prazo/meta

**Problema:** ordenação por proximidade de prazo ou distância até a meta é
possível ganho de uso, sem decisão fechada.

### Alternativa A — Barra de progresso com marcador de prazo (referência externa)
Adaptação direta do padrão de Pot Goals do Monzo: cada card de carteira na
lista mostra uma barra de progresso da meta com uma marca vertical indicando
"onde o valor deveria estar hoje para bater o prazo". A lista pode ser
ordenada por quão distante o progresso real está desse marcador — uma
métrica só, que já combina prazo e valor.

### Alternativa B — Um seletor simples de critério (minimalismo extremo)
Um dropdown único no topo da lista de carteiras: "Mais recentes" / "Prazo
mais próximo" / "Mais perto da meta". Sem nenhum elemento visual novo nos
cards — só reordena a lista que já existe, com o critério escolhido.

### Alternativa C — Dois badges independentes, sem métrica combinada (persona)
Cada card mostra dois indicadores pequenos e separados: um de prazo (ex:
"📅 12 dias") e um de progresso (ex: "💰 82%"), sem combiná-los numa única
métrica de ordenação. O HMW de origem já aponta que os dois critérios podem
não coincidir — dado que o usuário prefere ver o dado bruto e decidir ele
mesmo o que pesa mais em cada caso (ele resiste a qualquer coisa que decida
por ele sem deixar entender o raciocínio), dois badges lado a lado preservam
essa decisão nas mãos dele em vez de escondê-la atrás de uma fórmula única.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Barra com marcador de prazo | 4 | 2 |
| B — Seletor simples de critério | 5 | 5 |
| C — Dois badges independentes | 4 | 4 |

### Recomendação

**Combinação B + C.** O seletor de critério (B) é a forma mais simples e já
conhecida de reordenar uma lista (dropdown padrão do Compose), e os badges
independentes (C) resolvem o problema real apontado no HMW — prazo e
progresso podem não coincidir, e o usuário prefere ver os dois dados brutos
em vez de uma métrica combinada que decide por ele. A barra com marcador de
prazo (A) é visualmente rica, mas exige desenhar um `Canvas` customizado
para o marcador de "onde deveria estar hoje" — esforço bem maior para um
ganho de clareza que os badges já entregam de forma mais direta.

---

## 5. Lista do que pode ser excluído dentro de um período

**Problema:** exclusão de item simples e isolado é de baixo risco; a lista
exata do que pode ou não ser excluído ainda precisa de definição.

### Alternativa A — Exclusão sempre reversível por alguns segundos (referência externa)
Padrão de "undo" do Gmail/Android: qualquer exclusão remove o item da lista
imediatamente (com uma pequena animação) e mostra uma barra inferior
("Item excluído — Desfazer") por alguns segundos. Não é preciso classificar
antecipadamente o que é "simples" ou "arriscado" — o mecanismo de desfazer
cobre os dois casos com o mesmo componente.

### Alternativa B — Um único diálogo, texto que varia (minimalismo extremo)
Sempre o mesmo diálogo de confirmação ("Excluir este item?"). Quando o item
tem vínculo com outro dado (por exemplo, um aporte que alimenta uma fatura),
o diálogo ganha uma linha extra listando o efeito colateral ("Isso também
remove o resgate vinculado na fatura de outubro"). Não existem dois
componentes diferentes, só um texto que se adapta ao caso.

### Alternativa C — Revisão de cascata antes de confirmar vínculos (persona)
Para itens sem nenhum vínculo, a exclusão é direta (diálogo simples, um
toque). Para itens vinculados a outro dado (fatura, resgate, composição), o
app mostra uma tela de revisão explícita listando tudo que será afetado,
exigindo confirmação separada por item afetado antes de excluir. Dado que a
maior frustração relatada pelo usuário é perder rastreabilidade sem
conseguir provar de onde veio um valor (caso do resgate do Nubank), vale
trocar velocidade por clareza total nesse cenário específico, mesmo custando
uma tela extra.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Undo reversível | 4 | 3 |
| B — Diálogo único, texto variável | 5 | 5 |
| C — Revisão de cascata para vínculos | 3 | 2 |

### Recomendação

**Combinação B + C.** Usar o diálogo simples (B) como padrão para todos os
itens, e só quando o item tiver vínculo detectado, trocar para a tela de
revisão de cascata (C) listando os efeitos colaterais. Isso evita construir
o mecanismo de undo com snackbar e janela de tempo (A) — que exige manter
estado temporário do item "excluído mas recuperável" — e vai direto ao que
o usuário mais valoriza: nunca perder rastreabilidade de vínculos
financeiros, mesmo que isso custe uma tela extra só nos casos vinculados
(que devem ser a minoria, não a maioria dos itens de um período).

---

## 6. Tema e paleta de cores

**Problema (resumo neutro):** falta paleta de cores para tema escuro e
claro; nenhuma cor está decidida.

### Alternativa A — Paleta tonal gerada a partir de uma cor de marca (referência externa)
Adaptação do padrão Material Design 3 (dynamic color): define-se apenas
**uma** cor de marca (accent), e o restante da paleta — fundo, superfícies,
texto, estados — é derivado dela por regras de contraste tonal, geradas
automaticamente para os dois temas. Evita escolher manualmente cor por cor
e garante contraste mínimo de leitura por construção, não por revisão
manual.

### Alternativa B — Quatro cores fixas, cor não carrega significado de categoria (minimalismo extremo)
Paleta reduzida a 4 cores: 1 de fundo, 1 de texto, verde para saldo
positivo, vermelho para saldo negativo. Categorias de gasto são
diferenciadas só por ícone, nunca por cor — resolve o HMW de "distinguir
categorias sem memorizar cores" simplesmente removendo a cor dessa função,
em vez de tentar caber muitas categorias em poucas cores memorizáveis.

### Alternativa C — Paleta como regra derivada, não escolha estética avulsa (persona)
Mesma ideia da Alternativa A, mas formalizada como decisão documentada: 1
cor de marca + fórmula de derivação (ex: ajuste de luminosidade/saturação
em HSL) registrada como regra, não como "essa cor ficou bonita". O usuário
já demonstra confiar mais em sistemas com regra explícita e questionável do
que em decisões estéticas soltas — tratar a paleta como algo gerado por
fórmula (e documentável) tende a engajar mais do que uma tabela de cores
escolhida "no olho".

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Paleta tonal derivada (M3 dynamic color) | 5 | 5 |
| B — 4 cores fixas, sem cor por categoria | 5 | 5 |
| C — Paleta como regra documentada | 5 | 4 |

### Recomendação

**Alternativa A.** O Jetpack Compose Material 3 já tem suporte nativo a
`ColorScheme` gerado a partir de uma seed color (`dynamicColorScheme` /
ferramentas como Material Theme Builder), então é literalmente o caminho de
menor esforço, e o resultado (contraste garantido, tema claro e escuro
coerentes) é transparente pro usuário — ele nunca precisa "aprender" nada,
só vê a cor. A Alternativa B evita completamente o problema de
categoria-por-cor, mas descarta uma ferramenta de leitura rápida (cor)
sem necessidade, já que a derivação automática da paleta tonal em A
comporta cores de categoria sem comprometer contraste. C é essencialmente A
formalizada como decisão documentada — vale adotar a mesma escolha técnica
e só registrar a regra de derivação usada, sem que isso mude a implementação.

---

## 7. Diferenciação visual entre faturas de cartões diferentes

**Problema (resumo neutro):** com mais de um cartão de crédito, a lista do
fluxo precisa diferenciar visualmente de qual cartão é cada fatura; nenhuma
solução decidida.

### Alternativa A — Faixa de cor lateral por cartão (referência externa)
Padrão comum em apps bancários (Nubank, Inter): cada cartão cadastrado tem
uma cor própria definida no cadastro. O item de fatura na lista mostra uma
faixa fina (2-3px) na borda esquerda do item, na cor daquele cartão — sem
interferir no ícone de categoria do gasto, que continua central.

### Alternativa B — Só o nome do cartão em texto (minimalismo extremo)
Nenhum elemento visual novo: o nome curto do cartão (ex: "Nubank", "Inter")
aparece como texto secundário, discreto, abaixo da descrição do item.
Resolve a identificação sem introduzir cor, ícone extra ou faixa.

### Alternativa C — Faixa de cor + nome do cartão juntos (persona)
Combina as duas alternativas anteriores: a faixa de cor lateral permite
reconhecimento rápido ao rolar a lista (leitura por escaneamento), e o nome
do cartão em texto garante certeza absoluta sem depender de memorizar qual
cor é qual cartão. Como o usuário é explícito sobre não querer que "nem 1
centavo passe despercebido" e não confia em atalhos que dependem de memória
(ele mesmo relata escapar do próprio padrão no bloco de notas por falta de
validação), a redundância cor+texto entrega tanto velocidade quanto certeza.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Faixa de cor lateral | 3 | 4 |
| B — Só nome do cartão em texto | 5 | 5 |
| C — Faixa de cor + nome do cartão | 4 | 4 |

### Recomendação

**Alternativa C.** A faixa de cor por si só (A) exige que o usuário
memorize qual cor é qual cartão — algo que ele mesmo demonstra não confiar
(relata escapar do próprio padrão por falta de validação externa). O texto
por si só (B) é o mais simples de implementar, mas exige leitura linha a
linha, o que é mais lento para "escanear" a lista com vários cartões. A
combinação custa pouco esforço adicional sobre A (é só acrescentar um
`Text`) e entrega reconhecimento rápido por cor com confirmação certa por
texto — vale o esforço levemente maior.

---

## 8. Prioridade do ícone de compra no crédito: método de pagamento vs. destino

**Problema:** decidir se o ícone de uma compra no crédito prioriza o método
de pagamento (cartão) ou o destino da compra (loja).

### Alternativa A — Ícone de destino com selo de crédito (referência externa)
Padrão comum em apps bancários: o ícone principal do item continua sendo o
destino/categoria (loja, mercado, etc.), e um selo pequeno de cartão de
crédito aparece sobreposto no canto inferior direito desse ícone — o mesmo
recurso visual usado por bancos para indicar parcelamento.

### Alternativa B — Ícone único, crédito só em texto (minimalismo extremo)
Nenhum ícone duplo: o ícone é sempre o de destino/categoria, e a informação
de crédito aparece apenas como texto ao lado do valor (ex: "no crédito" ou
"3/12"). Resolve o conflito de espaço visual simplesmente não usando ícone
para essa segunda informação.

### Alternativa C — Selo pequeno é suficiente (persona)
A Alternativa A atende melhor o HMW de "reconhecer o tipo de gasto numa
leitura rápida sem abrir o detalhe": o selo pequeno de crédito fica sempre
visível (diferente da Alternativa B, que exige ler o texto), mas não disputa
espaço com a categoria porque é proporcionalmente pequeno. Como o usuário
quer controle total sem esforço extra de leitura, um selo sempre presente
bate melhor com esse objetivo do que depender de texto adicional.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Ícone de destino + selo de crédito | 4 | 3 |
| B — Ícone único, crédito em texto | 4 | 5 |
| C — Selo pequeno (reforço da A) | 4 | 3 |

### Recomendação

**Alternativa A/C.** O selo sobreposto (`Box` com `BadgedBox` do Material 3,
componente já pronto no Compose) tem esforço baixo e entrega leitura visual
instantânea sem abrir o item — o que a Alternativa B não entrega, já que
exige ler texto para confirmar o método de pagamento. O ganho de clareza de
manter o selo sempre visível compensa o esforço levemente maior frente à
opção só-texto.

---

## 9. Tela dedicada por classe de ativo vs. tudo dentro do gráfico de composição

**Problema (resumo neutro):** o usuário precisa ver o detalhe de uma classe
de ativo específica dentro da carteira; não decidido se é tela própria,
parte do gráfico de composição, ou outra abordagem.

### Alternativa A — Drill-down direto na fatia do gráfico (referência externa)
Padrão comum em apps de investimento: tocar numa fatia do gráfico de
composição da carteira expande uma segunda camada — uma lista ou
subgráfico só dos itens daquela classe — sem sair da tela da carteira. O
contexto do todo nunca desaparece, porque o gráfico original continua
visível acima ou ao lado.

### Alternativa B — Filtro por chips sobre a lista existente (minimalismo extremo)
Sem gráfico expansível, sem tela nova: chips de filtro (ex: "Renda Fixa" |
"Exterior" | "Ações") acima da lista de itens da carteira que já existe.
Tocar num chip simplesmente filtra a mesma lista — zero componente visual
novo além dos próprios chips.

### Alternativa C — Filtro simples é suficiente para o padrão de uso (persona)
A Alternativa B tende a se encaixar melhor: o usuário evita manter mais
telas ou interações para lembrar ("não quero dispersar... focado no
contexto"), e chips de filtro entregam o detalhe por classe sem exigir uma
navegação ou um gesto de expansão que pode ser esquecido. É a resposta mais
direta ao HMW de "explorar uma classe sem perder a visão do todo" — a lista
volta ao estado completo com um toque no chip já selecionado.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Drill-down na fatia do gráfico | 3 | 2 |
| B — Filtro por chips | 5 | 5 |
| C — Filtro simples (mesma ideia da B) | 5 | 5 |

### Recomendação

**Alternativa B.** `FilterChip`/`FlowRow` já são componentes prontos do
Material 3 no Compose, sem lógica de gráfico interativo customizado — o
drill-down direto na fatia (A) exigiria detectar toque em segmentos de
gráfico (não é um componente padrão, teria que ser desenhado com `Canvas` ou
biblioteca de gráficos com suporte a hit-testing por fatia). Os chips também
são o padrão de filtro mais reconhecível no Android atual, então não exigem
aprendizado. C é a mesma escolha da B, só reafirmada pela lente da persona.

---

## 10. Tela inicial ao abrir o app

**Problema (resumo neutro):** o que aparece ao abrir o app não está
decidido.

### Alternativa A — Período por padrão, patrimônio a um toque (referência externa)
Inspirado na separação Home/Reflect da YNAB: o app sempre abre no período
atual (a tela de ação do dia a dia), com um botão ou aba de acesso rápido no
topo para a visão consolidada de patrimônio. Nenhuma escolha é forçada na
abertura, mas há uma prioridade clara.

### Alternativa B — Sempre o período, sem exceção (minimalismo extremo)
O app abre sempre no período atual, sem nenhuma tela de patrimônio na
abertura e sem configuração para mudar isso. Quem quiser ver o patrimônio
navega manualmente pelo menu, como qualquer outra tela do app.

### Alternativa C — Período com um número de patrimônio fixado no topo (persona)
O usuário descreve a visão de patrimônio consolidado como uma motivação
forte (algo que hoje só existe "na cabeça", somando apps de bancos
diferentes) e antecipa que vai ser "chocante" ver o número de verdade — e
liga isso diretamente à motivação de continuar investindo. Abrir sempre no
período (Alternativa B) resolve a ação do dia a dia, mas esconde o dado que
mais sustenta o hábito. Alternativa: abrir no período, mas fixar um único
número — o patrimônio total — no topo da tela, sem virar uma tela própria.
Não força escolha entre as duas visões nem exige navegação extra para ver o
número que mais importa emocionalmente pra ele.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Período + acesso rápido ao patrimônio | 5 | 4 |
| B — Sempre o período, sem exceção | 5 | 5 |
| C — Período + número de patrimônio fixado no topo | 4 | 4 |

### Recomendação

**Alternativa C.** Embora B seja a mais simples de implementar (nenhum
elemento novo na tela), ela esconde justamente o dado que o usuário liga
diretamente à motivação de manter o hábito de investir ("vai ser chocante
ver o número de verdade"). Fixar um único valor (patrimônio total) no topo
da tela do período é uma mudança pequena de implementação (um `Text`
consultando um total já calculado) frente ao ganho emocional relatado na
persona — vale o esforço levemente maior que B por esse motivo específico,
mesmo sem ser a opção de maior nota combinada.

---

## 11. Comportamento de gráficos em tela pequena

**Problema:** decidir entre gráfico abaixo do formulário com scroll, ou
navegação para uma tela dedicada ao gráfico.

### Alternativa A — Gráfico compacto inline + expandir sob demanda (referência externa)
Padrão comum em apps de investimento mobile: um gráfico compacto
(tipo sparkline) fica sempre visível dentro do formulário, sem ocupar muito
espaço. Um botão "expandir" abre o gráfico em tela cheia (com opção de
girar para paisagem), mantendo o formulário intacto por trás, pronto para
voltar.

### Alternativa B — Só scroll, sem modo especial (minimalismo extremo)
O gráfico fica sempre abaixo do formulário na mesma tela, e a tela
simplesmente rola mais quando o gráfico é mais alto. Nenhum botão de
expandir, nenhuma tela dedicada, nenhum modo paisagem.

### Alternativa C — Expandir por escolha explícita, nunca por padrão (persona)
O usuário evita perder o contexto do que está fazendo no meio de uma tarefa
("se eu deixasse para depois, poderia esquecer algum detalhe"). A
Alternativa A atende melhor esse padrão: o formulário nunca sai de vista por
padrão, e a expansão do gráfico só acontece quando ele mesmo decide tocar em
"expandir" — ele controla a troca de contexto, em vez do app forçar um
scroll longo que empurra o formulário para fora da tela sem aviso.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Gráfico compacto inline + expandir | 4 | 3 |
| B — Só scroll, sem modo especial | 5 | 5 |
| C — Expandir por escolha explícita (mesma ideia da A) | 4 | 3 |

### Recomendação

**Alternativa A.** A opção B é mais simples de implementar, mas em tela
pequena o scroll pode empurrar o formulário inteiro fora de vista sem
aviso — algo que conflita diretamente com o padrão da persona de não querer
perder o contexto de uma tarefa em andamento. O botão de expandir (`Dialog`
ou navegação para uma tela cheia, componentes padrão do Compose) tem
esforço só um pouco maior que o scroll simples e devolve ao usuário o
controle sobre quando trocar de contexto, em vez do app decidir isso por
ele via necessidade de rolagem.
