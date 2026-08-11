# Panorama de soluções existentes — pesquisa de UI

> Pesquisa de referências de UI/funcionalidade em apps de finanças pessoais
> já estabelecidos, feita para alimentar decisões de design ainda abertas no
> projeto (ver `docs/analise-requisitos.md`, seções 3, 4, 7, 8, 9 e 10). Não é
> análise competitiva de negócio — é inventário de padrões de interface já
> testados por outros produtos, com indicação explícita de onde nenhum deles
> cobre as particularidades deste app.

Apps consultados: **YNAB**, **GnuCash**, **Mobills**, **Monzo**, **Revolut**,
**Nubank** (caixinhas), **Splitwise**. Organizze e Money Manager foram
pesquisados, mas a documentação pública disponível é rasa (a maior parte do
material encontrado é marketing, não documentação de produto) — os pontos
relevantes que apareceram estão citados onde cabem.

---

## 1. Fluxo de lançamentos com saldo projetado

O equivalente direto da "cascata de subtração" em apps estabelecidos é o
**registro de conta com saldo corrente por transação** (account register /
ledger), um padrão que vem da contabilidade tradicional, não é invenção de
app de finanças pessoais.

- **YNAB** — o *account register* tem uma ordem padrão (geralmente por data)
  e cada linha mostra um saldo corrente (*running balance*) calculado a
  partir da transação anterior. O saldo é sempre uma coluna fixa na lista,
  visível linha a linha, sem precisar abrir detalhe ([YNAB — The Running
  Balance](https://support.ynab.com/en_us/running-balance-an-overview-Syk1ZgdRc)).
- **GnuCash** — o *Basic Ledger* (estilo padrão do registro de conta) mostra
  uma linha por transação com saldo corrente ao lado, e no rodapé da janela
  exibe o saldo total da conta e o saldo "conciliado" separadamente
  ([GnuCash — The Account Register](https://cvs.gnucash.org/docs/ru/gnucash-guide/txns-register-oview.html)).
  Isso é conceitualmente parecido com sua distinção entre "saldo em cascata
  por item" e "saldo atual (realizado)" — GnuCash separa saldo do registro
  (tudo lançado) do saldo conciliado (confirmado com o banco).

**Aplicável ao seu caso:** sim, é essencialmente o mesmo padrão que você já
desenhou. A diferença real está na ordenação: nesses apps a ordem é
cronológica **por data efetiva da transação** (a lista é passiva, reflete o
extrato); no seu app a ordem é **manual e representa a sequência real dos
fatos**, independente da data (às vezes sem data preenchida). Nenhum dos
apps pesquisados usa reordenação manual como fonte de verdade da sequência —
eles assumem que a data já é suficiente. Isso não é uma lacuna dos
concorrentes por acaso: eles trabalham com extrato bancário importado (data
sempre confiável), enquanto seu app modela planejamento manual (data nem
sempre conhecida). Sua decisão de não vincular reordenação a edição de data
é uma resposta correta a um problema que eles não têm.

Nenhum concorrente pesquisado usava **elemento expansível por item** para
mostrar o saldo em cascata sem abrir o modal completo — eles simplesmente
sempre mostram a coluna de saldo. Vale reconsiderar se seu app também deveria
sempre mostrar (mesmo que como texto secundário pequeno), em vez de esconder
atrás de expansão — o padrão da indústria sugere que essa informação é
consultada com frequência suficiente para não valer a fricção extra de um
toque.

---

## 2. Painéis de resumo / dashboard por período

- **Mobills** — a tela inicial é um **dashboard customizável**: o usuário
  escolhe quais cartões/módulos aparecem na tela principal (saldo de contas,
  gastos do mês, cartões, metas), com os relatórios completos e gráficos
  aprofundados vivendo em telas próprias, acessadas por navegação separada
  ([Mobills — App Store](https://apps.apple.com/br/app/mobills-controle-de-gastos/id921838244)).
  Padrão: resumo customizável na tela de entrada, análise profunda escondida
  atrás de abas dedicadas.
- **YNAB** — divide explicitamente em duas abas com propósitos diferentes:
  a aba **Home**, focada em ação (dinheiro a alocar, transações a aprovar,
  metas com progresso) ([YNAB — At Home in YNAB](https://support.ynab.com/en_us/spotlight-BkdHBZUokg)),
  e a aba **Reflect**, com análises históricas mais pesadas — gráfico de
  receita vs. gasto dos últimos 6 meses, *spending trends*, patrimônio líquido
  mês a mês, e o indicador "idade do dinheiro" (quantos dias em média o
  dinheiro gasto ficou disponível antes de ser usado) ([YNAB — Reflect](https://support.ynab.com/en_us/reflect-in-ynab-B1GJsrWkj),
  [YNAB — Spending Trends](https://support.ynab.com/en_us/spending-trends-H1inlhzAc),
  [YNAB — Net Worth](https://support.ynab.com/en_us/net-worth-BkwQO5WA5)).
  A separação de propósito (ação imediata vs. reflexão histórica) é o ponto
  mais transferível daqui: seu quadro de resumo do período já cumpre o papel
  de "ação imediata" (totais, drill-down); talvez análises comparativas entre
  períodos (que não estão no MVP, mas podem vir depois) sigam o padrão de
  ficar numa aba de "reflexão" separada, sem competir por espaço com o
  quadro operacional do período atual.

**Aplicável ao seu caso:** parcialmente. A distinção entre visão
simplificada (resumo) e detalhada (fluxo) que você já definiu na seção 4
espelha exatamente a separação Home/Reflect da YNAB, mas dentro do mesmo
nível de navegação (drill-down), não em abas — decisão razoável dado que seu
app opera por período, não por conta corrente contínua. O conceito de
dashboard customizável do Mobills é overkill para um app de uso pessoal com
um único usuário e fluxo bem definido — não há necessidade de deixar o
usuário escolher o que aparece, ao contrário de um produto multi-perfil de
mercado.

---

## 3. Objetivos de economia / caixinhas

- **YNAB (Targets/Goals)** — metas vivem em **categorias do orçamento**, não
  em "potes" de dinheiro separados. Uma categoria com meta mostra uma barra
  de progresso; quando há um valor de meses anteriores rolando junto com uma
  nova parcela do mês, a barra é dividida em duas seções visuais
  ([YNAB — Visual Progress Bars](https://support.ynab.com/en_us/progress-bars-a-guide-SkDEhot09)).
  Não há conceito de múltiplos "aportes" rastreados individualmente dentro
  da meta — é um valor acumulado único por categoria.
- **Monzo (Pots)** — cada Pot tem uma barra de progresso que enche conforme
  o dinheiro entra; se há uma data-alvo, aparece uma **linha vertical** na
  barra indicando onde o progresso deveria estar para ficar no prazo (ex:
  "você deveria ter economizado X até aqui") ([Monzo — Pot Goals](https://monzo.com/us/blog/monzo-us-blog/pot-goals)).
  Isso é um padrão de UI interessante e diretamente aplicável ao seu campo
  de prazo + quantia desejada (seção 7): a barra de progresso com marcador
  de "onde eu deveria estar" é mais informativa do que só "R$X de R$Y".
- **Revolut (Vaults / Group Vaults)** — é o concorrente que chega mais perto
  de "múltiplos aportes dentro do mesmo objetivo": Vaults aceitam depósitos
  recorrentes, avulsos, ou por arredondamento de troco, e Group Vaults
  permitem que várias pessoas contribuam para o mesmo objetivo ao longo do
  tempo ([Revolut — Group Vaults](https://www.revolut.com/blog/post/hit-savings-goals-faster-with-group-vaults/)).
  Ainda assim, o Vault trata isso como **saldo agregado único** — não expõe
  cada depósito como um registro individual navegável com data, valor e
  origem própria; é histórico de transações genérico, não uma lista de
  "aportes" com atributos financeiros (instituição, produto, rentabilidade,
  vencimento) como você modelou.
- **Nubank (Caixinhas)** — mesma limitação: caixinha é saldo único que rende,
  resgates podem ser parciais, mas não há conceito de aporte individual
  rastreável com vencimento e produto próprios — é tudo fungível dentro da
  caixinha ([comunidade Nubank — Caixinha parcial](https://comunidade.nubank.com.br/t/caixinha-parcial/621636)).

**Onde nenhum concorrente cobre (esperado):** a granularidade de **aporte
individual com instituição, tipo de produto, rentabilidade e vencimento
próprios**, dentro de uma mesma carteira/caixinha, com suporte a
reinvestimento vinculado ao aporte de origem, não existe em nenhum app
pesquisado. Isso é porque nenhum desses produtos modela "carteira" como
container de múltiplos produtos financeiros distintos (CDB, LCI, Tesouro,
caixinha) simultaneamente — cada um assume que o produto financeiro já é a
unidade de conta. Seu modelo é mais próximo de uma planilha de controle de
renda fixa pessoal do que de um "pote de dinheiro" de fintech. É esperado
que não haja equivalente pronto — é a particularidade mais forte do seu app
nesta área. A barra de progresso com marcador de prazo (Monzo) é o único
elemento de UI diretamente reaproveitável aqui; o resto (lista de aportes,
reinvestimento) precisa ser desenhado do zero.

---

## 4. Composição de fatura de cartão

- **YNAB** — não modela "composição de fatura" como você definiu. Em vez
  disso, usa um mecanismo de orçamento: ao gastar no crédito, o valor sai
  automaticamente da categoria de orçamento correspondente (ex: "Mercado") e
  é redirecionado para uma categoria especial **Credit Card Payment**; o
  saldo dessa categoria é o valor "disponível para pagar a fatura" a
  qualquer momento, mesmo antes do fechamento ([YNAB — Handling Credit
  Cards](https://support.ynab.com/en_us/handling-credit-cards-overview-ry7cNub1s)).
  Isso resolve um problema parecido ao seu ("de onde vem o dinheiro que paga
  a fatura") mas por um caminho totalmente diferente: realocação de saldo
  entre categorias de orçamento, não rastreamento de resgates de
  caixinhas/carteiras específicas. Não há conceito de "fonte" vinculada a um
  aporte determinado, nem de complemento automático entre múltiplas origens.
- **Splitwise** — não é um app de finanças pessoais no sentido tradicional,
  mas resolve um problema estrutural parecido ao de "gasto composto por
  múltiplas fontes": um gasto tem um valor total e é dividido entre
  pagadores, com o saldo residual (quem deve quanto a quem) calculado
  automaticamente ([Splitwise — How do I use it](https://kb.splitwise.com/getting-started/how-do-i-use-splitwise)).
  O paralelo útil aqui não é a fatura em si, mas a "Explicação de Gasto" da
  seção 10 — Splitwise reforça que "valor total dividido em fontes, com
  complemento calculado automaticamente" é um padrão de UI validado, mesmo
  que em outro domínio (divisão social, não fatura de cartão).

**Onde nenhum concorrente cobre (esperado):** a composição de fatura por
**resgates de caixinhas específicas + complemento de conta corrente +
parte de terceiro**, com granularidade por aporte individual e suporte a
créditos/débitos mistos por estorno de parcela, não existe em nenhum app
pesquisado. Isso é esperado — nenhum concorrente modela "carteira de
terceiro administrada pelo usuário" nem "caixinha vinculada a uma compra
específica para reserva de valor", que são os dois pilares que tornam sua
composição de fatura mais rica que qualquer coisa no mercado. O único padrão
de UI reaproveitável é a alternância **detalhado vs. agrupado** (que você já
definiu na seção 10) — isso é conceitualmente parecido com alternar entre
lista de transações individuais e resumo por categoria, um padrão comum em
qualquer relatório financeiro, mas nenhum concorrente aplica especificamente
a "origem de resgate para pagar fatura".

---

## 5. Padrões de gesto em listas de lançamentos

- **Swipe actions (iOS)** — o padrão mais adotado do mercado para
  ações rápidas por item (editar, excluir, arquivar) é revelar botões ao
  arrastar o item horizontalmente, popularizado pelo Mail da Apple. É
  considerado o gesto contextual de maior adoção em apps de lista, mesmo
  que a maioria dos gestos touch tenha baixo uso geral ([NNGroup — Using
  Swipe to Trigger Contextual Actions](https://www.nngroup.com/articles/contextual-swipe/)).
- **Long-press para menu de contexto (Android)** — padrão nativo do Android:
  pressionar e segurar um item de lista abre um menu contextual (editar,
  excluir, etc.), reservando o toque simples para seleção/abertura
  ([Android Design Patterns — Selection](https://stuff.mit.edu/afs/sipb/project/android/docs/design/patterns/selection.html)).
  Isso é exatamente o padrão que você já adotou na seção 3 (pressionar e
  segurar → menu de contexto), então sua escolha está alinhada com a
  convenção nativa da plataforma, não é uma invenção arriscada.
- **Alça dedicada de arraste** — o GitHub, ao reordenar itens de lista de
  tarefas, usa uma alça específica (ícone de seis pontos) que só aparece ao
  passar o mouse/tocar perto da borda esquerda do item, em vez de permitir
  arrastar de qualquer ponto ([GitHub Docs — About task lists](https://docs.github.com/en/enterprise-cloud@latest/get-started/writing-on-github/working-with-advanced-formatting/about-task-lists)).
  Mesmo padrão que você já adotou para resolver o conflito de gestos entre
  reordenar e seleção múltipla.
- **GnuCash (Scheduled Transactions)** — o mecanismo de "sugestão de gasto
  recorrente pré-carregado" tem paralelo direto no GnuCash: transações
  agendadas aparecem como lembrete/sugestão antes de serem efetivamente
  criadas no registro, e o usuário pode confirmar, editar ou marcar como
  "ignorada" (skip) sem afetar a recorrência futura ([GnuCash — Since Last
  Run Assistant](https://www.gnucash.org/docs/v4/C/gnucash-help/trans-sched-slr.html)).
  Confirma que seu modelo de recorrentes materializados como cópia editável
  no início do período é um padrão já validado, não uma solução exótica.

**Aplicável ao seu caso:** os três gestos que você já desenhou (toque no
checkbox, toque simples para detalhe, arraste pela alça, pressionar e segurar
para menu de contexto) batem com convenções estabelecidas — não há
necessidade de reinventar nada aqui. **Onde nenhum concorrente cobre
(esperado):** a **cascata de checkbox** (marcar N marca tudo antes; desmarcar
N desmarca tudo depois; invariante de prefixo contíguo mantido após
reordenação) não tem equivalente em nenhum padrão de lista pesquisado —
listas de tarefas com checkbox em cascata geralmente lidam com hierarquia
pai/filho (marcar um grupo marca os itens dele), não com sequência
cronológica linear como a sua. É esperado que essa regra de negócio
específica não tenha precedente de UI pronto para copiar — a lógica em si
(seção 3 do seu documento) já está bem definida, o trabalho que falta é só
de implementação de interação, não de pesquisa de referência.

---

## Resumo: onde a pesquisa confirma lacuna esperada

| Particularidade sua | Encontrado em algum concorrente? |
|---|---|
| Caixinha com aportes individuais (instituição, produto, rentabilidade, vencimento, reinvestimento) | Não. Vaults/Pots tratam saldo como agregado único. |
| Carteira de terceiro administrada pelo usuário | Não. Nenhum concorrente modela custódia de dinheiro de terceiro dentro da própria carteira. |
| Composição de fatura por resgates de caixinha + complemento + parte de terceiro, granularidade por aporte | Não. YNAB resolve "de onde vem o dinheiro da fatura" por realocação de orçamento, não por rastreamento de fontes. |
| Cascata de checkbox baseada em ordem cronológica manual (prefixo contíguo) | Não. Padrão inexistente em listas de tarefas ou registros financeiros pesquisados. |

Nenhuma dessas ausências é motivo de preocupação — confirma que essas são as
áreas onde o design precisa ser original, enquanto o restante (registro com
saldo corrente, dashboard com drill-down, barra de progresso de meta, swipe/
long-press/alça de arraste, recorrência com sugestão editável) pode se apoiar
com confiança em padrões já testados pelo mercado.

## Fontes consultadas

- [YNAB — The Running Balance](https://support.ynab.com/en_us/running-balance-an-overview-Syk1ZgdRc)
- [GnuCash — The Account Register](https://cvs.gnucash.org/docs/ru/gnucash-guide/txns-register-oview.html)
- [Mobills — App Store listing](https://apps.apple.com/br/app/mobills-controle-de-gastos/id921838244)
- [YNAB — At Home in YNAB](https://support.ynab.com/en_us/spotlight-BkdHBZUokg)
- [YNAB — Reflect in YNAB](https://support.ynab.com/en_us/reflect-in-ynab-B1GJsrWkj)
- [YNAB — Spending Trends](https://support.ynab.com/en_us/spending-trends-H1inlhzAc)
- [YNAB — Reflect on Net Worth](https://support.ynab.com/en_us/net-worth-BkwQO5WA5)
- [YNAB — Visual Progress Bars](https://support.ynab.com/en_us/progress-bars-a-guide-SkDEhot09)
- [Monzo — Achieve your savings goals on time with Pots](https://monzo.com/us/blog/monzo-us-blog/pot-goals)
- [Revolut — Hit your savings goals faster with Group Vaults](https://www.revolut.com/blog/post/hit-savings-goals-faster-with-group-vaults/)
- [Comunidade Nubank — Caixinha parcial](https://comunidade.nubank.com.br/t/caixinha-parcial/621636)
- [YNAB — Handling Credit Cards](https://support.ynab.com/en_us/handling-credit-cards-overview-ry7cNub1s)
- [Splitwise — How do I use Splitwise?](https://kb.splitwise.com/getting-started/how-do-i-use-splitwise)
- [NNGroup — Using Swipe to Trigger Contextual Actions](https://www.nngroup.com/articles/contextual-swipe/)
- [Android Design Patterns — Selection](https://stuff.mit.edu/afs/sipb/project/android/docs/design/patterns/selection.html)
- [GitHub Docs — About task lists](https://docs.github.com/en/enterprise-cloud@latest/get-started/writing-on-github/working-with-advanced-formatting/about-task-lists)
- [GnuCash — Since Last Run Assistant](https://www.gnucash.org/docs/v4/C/gnucash-help/trans-sched-slr.html)
