# Alternativas de UI para os pontos abertos

> Insumo de saída dos prompts `04-problema-e-hmw.md` (perguntas HMW) e
> `02-panorama-solucoes-existentes.md` (referências de mercado), cruzados com
> `01-pontos-abertos.md` (contexto original) e `reference-files/discovery/persona.md`
> (como o usuário único do app reage a cada tipo de solução). Nenhuma
> alternativa aqui é decisão fechada — é matéria-prima para priorização.
>
> Cada ponto tem 2-3 alternativas concretas, descritas em termos de
> comportamento de tela, não de conceito abstrato. As lentes usadas em cada
> alternativa estão marcadas entre parênteses no título.

---

## 1. Ordem de exibição dos três valores (Planejado / Realizado / Previsto)

### Alternativa A — Peso visual decrescente, ordem fixa (minimalismo extremo)
Os três valores ficam sempre visíveis, um ao lado do outro (ou em três
linhas), na ordem Realizado → Previsto → Planejado inicial. A diferenciação
não vem de esconder nada, vem de tamanho de fonte e peso: Realizado em fonte
grande e cor forte (é o "fato consumado"), Previsto em fonte média,
Planejado inicial em fonte pequena e cor neutra/cinza, como uma legenda de
referência. Nenhum toque extra é necessário para ver qualquer um dos três.

### Alternativa B — Métrica-herói + comparação por delta (referência externa: YNAB Reflect)
Em vez de três números competindo por atenção, o card do período mostra só o
**Previsto** em destaque, grande, no topo. Imediatamente abaixo, um badge
pequeno mostra a diferença calculada automaticamente contra o Planejado
inicial (ex.: "↑ R$120 vs. planejado"), sem exigir que o usuário subtraia
mentalmente. O Realizado fica disponível como segunda linha, menor. Isso
responde diretamente ao HMW de "mostrar evolução sem comparação manual".

### Alternativa C — Estilo de traço por papel do valor (minha persona)
Como ele pensa em termos de "o que é fato" vs. "o que é projeção" vs. "o que
foi a intenção original" (a lógica de cascata de subtração do bloco de
notas), cada valor usa um estilo visual que reforça esse papel: Planejado
inicial com borda pontilhada/tracejada (é uma "foto" congelada do passado),
Realizado com preenchimento sólido cheio (é dado consumado), Previsto com
preenchimento sólido mas com um pequeno ícone de "atualização em tempo real"
(relógio ou seta circular) ao lado. Ele já pensa em modelagem de dados e
estados — um código visual que espelha "snapshot vs. atual vs. confirmado"
tende a fazer mais sentido para ele do que só ordem ou tamanho.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Peso visual decrescente, ordem fixa | 5 | 5 |
| B — Métrica-herói + delta | 4 | 3 |
| C — Estilo de traço por papel do valor | 3 | 2 |

**Recomendação: Alternativa A.** Não exige nenhum padrão novo de leitura —
é hierarquia de texto, algo que qualquer pessoa já sabe interpretar — e usa
só `Text` com `fontSize`/`fontWeight`/`color` diferentes, sem componente
customizado. A Alternativa C é conceitualmente mais fiel a como ele pensa
(snapshot vs. atual vs. confirmado), mas depende de borda tracejada
customizada (`Canvas`/`Modifier.drawBehind` com `PathEffect.dashPathEffect`)
e cria uma "legenda" que só ele entenderia sem explicação — o ganho de
expressividade não paga o custo nem o risco de confundir uma leitura futura.
Vale reconsiderar C só se, na prática, a Alternativa A não bastar para
distinguir os três valores.

---

## 2. Forma de apresentação do breakdown de totais do período

### Alternativa A — Chips resumidos com drill-down em bottom sheet (minimalismo extremo)
Abaixo do quadro de resumo principal, uma linha fina de chips pequenos:
"Crédito: R$X" · "Terceiros: R$Y". Cada chip é tocável; tocar abre um bottom
sheet (não uma tela nova, não uma aba) com a lista detalhada daquele
recorte. Fechar o bottom sheet volta exatamente para onde estava no período,
sem perda de contexto — responde ao HMW de alternar visões sem se perder.

### Alternativa B — Drill-down no mesmo nível de navegação, sem aba dedicada (referência externa: YNAB Home vs. Reflect, adaptado)
Ao contrário do padrão de aba separada da YNAB, o breakdown continua dentro
da tela do período (justificado no panorama: o app funciona por período, não
por conta corrente contínua). O acesso ao detalhamento pode partir de mais
de um ponto da tela — tocar no total geral do card de resumo, ou tocar num
item específico da lista que pertence a um cartão/terceiro — e todos levam à
mesma tela de detalhe, evitando um único ponto fixo de entrada (segundo HMW
do ponto 2).

### Alternativa C — Mini barra de proporção Meu/Terceiro (minha persona)
Como ele quer que valores de terceiro nunca passem despercebidos, o card de
resumo ganha uma barra horizontal fina e proporcional (estilo barra de
progresso), colorida em duas cores — uma para "Meu", outra para "Terceiro" —
sem nem precisar ler os números para perceber a proporção num piscar de
olhos. Além disso, na lista principal do período, itens que pertencem a
terceiro recebem uma marca discreta (uma faixa colorida fina na lateral
esquerda do item, ou um badge com iniciais — "P." para Pai, por exemplo),
mantendo a informação visível linha a linha sem exigir abrir detalhe.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Chips + bottom sheet | 4 | 3 |
| B — Drill-down no mesmo nível, múltiplos pontos de entrada | 4 | 4 |
| C — Mini barra de proporção Meu/Terceiro | 5 | 3 |

**Recomendação: combinar B + C.** B evita construir um componente de bottom
sheet extra só para isso — reaproveita navegação padrão (`NavHost`/tela de
detalhe) chamada a partir de mais de um ponto, o que é barato e resolve
diretamente o HMW de múltiplos pontos de entrada. C tem a maior clareza do
grupo (a proporção visual é entendida sem ler número nenhum) e cobre a
exigência mais forte da persona — nenhum valor de terceiro pode passar
despercebido — a um custo de implementação moderado: `LinearProgressIndicator`
do Material3 não suporta duas cores nativamente, então precisa de um
`Canvas`/`Row` com dois retângulos proporcionais, mas é um componente pequeno
e isolado, não uma tela nova. A Alternativa A fica de fora por ser
redundante com B (mesmo objetivo, custo de bottom sheet a mais) sem ganho de
clareza correspondente.

---

## 3. Visão de navegação por calendário

### Alternativa A — Não construir calendário: mini-heatmap acima da lista (minimalismo extremo + referência externa: heatmap de contribuições do GitHub)
Em vez de uma tela de calendário completa, uma faixa horizontal fina no topo
da lista do período mostra um ponto por dia, com intensidade de cor
proporcional ao valor gasto naquele dia (dias sem lançamento ficam
neutros/cinza claro). Tocar um ponto rola a lista até o primeiro item daquele
dia. Não é uma tela nova, é um componente pequeno acoplado à lista existente
— atende ao HMW de "perceber concentração de gastos" sem o custo de construir
e manter uma tela de calendário completa para algo de baixa prioridade.

### Alternativa B — Busca por data em vez de navegação temporal (minimalismo extremo)
Um ícone de busca/filtro na barra superior abre um seletor de data simples
(o componente nativo de date picker do Android). Escolher uma data rola a
lista até o item mais próximo daquela data. Resolve o HMW de "localizar item
por data sem rolar tudo" com o menor esforço de implementação possível, sem
introduzir nenhum conceito visual novo.

### Alternativa C — Não priorizar por ora; registrar como aceito, mas fora do MVP (minha persona)
Dado que a ordem no app é manual e cronológica pela sequência real dos
fatos (não pela data, que nem sempre é preenchida — conforme o panorama já
identificou como particularidade sua), uma visão de calendário pode
literalmente mentir sobre a ordem real dos eventos. Como ele valoriza
precisão acima de tudo ("não quero que nem 1 centavo passe despercebido"),
a alternativa mais alinhada à persona é não implementar nenhuma visão de
calendário agora, e revisar essa decisão só se e quando o preenchimento de
data se tornar consistente o suficiente para não distorcer a leitura.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Mini-heatmap acima da lista | 3 | 2 |
| B — Busca por data (date picker nativo) | 5 | 5 |
| C — Não implementar agora | 5 | 5 |

**Recomendação: Alternativa C.** O próprio documento de origem já classifica
este ponto como baixa prioridade e "não descartado, mas sem motivo para
priorizar" — e a persona reforça isso: como a ordem real dos fatos no app é
manual, não pela data (que às vezes nem é preenchida), qualquer visão
temporal (heatmap ou calendário) arrisca mostrar uma sequência que não é a
verdadeira, o que vai contra a prioridade máxima dele de precisão. Se algum
dia for necessário localizar por data, a Alternativa B é a rota mais barata
e óbvia (componente nativo `DatePickerDialog` do Android/Compose, sem
nenhuma lógica de agregação) — mas não há motivo para construir isso agora.

---

## 4. Ordenação da lista de carteiras por proximidade de prazo/meta

### Alternativa A — Menu de ordenação opcional, sem mudar o padrão (minimalismo extremo)
Um ícone de ordenação no topo da lista de carteiras abre um menu simples com
opções: "Padrão", "Prazo mais próximo", "Mais perto da meta". A ordem padrão
da lista não muda sozinha — o usuário escolhe quando quiser outra visão, e
a escolha não é permanente por padrão (reseta para "Padrão" ao reabrir o
app, evitando surpresa).

### Alternativa B — Barra de progresso com marcador de "onde deveria estar" (referência externa: Monzo Pots)
Cada card de carteira na lista ganha uma barra de progresso (valor atual
sobre meta) com uma marca vertical indicando onde o progresso deveria estar
hoje para cumprir o prazo. Isso resolve o problema dos dois critérios não
coincidirem (HMW do ponto 4): em vez de escolher entre ordenar por prazo OU
por valor, a distância entre "onde estou" e "onde deveria estar" (a marca)
se torna um único critério combinado de ordenação — carteiras mais atrasadas
em relação ao próprio prazo sobem na lista.

### Alternativa C — Fixar/favoritar carteira manualmente, por cima de qualquer ordenação automática (minha persona)
Como ele valoriza controle total e não gosta de estrutura rígida imposta
("não quero perder a liberdade"), cada carteira tem uma opção de fixar no
topo (ícone de pin, acessível por toque longo → menu de contexto, mesmo
padrão já adotado em outras listas do app). Carteiras fixadas ficam sempre
no topo, acima de qualquer ordenação algorítmica (prazo, meta ou padrão)
aplicada ao restante da lista — ele decide manualmente o que quer ver
primeiro, sem depender só do sistema calcular isso por ele.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Menu de ordenação opcional | 5 | 5 |
| B — Barra de progresso com marcador de prazo | 3 | 2 |
| C — Fixar/favoritar carteira | 4 | 3 |

**Recomendação: combinar A + C.** O menu de ordenação (A) é o componente
mais barato do grupo (`DropdownMenu` padrão do Material3) e cobre o caso
comum sem exigir nada novo do usuário. Fixar carteiras (C) reaproveita um
padrão de interação que o app já usa em outras listas (toque longo → menu de
contexto), e atende diretamente à recusa da persona a estrutura rígida
imposta — ele decide manualmente o que prioriza, sem depender de um cálculo
automático. A Alternativa B fica de fora por ora: a marca "onde eu deveria
estar" no Monzo funciona bem numa barra de progresso simples (uma meta), mas
aqui ela precisaria comunicar dois critérios ao mesmo tempo (prazo e valor)
numa única marca, o que não é óbvio de entender sem explicação — e ainda
exige desenhar esse marcador customizado sobre `Canvas`, sem componente
nativo equivalente.

---

## 5. Lista do que pode ser excluído dentro de um período

### Alternativa A — Exclusão simples com desfazer temporário (referência externa: padrão "Undo" do Gmail/Android)
Itens sem vínculo com outros registros (ex.: um gasto isolado, sem
composição de fatura ou aporte vinculado) podem ser excluídos direto pelo
gesto já existente (swipe ou toque longo → excluir), sem diálogo de
confirmação. Ao excluir, aparece uma snackbar no rodapé por alguns segundos
com "Item excluído — Desfazer". Se o usuário não tocar em "Desfazer", a
exclusão é confirmada silenciosamente. Baixa fricção para o caso de baixo
risco, conforme já sinalizado no documento de origem.

### Alternativa B — Confirmação explicativa para itens com vínculo (persona: medo de perda de rastreabilidade)
Itens que têm vínculo com outro dado (um aporte que alimentou uma composição
de fatura, por exemplo) não têm o atalho de swipe/gesto rápido disponível —
só podem ser excluídos abrindo o detalhe do item e usando um botão explícito
"Excluir". Antes de confirmar, um diálogo explica em linguagem direta a
consequência real (ex.: "Isso vai remover R$X vinculado à fatura de Março.
O valor da fatura será recalculado."), porque o medo dele não é o gasto em
si, é perder a capacidade de provar de onde veio ou para onde foi um valor.

### Alternativa C — Lixeira temporária, sem julgamento de risco por item (minimalismo extremo)
Em vez de classificar cada tipo de exclusão como simples ou arriscada
(trabalho de design que o próprio documento de origem registra como "ainda
sem análise própria"), toda exclusão — de qualquer item — vai primeiro para
uma tela de "Lixeira" e só é apagada de fato depois de um prazo (ex.: 30
dias) ou remoção manual explícita nessa tela. É uma regra única, sem
exceção por tipo de item, que evita ter que decidir agora a lista exata do
que é ou não seguro excluir.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Exclusão simples com desfazer (snackbar) | 5 | 5 |
| B — Confirmação explicativa para itens com vínculo | 4 | 4 |
| C — Lixeira temporária universal | 3 | 2 |

**Recomendação: combinar A + B.** Ambas usam componentes padrão do Material3
(`Snackbar` com `SnackbarAction` para o desfazer; `AlertDialog` para a
confirmação explicativa) e, juntas, cobrem exatamente a distinção que a
persona já faz naturalmente entre gasto simples e caso que exige atenção
(ela nunca trata todo dado com o mesmo peso de risco). A Lixeira (C) tem o
maior esforço do grupo — exige uma tela nova, lógica de expiração agendada
(provavelmente um `WorkManager` para limpeza automática) — para resolver um
problema que a combinação A+B já resolve com componentes prontos; fica como
alternativa a reconsiderar só se, na prática, a exclusão direta gerar
arrependimento frequente meses depois do "desfazer" já ter expirado.

---

## 6. Tema e paleta de cores

### Alternativa A — Semântica de cor fixa: vermelho/verde só para positivo/negativo (referência externa: convenção de contraste em apps financeiros)
As cores vermelho e verde ficam reservadas exclusivamente para indicar
valores negativos e positivos (saldo, variação) — nunca reaproveitadas para
diferenciar categorias de gasto. Categorias usam uma paleta separada de
cores neutras/frias, sempre acompanhadas de um ícone (a cor nunca é o único
sinal), o que também ajuda quem tem dificuldade de percepção de cor a não
confundir "categoria X" com "gasto negativo".

### Alternativa B — Paleta reduzida a dois acentos, resto em escala de cinza (minimalismo extremo)
Só duas cores de acento no app inteiro: uma para positivo/ação principal
(o verde já sugerido, `#3CBA59`), outra para negativo/alerta. Todo o resto
da interface (fundo, botões secundários, navegação) fica em tons de cinza.
Categorias de gasto se diferenciam por ícone/pictograma, não por cor
própria — reduz drasticamente o número de cores que precisam ser
memorizadas ou distinguidas a cada leitura da tela.

### Alternativa C — Tema com identidade fixa e accent selecionável entre poucos presets (minha persona)
Como o app é declaradamente pessoal ("é realmente pessoal") e ele já
demonstrou orgulho de identidade em projetos próprios, a estrutura de tema
(claro/escuro, contraste, hierarquia de texto) fica fixa e já validada por
acessibilidade, mas a cor de destaque (accent) pode ser escolhida entre 3-4
presets curados (ex.: verde, roxo, azul) numa tela de configuração simples —
não é personalização irrestrita ("feature bonita" que ele mesmo desvalorizaria
como polimento sem função), é uma escolha pequena e contida que ainda dá
sensação de identidade própria sobre o app.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Semântica fixa vermelho/verde | 4 | 4 |
| B — Paleta reduzida a dois acentos + cinza | 5 | 5 |
| C — Tema fixo com accent selecionável | 4 | 3 |

**Recomendação: combinar A + B como base do MVP.** Nenhuma das duas exige
nada além de definir tokens de cor no `ColorScheme` do Material3 — é
configuração de tema, não componente customizado — e as duas se
complementam sem conflito: B garante que sobra pouca cor no app para
competir com o significado fixo de vermelho/verde que A reserva para
positivo/negativo. A Alternativa C (accent selecionável) é a que exige mais
trabalho (tela de configuração, estado persistido de preferência) para um
ganho que é mais sobre gosto pessoal do que sobre uso diário — fica como
melhoria válida para depois do MVP, não como parte da base do tema.

---

## 7. Diferenciação visual entre faturas de cartões diferentes

### Alternativa A — Tag de cor por cartão + ícone genérico (referência externa: color-coding de contas em apps de finanças)
Cada cartão cadastrado recebe uma cor própria, escolhida pelo usuário (de
uma paleta fixa curta) no momento do cadastro. Na lista do período, o item
de fatura mostra o ícone genérico de cartão de crédito acompanhado de uma
faixa colorida fina na borda esquerda do item — a cor identifica de qual
cartão é a fatura, sem precisar de um segundo ícone competindo por espaço.

### Alternativa B — Texto em vez de ícone extra (minimalismo extremo)
Nenhum elemento visual novo: o subtítulo do item já existente passa a
incluir o nome/apelido do cartão diretamente como texto (ex.: "Fatura •
Nubank" ou "Fatura • Inter ••1234"). Resolve o problema sem adicionar
nenhuma cor, ícone ou componente — só reaproveita o espaço de texto
secundário que a lista já tem.

### Alternativa C — Ícone com a cor de marca do banco (referência externa: reconhecimento visual de bandeiras/bancos)
Em vez de uma cor arbitrária escolhida na hora do cadastro, o ícone de
fatura usa a cor oficial associada ao banco do cartão (roxo para Nubank,
laranja para Inter, etc.), aproveitando o reconhecimento visual que o
usuário já tem desses bancos no dia a dia — sem precisar memorizar uma cor
nova inventada só para o app.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Tag de cor por cartão (escolhida pelo usuário) | 4 | 4 |
| B — Texto com nome do cartão no subtítulo | 5 | 5 |
| C — Ícone com cor de marca do banco | 4 | 2 |

**Recomendação: Alternativa B primeiro, evoluir para A depois.** B resolve o
problema imediatamente sem adicionar nenhum componente visual novo — só
reaproveita o texto secundário que a lista já tem (`Text` com o nome do
cartão) — e tem clareza total, já que ler o nome do banco elimina qualquer
ambiguidade. A Alternativa C parece atraente por reconhecimento de marca,
mas na prática exige manter uma tabela de mapeamento banco→cor codificada
manualmente (e ela quebra ou fica incompleta a cada cartão de banco novo/
menos conhecido que ele cadastrar) — esforço maior sem ganho de clareza
sobre B. Se, depois de usar B, sentir falta de um reforço visual mais rápido
que ler texto, aí vale evoluir para A (cor escolhida por ele mesmo no
cadastro, mais barato e flexível que C).

---

## 8. Prioridade do ícone de compra no crédito: pagamento vs. destino

### Alternativa A — Destino como ícone principal, crédito como selo sobreposto (referência externa: badge de status sobreposto, padrão comum em apps de mensagens/notificação)
O ícone principal do item continua sendo o da categoria/loja (destino da
compra) — é o que domina a leitura rápida da lista. Um pequeno selo
(círculo com o símbolo de cartão) fica sobreposto no canto inferior direito
desse ícone só quando a compra foi no crédito. O selo não compete em
tamanho com o ícone principal, só complementa.

### Alternativa B — Cor de fundo do ícone como sinalizador de método de pagamento (minimalismo extremo)
Nenhum ícone adicional: o mesmo ícone de categoria/destino que já existe
recebe um fundo levemente tingido (ex.: um tom de roxo suave) quando a
compra foi no crédito, e fundo neutro nos demais casos. A informação de
"foi no crédito" fica disponível como reforço visual sutil, sem exigir
nenhum elemento novo na tela, e o detalhe completo (nome do cartão, etc.)
continua disponível ao abrir o item.

### Alternativa C — Pagamento como ícone principal só quando ele mesmo classifica isso como o dado relevante (minha persona)
Dado que a motivação central dele é rastrear "de onde vem o dinheiro" com
controle absoluto, para os casos em que o método de pagamento é o dado mais
relevante para a decisão financeira (ex.: parcelamentos longos, compras que
disparam limite de cartão), o ícone de cartão assume a posição principal, e
o destino/categoria vira o texto secundário. Isso muda o critério de
"sempre destino primeiro" para "prioridade conforme o tipo de gasto", o que
é mais fiel a como ele mesmo pensa (relevância do dado, não convenção fixa
de layout) — ainda que exija uma regra a mais para decidir qual dos dois
"ganha" em cada situação.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Selo de crédito sobreposto ao ícone principal | 4 | 4 |
| B — Cor de fundo tingida no ícone | 3 | 5 |
| C — Ícone principal condicional (pagamento vs. destino) | 2 | 3 |

**Recomendação: Alternativa A.** O Material3 já tem o componente `Badge`
pronto exatamente para esse padrão (selo pequeno sobreposto a um ícone),
então o esforço é baixo mesmo não sendo o mínimo do grupo, e a leitura é
consistente item a item — o selo sempre significa a mesma coisa. A
Alternativa B tem o menor esforço, mas um tingimento sutil de fundo é fácil
de não perceber numa lista rolando rápido, o que compromete a clareza. A
Alternativa C é a que fica pior nas duas dimensões: variar qual ícone é
"principal" conforme o tipo de gasto cria uma regra que muda o significado
visual de tela para tela — indo direto contra a busca da persona por
controle e precisão previsíveis, já que ele precisaria lembrar qual regra
se aplica a cada caso antes mesmo de olhar o ícone.

---

## 9. Tela dedicada por classe de ativo vs. tudo no gráfico de composição

### Alternativa A — Expansão inline por segmento, sem sair da tela da carteira (referência externa: drill-down no mesmo nível, padrão YNAB adaptado)
O gráfico de composição continua sendo a visão padrão. Tocar num segmento
(ex.: "Exterior") expande, dentro da própria tela, um bottom sheet ou painel
inline com a lista de ativos daquela classe — sem navegar para uma tela
nova. Fechar o painel volta exatamente para o gráfico, sem perder a visão do
todo (responde diretamente ao HMW "sem perder a visão do todo da carteira").

### Alternativa B — Tela dedicada só quando o volume de ativos justificar (minimalismo condicional)
Em vez de escolher fixamente entre "sempre gráfico" ou "sempre tela
dedicada", a decisão é automática: classes de ativo com poucos itens
continuam representadas só dentro do gráfico de composição; quando uma
classe específica acumula muitos ativos (passa de um limite, ex.: 8 itens),
o app oferece um atalho "Ver todos" que aí sim abre uma tela dedicada só
para aquela classe.

### Alternativa C — Gráfico como padrão, tela dedicada como aprofundamento opcional (minha persona)
Como ele gosta de granularidade mas não quer telas extras competindo por
atenção sem necessidade, o gráfico de composição é sempre a primeira coisa
que aparece (visão consolidada). Dentro de cada segmento expandido (como na
Alternativa A), um botão extra "Ver detalhe completo" leva a uma tela cheia
dedicada só se ele explicitamente decidir se aprofundar — a tela dedicada
existe, mas nunca é o caminho padrão, só a opção para quando ele quiser
mesmo entrar a fundo numa classe específica.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Expansão inline por segmento (bottom sheet) | 4 | 3 |
| B — Tela dedicada condicional por volume de ativos | 3 | 2 |
| C — Gráfico padrão + tela dedicada opcional | 4 | 3 |

**Recomendação: Alternativa A.** Se comportar sempre da mesma forma (tocar
um segmento sempre expande um painel, nunca uma tela cheia por padrão) é
mais fácil de prever do que a Alternativa B, cujo comportamento muda
silenciosamente dependendo de quantos ativos existem — ele teria que
descobrir na prática por que "às vezes abre um painel, às vezes abre uma
tela" sem nenhuma pista visual disso. A é também mais barata que C, que
adiciona uma segunda camada de navegação (botão extra "ver detalhe completo"
dentro do próprio painel) sem ganho de clareza proporcional. Reaproveitar o
mesmo componente de bottom sheet já recomendado no ponto 2 também reduz o
número de padrões de interação diferentes no app como um todo.

---

## 10. Tela inicial ao abrir o app

### Alternativa A — Abrir direto no Período atual, sem dashboard no MVP (minimalismo extremo)
Nenhuma decisão a ser tomada pelo usuário: o app sempre abre na tela do
Período atual, que já é a "ação imediata" mais frequente. Uma visão de
patrimônio consolidado fica de fora do MVP inteiramente, sem necessidade de
resolver agora onde ela mora na navegação.

### Alternativa B — Período atual + faixa fixa de patrimônio consolidado no topo (minha persona)
A tela inicial continua sendo o Período atual (ação imediata, sem forçar
escolha), mas uma faixa fina e fixa no topo da tela mostra um único número:
o patrimônio total consolidado (soma de todas as carteiras/contas). Sem
gráfico, sem detalhamento — só o número que ele mesmo descreveu como algo
que vai ser "chocante" de ver de verdade pela primeira vez, e que ele já
identificou como motivador para manter o hábito de investir. Resolve o HMW
de "mostrar as duas visões sem forçar escolha única" sem o custo de
construir uma tela de dashboard completa agora.

### Alternativa C — Duas abas de navegação, ação vs. reflexão (referência externa: separação Home/Reflect da YNAB) — registrado para depois do MVP
Navegação inferior com duas abas: "Período" (aberta por padrão, foco em
ação — o que precisa ser feito agora) e "Patrimônio" (foco em reflexão —
consolidado, evolução histórica, gráficos). É a separação mais robusta e a
mais alinhada ao padrão de mercado já validado pela YNAB, mas exige mais
estrutura (uma tela de patrimônio completa) do que o MVP comporta — fica
registrada como direção futura, não como decisão para agora.

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Abrir direto no Período atual | 5 | 5 |
| B — Período atual + faixa de patrimônio consolidado | 4 | 4 |
| C — Duas abas Home/Reflect | 4 | 2 |

**Recomendação: Alternativa B.** É praticamente tão simples de implementar
quanto A (só soma agregada de todas as carteiras/contas exibida num `Text`
no topo, sem gráfico nem tela nova), mas entrega algo que a persona já
identificou como motivador real de hábito — ver o patrimônio consolidado,
algo que hoje ele só estima somando apps de banco de cabeça. A Alternativa C
é a mais robusta e alinhada ao padrão de mercado (YNAB), mas tem o menor
esforço-benefício agora: exige uma tela de patrimônio completa que o próprio
levantamento original já marcou como "provavelmente fora do MVP" — vale
revisitar quando o app crescer além do escopo atual, não como parte da
abertura do app hoje.

---

## 11. Comportamento de gráficos em tela pequena

### Alternativa A — Gráfico sempre abaixo do formulário, com scroll (minimalismo extremo)
Sem nenhuma navegação nova: o gráfico correspondente àquele formulário/tela
(ex.: composição da carteira) fica sempre renderizado abaixo dos campos,
dentro da mesma tela, e o usuário rola para vê-lo. É a implementação mais
simples possível e não introduz nenhum componente ou fluxo adicional.

### Alternativa B — Mini-gráfico inline + tela cheia sob demanda (referência externa: sparkline seguido de drill-down)
Uma versão reduzida do gráfico (sparkline simples, sem legendas ou eixos
detalhados) fica sempre visível inline, próxima ao topo do formulário —
informação essencial disponível sem nenhum toque. Tocar nesse mini-gráfico
abre uma tela cheia dedicada, com o gráfico completo, legendas e
interatividade (zoom, tooltip por ponto). Resolve o HMW de "mostrar o
essencial antes de decidir expandir".

### Alternativa C — Bottom sheet expansível em vez de navegação para tela nova (minha persona)
Como ele evita qualquer fricção que o disperse do que está fazendo ("não
quero dispersar"), em vez de navegar para uma tela nova (o que tira ele do
contexto do formulário), o gráfico expande como um bottom sheet que se
arrasta de baixo para cima sobre a tela atual. Arrastar de volta para baixo
fecha o gráfico e ele está exatamente onde estava no formulário, sem
histórico de navegação para desfazer (sem precisar apertar "voltar").

### Avaliação

| Alternativa | Clareza de uso | Esforço de implementação |
|---|---|---|
| A — Gráfico sempre abaixo, com scroll | 5 | 5 |
| B — Mini-gráfico inline + tela cheia sob demanda | 4 | 3 |
| C — Bottom sheet expansível | 4 | 3 |

**Recomendação: Alternativa C.** Empata com B nas duas notas, mas ganha na
consistência: os pontos 2 e 9 já recomendam `ModalBottomSheet` para
drill-down sem perder contexto, então reaproveitar o mesmo padrão aqui
reduz o número de comportamentos diferentes que ele precisa aprender no app
inteiro — arrastar de baixo para cima passa a significar sempre a mesma
coisa ("ver mais detalhe sem navegar"). A Alternativa A continua válida como
fallback mais simples para gráficos pequenos que já cabem bem na tela sem
precisar de expansão nenhuma.

---

## Resumo das recomendações

| Ponto | Recomendação |
|---|---|
| 1. Ordem dos três valores | A — Peso visual decrescente |
| 2. Breakdown de totais | B + C — Drill-down multi-entrada + barra de proporção Meu/Terceiro |
| 3. Navegação por calendário | C — Não implementar agora |
| 4. Ordenação de carteiras | A + C — Menu de ordenação + fixar carteira |
| 5. Exclusão de itens do período | A + B — Desfazer para itens simples + confirmação explicativa para itens vinculados |
| 6. Tema e paleta de cores | A + B — Semântica fixa vermelho/verde + paleta reduzida a dois acentos |
| 7. Diferenciação de faturas por cartão | B — Texto com nome do cartão (evoluir para A se precisar de reforço visual) |
| 8. Ícone de compra no crédito | A — Selo sobreposto (`Badge` do Material3) |
| 9. Classe de ativo: gráfico vs. tela dedicada | A — Expansão inline por segmento (bottom sheet) |
| 10. Tela inicial do app | B — Período atual + faixa de patrimônio consolidado |
| 11. Gráficos em tela pequena | C — Bottom sheet expansível |
