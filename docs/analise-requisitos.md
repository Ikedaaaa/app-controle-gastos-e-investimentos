# Análise e Requisitos — App de Controle de Gastos e Investimentos

Baseado nas anotações mensais reais, nas ideias brutas documentadas e na
explicação da estrutura das anotações.

---

## Contexto e objetivo

App pessoal de uso exclusivo. O objetivo central do MVP é substituir
completamente o bloco de notas como ferramenta de controle financeiro mensal.

O bloco de notas resolve o problema de liberdade total, mas exige:
- Todos os cálculos feitos à mão
- Dados duplicados e copiados mês a mês
- Nenhuma visão analítica dos dados
- Risco constante de inconsistência

O app precisa replicar a lógica já consolidada nas anotações, automatizando
o trabalho manual, sem impor uma estrutura diferente da que o usuário já usa.

---

## 1. Estrutura de períodos de gasto (Meses e Quinzenas)

### O problema
O usuário recebe dois pagamentos por mês: salário no fim do mês e adiantamento
quinzenal na metade. Cada pagamento tem seus próprios gastos e alocações.

### Requisitos
- Cada mês pode ter um ou dois períodos de gasto:
  - **Modo quinzenal**: dois períodos — Adiantamento (1ª quinzena) e Salário (2ª quinzena)
  - **Modo mensal**: um único período por mês
- O modo deve ser configurável por mês (não globalmente fixo), para suportar
  meses atípicos e mudança futura de emprego
- Deve ser possível visualizar o mês consolidado, independente do modo
- A navegação entre meses deve ser fluida (estilo carrossel, meses mais antigos
  à esquerda)

### Observações de comportamento real
Uma conta recorrente pode ser paga fora do período esperado — por exemplo,
uma conta paga com o adiantamento em vez do salário. Quando o novo período é
criado, o item recorrente aparece como sugestão e o usuário simplesmente zera
ou remove. A anomalia que isso gera nos gráficos (um mês com valor duplo,
outro com zero) é aceitável — é um reflexo fiel da realidade.

Outro padrão: o período pode não ser montado com antecedência. Uma conta
vence, é paga com o saldo disponível no momento, e o período é formalizado
depois. O app deve permitir isso sem atrito.

### Decisão: período semanal não será implementado, mas a modelagem não deve impedi-lo
Considerado e descartado como funcionalidade — a probabilidade real de
precisar de granularidade semanal é baixa (salário/adiantamento semanal não
é um cenário plausível para o usuário). Não será implementado no MVP nem
está planejado para versões futuras.

Ainda assim, a modelagem de dados de Período deve ser genérica o suficiente
para não fechar essa porta sem esforço: um Período é um intervalo de datas
(`data_inicio`, `data_fim`) com um rótulo de `tipo` (mensal, quinzenal, e
futuramente semanal se necessário) e uma referência ao mês. A lógica de
cálculo do fluxo não deve depender de regras fixas como "quinzenal = dias
1-15 ou 16-30" — deve operar sobre o intervalo de datas do Período,
independente da duração. Modelado assim, suportar semanal no futuro seria
apenas criar Períodos com intervalos de 7 dias, sem exigir lógica nova.

O tipo do Período é independente entre períodos consecutivos — um mês pode
ser quinzenal e o seguinte mensal, sem quebrar a cadeia de saldo remanescente
(que depende apenas da sequência cronológica de períodos fechados, não do
tipo). O tipo é escolhido na criação do Período; alterá-lo depois que já
existem itens dentro não é suportado — nesse caso, o período deve ser
recriado do zero.

### Esboço de modelagem: Mês e Período
Estrutura considerada para a entidade que agrupa períodos:

- **Mês** — `ano`, `numero_mes`, `tipo` (mensal/quinzenal). Agrega um ou
  mais Períodos. Não requer uma tabela `Ano` separada — ano é apenas um
  campo em Mês; uma tabela dedicada só se justificaria se surgir necessidade
  de atributos próprios do ano (ex: meta anual, resumo anual fechado), o
  que pode ser introduzido depois sem grande impacto se necessário
- **Período** — `data_inicio`, `data_fim`, FK para o Mês. **Não tem campo
  `tipo` próprio** — o tipo é sempre inferido do Mês ao qual o período
  pertence. Duplicar `tipo` em Período reintroduziria exatamente o problema
  de duplicidade de dados que o app existe para eliminar: duas fontes de
  verdade para o mesmo fato, com risco de divergirem (ex: um mês marcado
  como "mensal" tendo um período marcado como "quinzenal" dentro dele)

**Ponto de atenção:** a FK de Período para Mês representa o **mês de
referência** (o mês cujo orçamento aquele período está compondo), não
necessariamente o mês calendário das datas. Um período recebido no fim de
julho pode compor os gastos de agosto (ver seção sobre salário recebido no
fim do mês cobrindo a primeira quinzena do mês seguinte). A FK deve apontar
para o mês de referência, não ser inferida da data.

### Enforcement de regras de negócio: schema vs. aplicação
Mesmo com `tipo` centralizado em Mês, nada nos constraints básicos do SQLite/
Room impede, por exemplo, um insert manual criando três períodos dentro de
um mês quinzenal, ou períodos com datas sobrepostas. Impedir isso 100% via
constraints de banco exigiria triggers ou lógica mais complexa, desproporcional
para um app local de uso pessoal. Decisão pragmática: o **schema** garante o
básico (tipo é um enum válido, FKs existem); a **camada de
aplicação/repositório** garante a regra de negócio (não permitir criar um
terceiro período num mês quinzenal, não permitir datas sobrepostas). Essa
divisão de responsabilidade é normal em sistemas relacionais e não representa
abandono de rigor — só reconhece que regra de negócio complexa vive melhor
no código da aplicação do que espalhada em constraints de banco.

### Criação de período: automática, sem perguntar, editável depois
Ao criar um novo Período dentro de um Mês, a criação é automática e
determinística, sem exigir escolha do usuário:
- **Mês mensal** — cria automaticamente com `data_inicio = dia 1`,
  `data_fim = último dia do mês` (calculado por calendário, considerando
  28/29/30/31 dias)
- **Mês quinzenal** — se não existe nenhum período ainda, cria o primeiro
  (dia 1 ao dia 15); se já existe o primeiro, cria o segundo (dia 16 ao
  último dia do mês). Não há ambiguidade que justifique perguntar ao
  usuário — a próxima criação sempre tem exatamente uma resposta correta
  dado o estado atual do mês

Perguntar ao usuário aqui seria fricção desnecessária, na contramão do
princípio de reduzir esforço manual que já orienta todo o app. Se o usuário
tiver um caso atípico (datas de pagamento fora do padrão), o período criado
automaticamente pode ser editado depois — já coberto pelo princípio geral
de flexibilidade (nada é travado).

Exercício de extensibilidade (não para implementar — ver decisão sobre
período semanal acima): se um mês fosse do tipo semanal, a mesma lógica de
preenchimento sequencial se aplicaria — cada novo período começa onde o
anterior parou, em janelas de 7 dias, até o fim do mês. A última janela do
mês pode ficar menor que 7 dias, já que meses não dividem por 7 igualmente
— consequência natural do calendário, não uma mudança de modelo. (Alternativa
não detalhada: alinhar semanas ao calendário real, domingo a sábado, ao
invés de blocos fixos de 7 dias a partir do dia 1 — irrelevante aprofundar
agora, já que a feature não será implementada.)

A lógica de calcular essas datas (calendário, últimos dias do mês, sequência
de janelas) é responsabilidade da camada de aplicação, não do modelo de
dados. O banco armazena apenas o intervalo de datas resultante.

**Princípio central de design: flexibilidade acima de tudo.**
O app não deve impedir nenhuma ação. Gastos recorrentes são sugestões,
não obrigações. Períodos são estruturas opcionais, não pré-requisitos.
O objetivo é eliminar o trabalho manual repetitivo sem perder a liberdade
que o bloco de notas oferece.

O app também precisa suportar **estado incompleto sem bloquear o fluxo**.
Um período pode estar parcialmente preenchido enquanto o usuário avança para
o próximo. Uma fatura pode ser registrada como "paga, composição pendente"
sem impedir o restante do mês. Forçar completude antes de avançar replicaria
a principal causa de procrastinação do bloco de notas: um evento complexo
não resolvido que bloqueia tudo que vem depois.

---

## 2. Fontes de renda e entradas do período

### Requisitos
- Um período pode ter múltiplas fontes de entrada:
  - Salário
  - Adiantamento Quinzenal
  - Saldo Remanescente do período anterior (transita automaticamente)
  - Pagamento de Férias
  - 13º Salário
  - PLR
  - Reembolsos
  - Depósitos de terceiros (custódia temporária)
- Cada fonte tem nome, data de recebimento, **valor previsto** e **valor
  realizado** (dois campos separados)
- Ao criar um novo período, o valor previsto de cada fonte pode ser sugerido
  a partir do valor previsto ou realizado do período anterior, editável antes
  de confirmar
- O valor realizado é preenchido quando o dinheiro efetivamente entra na conta
- Algumas fontes são somadas ao montante principal do período; outras são
  tratadas como blocos independentes de alocação
- O total das entradas do período (previsto e realizado) deve ser calculado
  automaticamente — sem necessidade de refazer a soma manualmente ao editar
  uma fonte
- Saldo remanescente ao fim de um período deve transitar automaticamente
  para o próximo. O saldo final de cada período é armazenado (cached), não
  recalculado do zero a cada leitura.

### Correção final: edição retroativa que altera saldo final é rara — solução proporcional
Duas versões anteriores deste documento propuseram soluções desproporcionais
para esse problema (bloqueio de período, depois cache com cascata assíncrona
em background). Análise mais realista do padrão de uso real:

**A frequência real de edição retroativa que altera o saldo final de um
período passado é extremamente baixa — quase inexistente.** O app é espelho
fiel do saldo real no banco, não uma ficção paralela. Se o saldo de um
período diverge do que está no banco, a correção acontece na hora ou, no
máximo, durante o período seguinte — nunca meses ou anos depois. O único
cenário plausível de precisar editar vários períodos em sequência é o
usuário ter ficado tempo sem usar o app (nesse caso, ele também não estaria
criando novos períodos — o problema se resolve retomando o uso a partir de
onde parou, período a período, não editando retroativamente uma cadeia
longa já fechada).

Dado isso, a solução correta é simples e proporcional:
- **Edição/inclusão retroativa que não altera o saldo final do período**
  (ex: registrar uma transação de custódia que entrou e saiu sem afetar o
  saldo) é **totalmente livre**, sem qualquer aviso ou recálculo — não
  dispara nada, porque nada precisa ser propagado
- **Edição que efetivamente altera o saldo final de um período que já tem
  períodos posteriores existentes** dispara um aviso explícito ("esta
  alteração vai atualizar o saldo de N períodos seguintes — continuar?"),
  e o recálculo em cascata é feito de forma **síncrona**, no momento da
  confirmação — sem necessidade de infraestrutura de propagação assíncrona
  em background, porque o evento é raro o suficiente para não precisar ser
  instantâneo ou invisível
- Nenhum bloqueio de edição é necessário — a proteção contra erro acidental
  vem do aviso explícito antes de confirmar, não de impedir a ação

### Fluxo: múltiplas cascatas independentes dentro do mesmo período
Nas anotações reais, um período pode ter mais de uma cascata de subtração
funcionando em paralelo, não uma lista única — por exemplo, adiantamento e
saldo remanescente somados numa cascata, enquanto férias e 13º cada um tinha
sua própria cascata separada. Para suportar isso, é necessária uma entidade
intermediária entre Período e Item, aqui chamada de **Fluxo**: um Período
tem um ou mais Fluxos, cada Fluxo é uma cascata de subtração ancorada num
conjunto de fontes de renda somadas entre si. Por padrão, um Período tem um
único Fluxo (todas as fontes somadas); o usuário pode criar Fluxos
adicionais para tratar uma fonte separadamente quando quiser decidir a
alocação daquele dinheiro de forma independente do fluxo principal.

Navegação decorrente: o usuário entra no Período, vê a lista de Fluxos
existentes (geralmente apenas um), escolhe um, e vê a lista de itens daquele
Fluxo específico, com saldo próprio.

### Período fechado para edição: trava suave de confirmação de intenção
Correção final: um mecanismo de "fechar período" **é recomendado**, mas com
motivação diferente da inicialmente considerada (proteger contra cascata
de recálculo — descartada por ser evento raro, ver seção 2). A motivação
real e válida é **prevenir edição por engano de navegação**: no bloco de
notas já ocorreu o usuário editar o período errado por confusão ao rolar o
texto; no app, um risco análogo existe (editar pensando estar num período
recente, mas estar navegando em um antigo por engano). Um período "fechado"
exige uma ação deliberada de **reabrir** antes de qualquer edição — não é
bloqueio permanente, é uma confirmação de intenção extra para editar algo
antigo, sem contradizer o princípio de flexibilidade (nada é impedido, só
exige um passo consciente adicional).

Uma flag simples (`fechado: true/false`) é suficiente para representar
isso — sem necessidade de lógica complexa de permissão, só um estado que a
UI usa para exigir a confirmação de reabertura antes de permitir edição.

### Modo de edição em lote: evitar recálculo prematuro em mudanças relacionadas
Ao fazer múltiplas mudanças relacionadas entre si num período (ex: registrar
um depósito retroativo e, na mesma sessão, registrar o uso integral desse
valor), recalcular o saldo a cada mudança individual é trabalho desnecessário
— o estado intermediário entre as mudanças não é o que o usuário quer que
"valha" como resultado final. Solução: entrar em um **modo de edição** pausa
o recálculo automático enquanto o usuário faz as alterações necessárias; o
recálculo (e o aviso de cascata, se o saldo final mudar — ver seção 2) só é
disparado uma única vez, ao confirmar a saída do modo de edição, sobre o
resultado agregado de todas as mudanças feitas na sessão.

Essa opção fica disponível em **qualquer período, incluindo o atual** — o
benefício de evitar recálculo redundante não é exclusivo de períodos antigos.
A única diferença entre editar o período atual e um período fechado é a
exigência extra de reabertura deliberada (ver acima) antes de poder editar;
uma vez em condição editável (por já estar aberto, ou por ter sido reaberto),
o modo de edição em lote funciona da mesma forma nos dois casos.

---

## 3. Fluxo de gastos: subtração em cascata

### O problema
O núcleo do controle é um fluxo: começa com o valor recebido e vai subtraindo
cada item (gasto, investimento, aporte em caixinha), mostrando o saldo após
cada dedução. O resultado final deve ser zero (planejamento fechado) ou um
saldo remanescente.

### Requisitos
- Lista de itens de gasto/alocação ordenável e editável
- Cada item tem: descrição, valor, categoria, data, estado (pendente/realizado)
  e campo de nota opcional
- A descrição é texto completamente livre — sem validação de formato, sem
  categoria obrigatória. O usuário precisa poder escrever qualquer coisa,
  incluindo caracteres informais (`?`, parênteses, colchetes, notas pessoais)
- O campo de nota é texto livre sem limite de tamanho, separado da descrição.
  Usado para cálculos auxiliares, justificativas e contexto que não afeta o
  cálculo do saldo (equivalente aos blocos `UNRELATED` das anotações)
- O saldo acumulado é recalculado automaticamente após cada item
- Entradas no meio do fluxo são suportadas (ex: resgates de carteiras que
  aumentam o saldo antes de uma dedução)
- Itens podem ser reordenados livremente (drag and drop). A ordem na lista
  representa a **sequência cronológica real** dos fatos — a ordem em que os
  eventos aconteceram ou estão planejados para acontecer. É o usuário quem
  posiciona manualmente cada item no ponto da sequência onde ele ocorre,
  exatamente como já fazia nas anotações originais (ex: se a conta de luz
  fosse paga antes da fatura do cartão em determinado mês, ela apareceria
  antes na lista, refletindo a ordem real dos fatos)
- **A data de um item não deve disparar reordenação automática.** Datas nem
  sempre são conhecidas ou preenchidas para todos os itens (metadado
  opcional), o que torna qualquer tentativa de reordenação automática por
  data ambígua e arriscada — poderia mover um item silenciosamente para uma
  posição errada sem o usuário perceber. A ordem manual (drag and drop)
  permanece a única fonte de verdade sobre sequência. Se uma função de
  "ordenar por data" for oferecida no futuro, deve ser uma ação explícita
  acionada pelo usuário (ex: botão dedicado), nunca um efeito colateral
  automático de editar a data de um item
- **Correção sobre hora do item:** avaliado e descartado guardar horário
  (apenas data) no item — não há valor prático em registrar a hora exata em
  que um gasto ocorreu (esforço de digitação sem retorno; se um dia for
  necessário saber a hora exata de uma transação, o extrato do banco é a
  fonte de verdade, não o app). O agendamento de notificação (seção 11) é
  um conceito independente da data do item — vive em estrutura própria
  (ver seção 11), não é derivado do campo de data do item em si
- Menu de contexto por item com opções: editar, excluir, inserir acima,
  inserir abaixo — ativado por **pressionar e segurar** em qualquer parte
  do item. Esse gesto ficou livre após a correção do conflito de gestos
  (reorder passou para uma alça dedicada, seleção múltipla passou a ser
  ativada por menu — ver abaixo), evitando a necessidade de um ícone
  adicional de "três pontos" por item, que competiria por espaço já
  limitado na linha
- A opção "Editar" do menu de contexto é um atalho equivalente a abrir o
  modal/tela de detalhe do item (toque simples) já em modo de edição —
  não é um caminho concorrente, é apenas mais direto. Ambos os caminhos
  levam ao mesmo lugar
- Um item pode ter estado **"ignorado"**: fica visível na lista mas não entra
  no cálculo do saldo. Substitui o padrão de "comentar" gastos com `***` ou
  `/* ... */` nas anotações
- Um item pode ter **zero ou mais tags livres**, complementares à categoria
  principal (seção 5). Enquanto a categoria classifica a natureza contábil
  do item (gasto, investimento, acúmulo), tags são livres e múltiplas,
  permitindo classificações cruzadas para filtro/relatório futuro — ex: um
  gasto pode ter tags `roupas` e `shopee` simultaneamente, permitindo
  responder "quanto gastei com roupas", "quanto gastei na Shopee" ou "quanto
  gastei com roupas na Shopee" com o mesmo dado. Sem hierarquia ou taxonomia
  fixa — o usuário cria tags livremente, na mesma filosofia de flexibilidade
  já adotada para descrição e notas
- Gastos recorrentes são pré-carregados no início de cada período com seus
  valores padrão, editáveis antes de confirmar. **Ancoragem por data, não
  por tipo de período:** um gasto recorrente é configurado com um dia
  habitual do mês (ex: dia 12 para a conta de luz, dia 20 para a internet),
  não vinculado a um tipo específico de período (quinzenal/mensal). Ao gerar
  as sugestões de um novo Período, o app verifica quais recorrentes têm seu
  dia habitual dentro do intervalo de datas daquele Período e os sugere
  automaticamente. Isso garante que a recorrência continue funcionando
  corretamente mesmo se o tipo de período mudar de um mês para outro (ex:
  internet configurada para dia 20 aparece na segunda quinzena se o mês for
  quinzenal, ou no único período do mês se for mensal — sem precisar
  reconfigurar o recorrente). Inteligência de ajuste para fim de
  semana/feriado (mover para o próximo dia útil) não é necessária agora —
  o usuário pode editar a data manualmente quando precisar; fica registrado
  como possibilidade futura
- Gastos com valor variável são pré-carregados com o valor do mês anterior
  como sugestão
- Estado por item: **pendente** ou **realizado** (checkbox). Substitui o `*`
  e o `- OK` das anotações
- **Cascata de checkbox baseada na ordem cronológica.** Como a ordem da
  lista é a sequência real dos fatos (ver acima), marcar/desmarcar um item
  tem implicação lógica sobre os demais:
  - **Marcar** o item N como realizado → marca automaticamente todos os
    itens *antes* de N na lista (cronologicamente anteriores — se algo
    depois já aconteceu, o que veio antes também aconteceu)
  - **Desmarcar** o item N → desmarca automaticamente todos os itens
    *depois* de N (cronologicamente posteriores — a certeza deles dependia
    de N ter ocorrido primeiro)
  - **Dependência:** essa cascata só é correta se a lista estiver
    efetivamente na ordem real dos acontecimentos no momento da marcação.
    O fluxo de trabalho esperado é reordenar primeiro (drag and drop,
    refletindo a realidade quando ela diverge do planejado) e só então
    marcar/desmarcar — não o contrário
  - **Invariante decorrente:** como marcar N sempre marca tudo antes de N,
    o conjunto de itens marcados forma sempre um **prefixo contíguo** da
    lista, a partir do topo — nunca há um item desmarcado "no meio" de um
    trecho marcado
  - **Reordenação e o invariante:** após qualquer reordenação (drag and
    drop), o conjunto de itens marcados deve permanecer um prefixo
    contíguo, sem buracos. Regra unificada: considere o prefixo marcado
    formado pelos **demais** itens (excluindo o item que está sendo
    movido) — seja `k` a posição imediatamente após esse prefixo. Ao mover
    um item para a posição `p`:
    - Se `p ≤ k` (o item cai dentro ou na borda imediata do prefixo dos
      demais) → o item movido **fica marcado**
    - Se `p > k` (o item cai em qualquer posição além dessa borda,
      próxima ou distante) → o item movido **fica desmarcado**,
      independente do estado que tinha antes de ser movido
    - Apenas o **item movido** tem seu estado recalculado — os demais
      itens continuam formando o prefixo contíguo naturalmente, sem
      necessidade de cascata adicional
- O saldo atual (realizado) é calculado como: entradas realizadas − soma de
  todos os itens marcados como realizados

### Informações mínimas necessárias por item na lista
Cada item exibido na lista precisa apresentar, no mínimo:
- Estado (checkbox pendente/realizado)
- Categoria (representada visualmente por ícone — detalhes de qual ícone
  ficam nas sugestões de UI)
- Descrição
- Valor, com o sinal (+ ou -) sempre explícito junto ao número, nunca
  representado apenas por cor de fundo do item
- Data

**Requisito de acessibilidade (não negociável):** cor nunca deve ser o único
portador de significado. Soma/subtração deve ser sempre identificável pelo
sinal explícito (`+`/`-`) junto ao valor — a cor (verde/vermelho) é reforço
visual redundante, não a fonte primária da informação. Isso garante uso
correto por usuários com daltonismo ou qualquer limitação de percepção de
cor.

**Nota sobre saldo acumulado:** diferente do bloco de notas, onde o saldo
`=` precisa ser repetido após cada linha por ser texto estático, no app o
saldo atual deve estar sempre visível de forma centralizada (ex: um
cabeçalho fixo que atualiza conforme os itens são somados/subtraídos), sem
necessidade de repetir o saldo em cada item individual da lista — isso
seria redundância visual sem função no contexto interativo do app.

Tags (definidas acima) não precisam ocupar espaço na linha principal do
item — são metadado para filtro e relatório, não informação de leitura
rápida obrigatória.

### Correção: conflito de gestos era entre reordenar e seleção múltipla, não checkbox
Correção de um ponto anterior deste documento: o conflito real de gestos
não envolvia o checkbox de status (que sempre foi toque simples, sem
ambiguidade). O conflito genuíno era entre **reordenar itens (drag and
drop)** e **ativar seleção múltipla para somar/subtrair (seção 12)** — ambos
poderiam usar "pressionar e segurar" como gesto, gerando ambiguidade sobre
qual comportamento seria disparado.

Resolução adotada: cada comportamento tem seu próprio disparador, eliminando
a ambiguidade:
- **Checkbox** → toque simples, sempre visível (sem mudança)
- **Reordenar** → gesto de arrastar localizado numa **alça dedicada** (ícone
  específico, ex: duas linhas horizontais, padrão comum em listas
  reordenáveis), não em qualquer ponto do item. Isso também previne
  reordenação acidental ao tocar em outras partes do item
- **Seleção para somar/subtrair (seção 12)** → ativada por opção de menu
  (ex: ícone de três pontos no canto da tela, com opções "Somar itens" /
  "Subtrair itens"), não mais por pressionar e segurar. Uma vez ativo o
  modo, toque simples em cada item o adiciona à seleção, na ordem em que
  foram tocados
- Toque em qualquer outra parte do item (fora da alça e fora do checkbox,
  com o modo de seleção desativado) abre o detalhe do item (modal/tela)
- **Pressionar e segurar** em qualquer parte do item (gesto que ficou livre
  após essa correção) abre o menu de contexto do item (editar, excluir,
  inserir acima/abaixo — ver requisito no início desta seção). Isso evita
  a necessidade de um ícone dedicado de "três pontos" por item, poupando
  espaço na linha

Resumo final dos gestos, sem sobreposição:
- Toque simples no checkbox → marca/desmarca
- Toque simples no restante do item → abre detalhe (modal/tela)
- Arrastar pela alça → reordena
- Pressionar e segurar em qualquer parte do item → abre menu de contexto
- Toque simples nos itens, com modo de seleção ativo (ativado via menu
  superior) → seleciona para somar/subtrair

### Saldo do período: a cascata é o instrumento central de planejamento
O saldo projetado após cada item **não é informação secundária de
auditoria** — é a razão de ser do fluxo. O uso real é simular o futuro antes
dele acontecer: "depois de pagar esta conta, quanto vou ter? Quando chegar
na fatura, vou ter dinheiro suficiente para cobrir a parte que não vem de
resgate de caixinha?" Isso só funciona se a sequência for cronológica (ver
acima) e se o saldo projetado após cada item permanecer visível durante a
navegação pela lista — não escondido atrás de um toque de detalhe.

Duas informações de saldo coexistem, ambas relevantes, mas com visibilidade
diferente:

- **Saldo em cascata por item** — quanto resta imediatamente após cada item,
  considerando a ordem cronológica da lista. É calculado e mantido
  atualizado automaticamente para todo item (sem esforço manual, ao
  contrário do bloco de notas), mas **não precisa ficar sempre visível na
  linha do item** — itens já concluídos no passado, esse dado é só
  curiosidade histórica; o valor real está nos itens futuros/planejados,
  onde serve para simular se o planejamento cabe na renda esperada.
  Recomendação de exibição: elemento expansível por item (mesmo padrão de
  lista expansível já sugerido para outras visões — ver documento de UI),
  revelando "Saldo após este item: R$xxx,xx" apenas quando o usuário
  interage com um controle específico de expansão, sem abrir o modal
  completo de detalhe do item
- **Saldo atual (realizado)** — calculado apenas com base nos itens já
  efetivamente marcados como realizados, independente de sua posição na
  lista. Fixo e sempre visível (ex: rodapé da tela), representa "quanto eu
  realmente tenho agora"

### Alerta de saldo previsto insuficiente
Quando a soma dos itens previstos no fluxo supera a renda prevista do
período antes de chegar ao fim da lista, o saldo em cascata fica negativo
em algum ponto. Isso deve ser sinalizado visualmente de forma clara (ex:
destaque de cor/aviso no ponto onde o saldo fica negativo) — é o alerta que
avisa "esse planejamento não cabe na renda esperada para o período", análogo
ao cenário real de um mês de férias com renda baixa onde o gasto planejado
excede o disponível.

---

## 4. Painel analítico do período

O app calcula e exibe automaticamente para cada período um quadro de resumo,
espelhando a estrutura que o usuário tentava manter manualmente nas
anotações antes de abandonar por ser trabalhoso demais:

- Total de Gasto, Acúmulo, Investido e Total Geral — tanto **Previsto**
  quanto **Realizado** (refletindo a distinção já documentada na seção 2
  para fontes de entrada — aqui aplicada aos gastos do período)

**Correção sobre a definição do cálculo "Previsto":** definir Previsto como
soma apenas dos itens `pendente` tem um defeito — o valor degradaria para
zero conforme os itens fossem marcados como `realizado` ao longo do período,
perdendo a informação "o que eu tinha planejado". Definição corrigida:

**Total Previsto (por categoria) = soma de todos os itens da categoria,
independente do estado (pendente ou realizado), excluindo apenas os itens
"ignorados".** Total Realizado continua sendo a soma apenas dos itens
marcados como realizado. Com essa definição, marcar um item como realizado
não o remove do total previsto — ele continua contando (porque fazia parte
do plano) e passa a contar também no realizado. Se tudo que foi planejado
for cumprido, Previsto e Realizado convergem para o mesmo valor ao final do
período — comportamento correto de um planejamento bem-sucedido, sem "zerar"
no meio do caminho.

Isso deriva automaticamente os cenários comuns:
- Uma compra não planejada (ex: impulso numa promoção), se adicionada e
  marcada como `realizado` na hora, passa a contar tanto em Previsto quanto
  em Realizado a partir do momento em que é criada — reflete corretamente
  que ela virou parte do plano (mesmo que retroativamente) no momento em
  que foi registrada
- Um gasto futuro genuinamente planejado (ex: uma compra específica marcada
  para uma data futura) conta em Previsto assim que é adicionado como item
  `pendente`, mesmo sem ser recorrente — correto, porque o usuário de fato
  pretende gastar aquele valor
- A fatura do período (categoria Fatura) entra em Previsto pelo mesmo
  mecanismo, sem tratamento especial

**Simulação sem compromisso:** se o usuário quer apenas especular ("e se eu
comprasse isso?") sem assumir o plano, o item não deve ser adicionado ao
Fluxo — a simulação usa a calculadora embutida (seção 16) ou a visão de
saldo em cascata (seção 3) mentalmente, sem criar um item real que afetaria
os totais previstos.

### Snapshot de "Previsto Inicial": exceção deliberada ao princípio de não duplicar dados
Mesmo com a correção acima, "Previsto" continua sendo um valor **vivo** —
editar o valor de um item recorrente no meio do período atualiza o total
retroativamente, perdendo a capacidade de responder "quanto eu tinha
comprometido no dia em que criei este período, antes de qualquer ajuste".
Isso não pode ser resolvido com um cálculo derivado; exige um snapshot
(cópia congelada, tirada num momento específico).

**Esta é uma exceção deliberada e reconhecida ao princípio geral de nunca
duplicar dados** (que guia o restante deste documento). A diferença: aqui
o propósito da cópia é justamente ser um registro histórico imutável — não
uma fonte de verdade viva que poderia divergir por acidente. É o mesmo
padrão usado por qualquer app de orçamento pessoal ("planejado no início do
mês" vs. "gasto real", comparados lado a lado).

Proposta: ao criar o Período, capturar um snapshot imutável de
`previsto_inicial` por categoria, no momento da criação (tipicamente,
refletindo os gastos recorrentes pré-carregados naquele instante). Esse
valor nunca é recalculado depois. O "Previsto" vivo (definição corrigida
acima) continua existindo em paralelo para uso corrente durante o período.
Os dois coexistem com propósitos diferentes: um para planejamento em tempo
real, outro para comparação histórica "planejado vs. realizado" ao final.

Exibição: no Quadro de Resumo (início desta seção), cada categoria pode
exibir três valores lado a lado — `Planejado inicialmente (snapshot) |
Realizado | Previsto (vivo, atual)` — respondendo visualmente "onde comecei,
onde estou agora de fato (realizado), onde planejo chegar (previsto)". Ordem
sugerida com Realizado antes de Previsto (já que Previsto ≥ Realizado
sempre); ajustável livremente no design de UI conforme preferência de
leitura.

### Rastreamento de origem recorrente: cópia no momento da criação, não FK viva
Em vez de criar uma nova categoria de gasto ("Recorrente") e em vez de uma
FK obrigatória do item para a definição do Recorrente (que impediria excluir
o Recorrente sem quebrar itens já criados, ou exigiria cascade delete
destrutivo), a abordagem correta é: ao criar um novo Período, o app lê as
definições de Recorrente vigentes e **cria uma cópia independente** de cada
uma como Item novo no Fluxo (descrição, valor e categoria copiados naquele
momento). O item resultante tem um campo opcional `origem_recorrente_id`,
que é apenas uma **referência histórica**, não uma dependência estrutural
— útil para relatório e para sugerir valor em períodos futuros, mas se a
definição do Recorrente for excluída ou desativada depois, os itens já
materializados em períodos passados permanecem intactos (são cópias, não
vínculos vivos). Mesmo princípio de snapshot imutável já aplicado ao
`previsto_inicial` acima, agora aplicado ao próprio item recorrente.

Isso permite calcular **Total Recorrente** = soma dos itens com essa
referência preenchida, sem precisar de categoria dedicada. Itens adicionados
manualmente (ex: uma compra planejada para uma data futura, sem ser
recorrente) não têm essa referência — contam em Previsto normalmente, mas
não em Total Recorrente.

**Consequência direta:** no momento da criação do Período, antes de qualquer
adição manual, `Previsto == Total Recorrente` (tudo que existe até então
veio dos recorrentes pré-carregados). Conforme itens planejados não
recorrentes são adicionados, Previsto passa a superar Total Recorrente —
comportamento esperado.

### Fatura como recorrente: valor sempre derivado, nunca fixo ou congelado
O recorrente de fatura é uma exceção ao padrão dos demais recorrentes. Os
outros recorrentes materializam como cópia independente com valor fixado
no momento da criação (ver acima). A fatura não pode seguir esse padrão,
porque seu valor precisa continuar vivo até o fechamento real — novas
compras no crédito podem ser adicionadas a ela em qualquer momento antes de
o cartão fechar.

Isso já decorre naturalmente do que a seção 9 estabelece: toda compra no
crédito, ao ser registrada, já sabe a qual fatura pertence (baseado na data
da compra e nas datas de fechamento/vencimento do cartão). Portanto, o valor
da fatura **não precisa de nenhuma inteligência de cálculo especial** — é
simplesmente a soma corrente de todas as compras já vinculadas a ela, a
qualquer momento. O "recorrente de fatura" apenas garante que o item Fatura
apareça automaticamente no novo período, com esse valor derivado (nunca
fixo) — diferente dos demais recorrentes, que fixam um valor no momento da
criação.

### Excluir e desativar Recorrente: dois caminhos independentes, ambos seguros
Como os itens materializados a partir de um Recorrente são cópias
independentes (não vínculos vivos via FK, ver acima), **tanto excluir quanto
desativar a definição do Recorrente são seguros** — nenhum dos dois afeta
itens já criados em períodos passados, porque o histórico não depende da
definição original continuar existindo ou estar ativa.

Os dois caminhos resolvem necessidades diferentes e coexistem:
- **Desativar** (`ativo: true/false`) — pausa reversível, mantendo toda a
  configuração já refinada (nota, tags, vínculo com caixinha específica,
  valor ajustado ao longo do tempo). Ideal para vigência temporária com
  intenção clara de retomar (ex: pausar por alguns meses e reativar depois)
  sem precisar reconfigurar tudo do zero
- **Excluir** — remoção definitiva, para recorrentes que genuinamente não
  fazem mais sentido

A preocupação de poluir a tela de gestão com recorrentes inativos se resolve
por **filtro de visualização** (mostrar apenas ativos por padrão, com opção
de alternar para ver os inativos), não por forçar exclusão. Os dois recursos
não são mutuamente exclusivos.

### Recorrência de renda (salário, adiantamento) exige tratamento diferente de recorrência de gasto
Gastos e investimentos recorrentes materializam seu item no próprio Período
cuja data de ocorrência pertence ao seu intervalo. **Fontes de renda
recorrente (salário, adiantamento) não seguem essa regra** — como já
documentado na seção 1, o salário recebido no fim de um mês financia a
primeira quinzena do mês seguinte, não o período em que a data de
recebimento cai.

Por isso, a definição de um Recorrente de renda precisa de um campo
adicional de **deslocamento de período de destino** (ex: "materializa como
entrada do próximo Período", em vez de "materializa no Período que contém
a data"). Isso é uma categoria de recorrência distinta da recorrência de
gasto/investimento, e deve ser tratada como tal na modelagem — não é uma
variação do mesmo mecanismo, é uma regra de destino diferente.
- Total no Crédito — soma das **compras feitas no crédito durante o período**
  (novo endividamento gerado agora), não o valor da fatura a pagar. São
  conceitos diferentes: a fatura do período pode incluir parcelas de compras
  de meses anteriores, enquanto o "Total no Crédito" reflete só o volume de
  compras parceladas/no crédito originadas neste período específico
- Cada total acima **separado entre "Meu" e "Terceiro"**, além do total
  combinado — reflete o padrão observado nas anotações onde o usuário
  separava "Gasto Meu" de "Gasto Outros" para saber sua exposição real
  distinta da de terceiros administrados
- Saldo final do período

Não requer preenchimento manual — é totalmente derivado dos itens do fluxo
por categoria e por titular (usuário vs. terceiro). A forma de apresentação
na tela (seção fixa, aba própria, popup) é decisão de design a definir
depois — o requisito aqui é o dado, não o layout.

### Duas visões do período: simplificada (resumo) e detalhada (fluxo) com drill-down
O período tem duas visões complementares, não concorrentes:
- **Simplificada** — o quadro de resumo acima, com os totais agregados
- **Detalhada** — o fluxo completo de subtração em cascata (seção 3), item
  por item

A visão simplificada deve permitir **drill-down** para a detalhada: ao
interagir com um total específico do resumo (ex: tocar em "Total Gasto"),
o app navega para a lista filtrada apenas dos itens que compuseram aquele
total. A visão simplificada funciona como um painel de entrada rápida, e a
detalhada como a fonte completa por trás de cada número — nunca dados
duplicados, sempre a mesma origem (os itens do fluxo), só apresentados em
dois níveis de agregação diferentes.

### Terceira visão: navegação por calendário (opcional, baixa prioridade)
Cada item do fluxo já tem uma data (seção 3), o que permitiria uma terceira
forma de navegar pelos mesmos dados: uma visão de calendário onde o usuário
seleciona um dia específico e vê apenas os itens daquela data — inspirada
no padrão comum em apps de finanças pessoais (calendário mensal, toque no
dia, lista filtrada, navegação dia anterior/seguinte). Não requer nenhuma
estrutura de dado nova (a data já existe em cada item).

**Avaliação:** valor questionável para o padrão de uso real do usuário —
poucos gastos por dia, muitos dias sem nenhum item, o que tornaria a
navegação dia-a-dia mais vazia do que útil. Buscar por data já é possível
na lista detalhada padrão. Fora do MVP e de baixa prioridade — não
descartada, mas sem motivo para priorizar frente a outras visões.

---

## 5. Categorias de itens de gasto

Identificadas nas notas:

- **Gasto fixo** — contas recorrentes mensais
- **Gasto variável** — compras avulsas
- **Gasto em nome de terceiro** — despesas pagas pelo usuário em benefício
  de terceiros sem expectativa de reembolso (ex: recarga de celular de
  familiar). Não cria carteira de terceiro — é simplesmente um gasto do
  usuário classificado por destinatário
- **Gasto pago por terceiro** — item recorrente que ocorreu no mês mas não
  saiu do bolso do usuário (ex: conta paga por um familiar). O item
  aparece como sugestão no período com seu valor real preservado, mas é
  marcado como "ignorado" para não entrar no cálculo do saldo. O valor é
  mantido para referência histórica e futuros gráficos. O campo de nota
  (ou um campo de motivo curto) registra quem pagou. Semanticamente diferente
  de "ignorado por decisão" — o gasto aconteceu com aquele valor, só não foi
  custeado pelo usuário naquele mês
- **Crédito** — compras no cartão de crédito (não subtraem imediatamente;
  acumulam na fatura)
- **Acúmulo** — aportes em caixinhas de objetivo
- **Investimento** — aportes em carteiras de investimento
- **Fatura** — pagamento de fatura de cartão (valor composto de múltiplas fontes)
- **Transferência entre contas** — movimentação entre contas do próprio usuário
- **Custódia de terceiros** — dinheiro de terceiros que passa pela conta

---

## 6. Contas e locais onde o dinheiro está

### Requisitos
- O app gerencia múltiplas contas/locais de dinheiro simultaneamente
- Exemplos de tipos: conta principal, saldo separado, conta secundária,
  conta em outra instituição
- Cada conta tem saldo atual acompanhado individualmente
- Transferências entre contas são registradas como movimentações vinculadas
  (saída de uma = entrada na outra)
- Rendimentos de conta são registráveis

---

## 7. Carteiras e caixinhas

### O problema
Hoje o usuário copia a carteira inteira todo mês para saber o estado atual.
No app, a carteira existe uma única vez e os movimentos são registrados
cronologicamente.

### Requisitos
- Uma carteira tem: nome, objetivo/descrição, tipo (acúmulo ou investimento),
  prazo (data-alvo, opcional) e quantia desejada (valor-alvo, opcional)
- Cada carteira contém aportes individuais com:
  - Valor aplicado
  - Data de aplicação
  - Data de vencimento (quando aplicável)
  - Instituição financeira
  - Tipo de produto (CDB, LCI, LCA, Caixinha, Tesouro, etc.)
  - Rentabilidade (% CDI, IPCA+X%, prefixado %)
  - Saldo atual (calculado ou informado manualmente)
- Aportes podem ser resgatados parcial ou totalmente; o resgate registra
  data, valor e destino
- A carteira mostra o histórico completo de aportes e resgates
- Aportes podem ser marcados como liquidados
- Um aporte pode ser marcado como "reinvestimento" de um aporte anterior
  vencido, com referência ao aporte de origem e valor original

### Tipos de carteiras identificados
- Carteiras de objetivo de médio prazo (acúmulo com data alvo)
- Carteiras de acúmulo mensal contínuo
- Carteiras de reserva (emergência)
- Carteiras de reserva de oportunidade (acúmulo para compra futura de ativos
  de renda variável — FIIs, ações, ativos exterior, criptomoedas. Não são
  compras de ativos, são caixinhas que acumulam até o momento da compra)
- Carteiras de liquidez imediata (reserva para imprevistos de curto prazo,
  sem controle de aportes individuais, só saldo atual e movimentações pontuais)
- Carteiras de investimento em renda fixa
- Carteiras de terceiros administradas pelo usuário
- Caixinha dedicada a fatura de cartão
- Caixinha de acúmulo de dividendos (valor total rendendo, sem controle
  granular por empresa ou evento — apenas acúmulo para reinvestimento futuro)

### Campos de meta da carteira: prazo e quantia desejada
"Juntar para consumo" não é um filtro adicional — é o próprio tipo de
carteira já listado acima (Acúmulo/Caixinha). O que de fato vale documentar
são dois campos legítimos de uma Carteira com objetivo, usados para definir
a meta desde a criação:

- **Prazo** (data-alvo) — quando o objetivo deve ser alcançado
- **Quantia desejada** (valor-alvo) — quanto se pretende juntar/investir

Esses campos permitem exibir progresso na própria tela da carteira (ex:
"R$6.500 de R$10.000, faltam 720 dias").

Filtrar carteiras por faixas desses valores (ex: "prazo menor que 2 anos")
tem valor questionável, principalmente com poucas carteiras — é complexidade
de UI sem ganho real de uso. Mais útil seria **ordenar** a lista de carteiras
por proximidade do prazo ou distância até a meta, para ajudar a priorizar
onde alocar dinheiro num período — diferente de filtro (inclusão/exclusão
binária), ordenação agrega valor mesmo com poucos itens. Sem decisão fechada
sobre implementar a ordenação agora; registrado para o design de telas.

### Rendimento: informado manualmente, não calculado pelo app
O app não recalcula rendimentos de renda fixa — o banco calcula, o usuário
informa o valor atualizado quando necessário. Cada aporte exibe:
- Valor aplicado original
- Valor atual (informado manualmente pelo usuário)
- O total da carteira pode exibir ambos separadamente

**Motivação para essa decisão no MVP:** o cálculo em si não é complexo —
juros compostos com taxa diária é implementável. O que requer estudo é a
camada de detalhes: fonte da taxa CDI diária (ex: API do Banco Central),
base de cálculo usada pelo banco (252 ou 360 dias úteis), momento e alíquota
de desconto de IR (tabela regressiva por prazo), aplicação de IOF nos primeiros
30 dias, e possíveis variações entre instituições. Implementar sem validar
contra os extratos reais do banco pode gerar divergências. Por isso, para o
MVP o usuário informa o valor manualmente. O cálculo automático fica para
uma versão posterior, após estudo e validação da metodologia exata.

Caso especial — contas que rendem automaticamente com liquidação diária
(comum em contas de pagamento/carteiras digitais): rendem de forma automática,
já líquido de impostos. O usuário registra o rendimento consolidado do período
manualmente (sem rastrear por depósito individual). O app não tenta modelar
os depósitos individuais como aportes separados nesse tipo de conta — quando
há uma saída, a instituição escolhe de quais depósitos resgatar, sem controle
do usuário.

### Alternativa descartada: automação de coleta de dados do app do banco
Avaliada e descartada a ideia de automatizar a extração de dados diretamente
do app do banco (via serviço de acessibilidade ou captura de tela) para evitar
a duplicação manual de dados. Motivos da rejeição:

- Apps bancários usam certificate pinning, detecção de root/emulador e bloqueio
  de captura de tela justamente para impedir esse tipo de automação
- O único mecanismo técnico viável no Android (Accessibility Service) é o
  mesmo usado por malware bancário para roubo de credenciais — o próprio banco
  pode identificar o padrão como suspeito e bloquear a conta
- Viola os termos de uso de praticamente todas as instituições financeiras
- Exigiria manutenção constante, pois qualquer atualização do app do banco
  quebraria a automação
- Open Finance (Open Banking) é o caminho oficial e seguro para integração
  futura, mas provavelmente não expõe o nível de granularidade necessário
  (breakdown de aportes individuais dentro do mesmo produto financeiro),
  porque essa informação não é exposta nem nas interfaces oficiais do banco

Decisão: manter a entrada manual de valores atualizados (já documentada
acima) como abordagem para o MVP e para o futuro. Open Finance pode ser
avaliado depois como fonte de dados oficiais quando o backend existir, mas
sem expectativa de resolver a granularidade por aporte.

### Aporte de saldo inicial para migração de dados históricos
Carteiras mais antigas podem não ter o histórico completo de aportes
individuais — apenas o saldo atual é conhecido. O app precisa suportar um
"aporte de saldo inicial" sem data de origem para esses casos, permitindo
que o usuário registre o valor atual da carteira sem precisar reconstituir
todo o histórico.

---

## 8. Carteiras de terceiros

### Requisitos
- Uma carteira pode ter um titular diferente do usuário (pai, mãe, etc.)
- Os aportes seguem a mesma estrutura das carteiras próprias
- Resgates da carteira de terceiro podem ser vinculados a um gasto específico
- O saldo da carteira de terceiro é exibido separadamente do patrimônio próprio
- Itens do fluxo vinculados a uma carteira de terceiro são identificados
  visualmente como pertencentes a outra pessoa

---

## 9. Cartões de crédito e faturas

### Requisitos
- O app suporta múltiplos cartões de crédito
- Cada cartão tem data de fechamento e data de vencimento configuráveis
- Compras no crédito não subtraem do saldo imediatamente; acumulam na fatura
  do período correspondente
- Uma compra no crédito pode ter uma caixinha vinculada para reserva do valor
  (o usuário separa o dinheiro na hora da compra, para resgatar na fatura)
- Compras parceladas são registradas com número de parcelas; cada parcela
  aparece na fatura do mês correspondente automaticamente
- A fatura é composta por múltiplas fontes: resgates de caixinhas + complemento
  da conta corrente
- O app calcula automaticamente quanto falta complementar após os resgates
- A fatura pode ter uma parte de terceiro vinculada à carteira do terceiro
- Suporte a créditos e débitos mistos numa mesma fatura: caso de estorno de
  compra parcelada após fechamento, onde o valor final é sempre derivado
  automaticamente da soma dos itens da fatura (créditos e débitos) — nunca
  editado manualmente como um ajuste solto (ver detalhamento abaixo)

### Observação de comportamento real
Compras parceladas do cartão têm o número de parcela rastreado manualmente
hoje. O app deve fazer esse rastreamento automaticamente.

### Fatura paga por débito automático sem composição prévia
A fatura pode ser debitada automaticamente antes de o usuário ter organizado
a composição (de onde cada valor sairia). Nesse caso o usuário registra a
fatura como "paga, valor X" e resolve a composição depois — vinculando
retroativamente os resgates de caixinhas e a parte de terceiros. O app não
bloqueia o avanço por composição pendente.

### Estorno de compra parcelada com fatura já fechada
Comportamento real da operadora: quando uma compra parcelada é estornada após
o fechamento de uma fatura, o banco não consegue estornar a parcela já cobrada.
Na fatura seguinte aparecem:
- Crédito do valor integral da compra (+)
- Débito das parcelas futuras canceladas (-)
- O resultado líquido é um crédito equivalente à parcela que já foi paga na
  fatura anterior — que pode ser usado para abater a fatura seguinte

O app precisa suportar itens com valor positivo (créditos) misturados com
débitos numa mesma fatura, e calcular o saldo líquido corretamente. Não é
um ajuste manual — é o registro fiel do extrato da fatura.

### Reembolso de pagamento de fatura
Caso em que o usuário pagou a parte do terceiro da fatura com seu próprio
dinheiro (por falta de tempo para organizar os resgates antes do débito),
e depois resgatou da carteira do terceiro para se reembolsar. No app:
1. Pagamento da fatura registrado com a parte do terceiro saindo da conta
   do usuário (não da carteira dele)
2. Resgate da carteira do terceiro com categoria "reembolso ao usuário",
   entrando como `+` no fluxo do período
3. O app vincula o resgate ao pagamento que originou a dívida, mantendo
   a rastreabilidade completa

---

## 10. Explicação de Gasto (gasto composto) e cruzamento automático de dados

### Conceito unificado
"Explicação de Gasto" é um único conceito que se repete em praticamente todo
mês nas anotações: um gasto tem um valor total, e esse valor é detalhado em
múltiplas fontes até fechar o total. Não são vários tipos diferentes de caso
— é o mesmo padrão estrutural aplicado a contextos diferentes. Exemplos reais
que são todos a mesma coisa:
- Pagamento de fatura de cartão (o caso mais recorrente, ocorre todo mês)
- Compra de item de alto valor custeada por múltiplos resgates de carteiras
  diferentes
- Gasto de terceiro pago pelo usuário, com resgates de aportes específicos
  da carteira desse terceiro
- Transferência avulsa para terceiro composta por liquidação de múltiplos
  aportes de uma carteira de terceiro + complemento do usuário

### Requisitos
- Uma "Explicação de Gasto" tem: valor total (alvo ou calculado), descrição,
  data, e uma lista de **fontes**
- Cada fonte tem: valor, origem (resgate de um aporte específico de uma
  carteira própria ou de terceiro, complemento do saldo/conta, ou "valor
  desconhecido"), e nota opcional própria
- O total das fontes deve bater com o valor do gasto
- O complemento final (diferença entre o total das fontes e o valor do gasto)
  é calculado automaticamente
- Uma composição pode ter várias fontes vinculadas a aportes diferentes da
  mesma carteira (própria ou de terceiro) — a granularidade é por aporte,
  não por carteira (ver seção sobre múltiplos aportes abaixo)
- O pagamento de fatura é apenas um dos contextos onde uma Explicação de
  Gasto é usada — a mesma entidade serve para qualquer gasto de valor
  significativo que precise de detalhamento de composição

### Cruzamento automático de dados
Toda movimentação tem origem e destino — ambos devem ser atualizados
automaticamente, sem cópia manual. Exemplos:

- **Aporte em carteira** → saldo da carteira sobe, valor deduzido do fluxo
  do período aparece vinculado àquela carteira
- **Resgate de carteira** → saldo da carteira cai, valor entra automaticamente
  como `+` no fluxo do período ou como fonte de um gasto composto
- **Transferência entre contas** → sai de uma conta e entra na outra,
  ambas atualizadas
- **Saldo remanescente** → ao fechar um período, o saldo restante transita
  automaticamente como entrada do período seguinte
- **Composição de fatura** → os resgates de caixinhas vinculadas aparecem
  automaticamente como fontes do pagamento, com o complemento calculado

### Fontes de pagamento de fatura não se limitam a uma caixinha específica
Qualquer carteira ou caixinha pode ser vinculada como fonte de um item da
fatura, independente do nome ou propósito original da carteira.

### Visão detalhada vs. visão agrupada por carteira de origem
Uma composição de fatura pode ter vários itens vinculados à mesma carteira
de origem (ex: internet, um jogo e outro jogo, todos saindo da Cx. Fatura).
Cada item é registrado individualmente para manter o controle de que compõe
o total, mas o usuário precisa alternar entre duas visões da mesma composição:

- **Visão detalhada** — lista cada item individualmente com descrição e valor
  (como é hoje, para auditoria e controle do que compõe o total)
- **Visão agrupada** — soma os itens por carteira de origem, mostrando quanto
  no total sai de cada carteira (para ação: o resgate é feito de uma vez só,
  pelo total, não item a item)

Similar à alternância entre visualização em lista ou em grade, mas aqui a
alternância é entre detalhado e agrupado. É o mesmo princípio da seleção e
soma de itens (seção 12), aplicado automaticamente por origem dentro de uma
composição, em vez de seleção manual numa lista livre.

Em ambas as visões, a seleção manual de itens (seção 12) continua disponível:
- Na visão detalhada, selecionar e somar itens individuais específicos
- Na visão agrupada, selecionar e somar totais de carteiras diferentes
  (ex: quanto sai somando duas caixinhas de origens diferentes)

### Onde vive o campo de nota longa (o "textão")
Casos como a análise de um estorno de compra parcelada ou o raciocínio de uma
transferência composta por múltiplos aportes de terceiro geram um texto longo
explicando o que aconteceu e como os números foram calculados. Esse texto
pertence ao nível da **Explicação de Gasto como um todo**, não a uma fonte
individual dentro dela — porque é uma explicação do raciocínio da composição
inteira. Resumo dos níveis onde nota pode existir:

- **Item simples do fluxo** (sem composição) → campo de nota no próprio item
  (seção 3)
- **Explicação de Gasto** (a composição inteira) → campo de nota no nível da
  composição — é aqui que vive o "textão" de casos complexos como estornos
  ou transferências com múltiplos aportes de terceiro
- **Fonte individual dentro da composição** → nota curta opcional, para
  contexto pontual daquela fonte específica (ex: "resgate parcial, valor
  informado pelo banco")

### Conceito de dados: item e fontes (sem entidade duplicada)
Um item do fluxo é sempre a mesma coisa, simples ou composto — não existem
duas entidades diferentes ("item simples" vs. "Explicação de Gasto"). A
diferença é apenas se o item tem fontes associadas:
- Item simples → sem nenhuma fonte associada
- Item com Explicação de Gasto → uma ou mais fontes associadas

O valor e a descrição vivem só no item. As fontes explicam apenas *como*
aquele valor foi composto. Isso evita duplicidade entre "quanto saiu" e
"qual a descrição" existir em dois lugares diferentes.

### "Minha parte" e "parte do terceiro" não são campos — são agrupamento
Nas anotações, a fatura é dividida visualmente entre "minha parte" e "parte
do pai". Isso não precisa ser modelado como uma estrutura própria. Cada fonte
já referencia uma carteira de origem (ou complemento do saldo), e toda
carteira já tem um titular (usuário ou terceiro). "Minha parte" é apenas a
soma das fontes cujo titular da carteira é o usuário; "parte do terceiro" é
a soma das fontes cujo titular é o terceiro. É o mesmo princípio da visão
agrupada (ver acima), agrupando por titular em vez de por carteira específica.
Nenhum campo extra é necessário para marcar de quem é cada fonte — a
informação já está implícita na carteira referenciada.

### Itens com valor desconhecido na composição
Caso real: em algumas instituições, ao resgatar de uma caixinha, o sistema
escolhe automaticamente de quais aportes sai o valor e não informa o
breakdown individual. O usuário sabe o valor total resgatado mas não quanto
saiu de cada aporte.

Regra de negócio para composições:
- Por padrão, o valor total é a soma dos itens e o campo total é bloqueado
  para edição direta — evita zerar acidentalmente todos os valores ao editar
  o total
- Um item pode ser marcado explicitamente como **"valor desconhecido"**
  (valor zerado, flag de desconhecido). Quando há exatamente um item
  desconhecido, o app calcula seu valor automaticamente pelo complemento:
  `total - soma dos conhecidos`
- Quando há dois ou mais itens desconhecidos, o campo total se torna editável
  manualmente e a calculadora embutida fica disponível. Os itens desconhecidos
  permanecem zerados — o usuário distribui os valores manualmente
- Itens com valor já preenchido pelo usuário não são afetados pela edição do
  total, prevenindo perda de dados por edição acidental

### Composição com múltiplos aportes de uma mesma carteira de terceiro
Uma composição pode ter várias fontes vinculadas à mesma carteira de terceiro,
cada uma originada de um aporte diferente dentro dela. Exemplo genérico: para
juntar um valor X da carteira de um terceiro, esse valor pode vir de três
aportes diferentes dentro dela (parte de um CDB, parte de uma caixinha, parte
de outro CDB) — não de um resgate único. O modelo de dados não pode limitar
"uma fonte = uma carteira"; precisa ser "uma fonte = um aporte específico",
permitindo várias fontes apontando para aportes distintos da mesma carteira.
Esse padrão apareceu repetidamente nas anotações mais antigas, quando a
carteira de um terceiro tinha múltiplos depósitos ativos simultaneamente.

> **Nota para o design:** detalhar o modelo de dados que representa essa
> vinculação entre resgate de carteira e item de fatura ou gasto composto,
> garantindo que a rastreabilidade seja completa sem exigir entrada manual
> duplicada.

---

## 11. Gastos recorrentes e previstos

### Requisitos
- Gastos recorrentes são configurados uma vez e aparecem automaticamente em
  todo novo período como sugestão (não como obrigação)
- Tipos de recorrência: mensal, quinzenal, por período específico
- Um gasto recorrente tem valor padrão editável antes de confirmar no período
- Estado do gasto: pendente (agendado, ainda não pago) ou realizado (pago).
  Correção terminológica: o estado do item é sempre "pendente/realizado" —
  "previsto" é reservado para o conceito de agregação no painel analítico
  (seção 4: soma de itens pendentes por categoria), não para o estado de
  um item individual (ver seção 3)
- Gastos previstos com data futura podem gerar notificação push no dia do
  vencimento
- Notificação de vencimento de ativo de renda fixa: quando um aporte tem
  data de vencimento cadastrada, o app notifica o usuário próximo ao vencimento
  para que possa decidir o que fazer com o valor resgatado (reinvestir,
  realocar, etc.)
- Horário padrão de notificação configurável globalmente, com override por
  gasto individual

### Notificação como entidade própria, independente da data do item
O agendamento de notificação (quando o app deve avisar) é conceitualmente
independente da data do item (quando o gasto ocorre). Deve ser modelado como
uma estrutura própria (ex: entidade Notificação, com data/hora de disparo
e referência ao item relacionado), não derivado automaticamente do campo de
data do item. Isso permite flexibilidade real: notificar num horário
diferente da data do item (ex: "avisar 1 dia antes, às 9h", ou "avisar no
mesmo dia, no horário padrão configurado nas preferências do usuário"), sem
exigir que o item em si carregue um campo de hora que não tem uso prático
fora desse contexto.

---

## 12. Seleção e soma de itens

### Requisito
- O modo de seleção múltipla é ativado via **opção de menu** (ex: ícone de
  três pontos, com opções "Somar itens" / "Subtrair itens"), não por
  pressionar e segurar — ver correção do conflito de gestos na seção 3
- Uma vez o modo ativo, toque simples em cada item o adiciona à seleção, na
  ordem em que foram tocados
- Com múltiplos itens selecionados, exibir a soma (ou diferença, conforme o
  modo escolhido) dos valores selecionados
- Exibir também a diferença entre dois itens selecionados quando exatamente
  dois estiverem selecionados, independente do modo escolhido
- Resultado exibido em destaque na tela (ex: popup ou barra fixa no topo,
  abaixo do menu superior)
- Reordenação de itens (drag and drop) usa uma **alça dedicada** (ícone
  específico na lateral do item), não o item como um todo — ver seção 3

---

## 13. Transações de custódia (dinheiro de terceiros sem carteira permanente)

### O problema
Casos em que dinheiro de terceiro entra na conta, é aplicado/usado e devolvido,
sem criar carteira permanente.

### Requisito
- Suportar registro de transação de custódia: entrada, aplicação, devolução
- Não compõe o patrimônio próprio
- Aparece no histórico do período como movimentação neutra (entra e sai)

---

## 14. Relatório mensal e exportação (pós-MVP)

- Geração de PDF com resumo do mês: entradas, gastos por categoria, saldo final
- Gráficos opcionais no relatório

---

## 15. Gráficos e análise (pós-MVP)

Identificados como necessários para superar a limitação do bloco de notas:

- Evolução de gastos mês a mês (linha)
- Gastos por categoria (pizza/barra)
- Comparação receita vs. gastos vs. investimentos por mês
- Evolução do patrimônio total ao longo do tempo
- Evolução por carteira individual
- Histórico de dividendos (quando módulo de investimentos estiver ativo)
- Comparação de carteira com benchmarks (CDI, Ibovespa, IFIX, poupança)

---

## 16. Considerações de arquitetura para suporte futuro a múltiplos usuários

Registrado das anotações brutas originais: mesmo sendo um app de uso pessoal
exclusivo, vale desenhar o banco de dados pensando em não fechar a porta para
múltiplos usuários no futuro, caso o projeto evolua nessa direção.

- **Faixas de ID para distinguir registros padrão do sistema vs. registros
  do usuário.** Prática observada em sistemas profissionais: registros que
  seriam comuns a qualquer usuário (ex: templates de gastos recorrentes como
  "conta de água", "conta de luz") ocupam uma faixa de ID reservada (ex: 1 a
  99.999), enquanto registros criados por um usuário específico começam a
  partir de outra faixa (ex: 100.000+). Isso não é uma feature do MVP nem
  uma prioridade agora — é uma consideração de modelagem de banco de dados
  para não fechar portas, relevante quando o schema for desenhado de fato
  (Phase 11/12 do roadmap). Implicaria em relacionamentos N-para-N entre
  usuário e entidades como gasto recorrente, com tabelas intermediárias,
  caso o suporte a múltiplos usuários seja implementado de verdade.

---

## 17. Funcionalidades mapeadas para versões futuras (fora do escopo atual)

- Módulo completo de renda variável: registro de ordens de compra e venda
  de ações, FIIs e criptomoedas com carteira de ativos no app. Quando o
  usuário decidir comprar, o fluxo seria: resgate da caixinha de reserva de
  oportunidade (aparece como `+` no período) seguido do item de compra do
  ativo (aparece como `-`), e o ativo é adicionado à carteira de renda variável.
  **Lembrete:** existe uma planilha Excel pessoal com o histórico de ordens
  de compra/venda, indicadores acompanhados e gráficos de evolução da
  carteira de renda variável já usados no passado. Ao retomar este módulo
  para levantamento de requisitos detalhado, revisar essa planilha como
  insumo — ela contém a estrutura real de dados e a lógica de acompanhamento
  já validada pelo usuário, análoga ao papel que as anotações mensais
  tiveram para o levantamento de requisitos do MVP
- Eventos corporativos que alteram quantidade de ativos sem compra
  (bonificações, subscrições, desdobramentos)
- Cálculo automático de rendimento de renda fixa pelo app
- Integração com APIs bancárias para importação automática de dados
- Integração com B3 ou custodiantes para posição de renda variável
- Controle granular de dividendos por empresa/fundo com histórico mensal.
  Não descartado — apenas sem urgência hoje porque os valores recebidos
  atualmente são pequenos e não compensam o esforço de rastreamento (ver
  seção 7). Fica no backlog para quando o volume de dividendos recebidos
  justificar o controle granular. Nota conceitual: dividendo é o espelho da
  "Explicação de Gasto" (seção 10), só do lado da receita — uma fonte de
  renda "Dividendos" composta por múltiplos recebimentos individuais
  (dividendo, JCP, JCC) de diferentes empresas/fundos. A nível de dado, o
  conceito de "composição" é o mesmo dos dois lados (entrada ou saída), só
  muda a direção do fluxo

---

## 18. Calculadora embutida

### Requisito
- Calculadora acessível de qualquer tela do app, sem sair do contexto atual
- Ao fechar a calculadora, o app oferece a opção de usar o resultado para
  preencher o campo em foco
- Elimina a necessidade de alternar entre o app e a calculadora do sistema
  para cálculos auxiliares durante o registro de gastos ou composições

### Calculadora de Renda Fixa (ferramenta separada, pós-MVP)
Diferente da calculadora simples acima, esta é uma ferramenta de simulação:
projeção de rendimento de um investimento de renda fixa dado valor, taxa e
prazo, permitindo comparar cenários antes de investir. Já prevista no roadmap
geral (Phase 10 — "Fixed-income calculator"). Depende do estudo de metodologia
de cálculo de rendimento mencionado na seção 7.

---

## 19. Busca de investimento por carteira (pós-MVP)

### Requisito
Permitir buscar um ativo ou aporte específico e ver a qual Carteira ele
pertence. Como no modelo de dados todo aporte já nasce vinculado a uma
Carteira (seção 7), essa busca é uma consulta direta sobre dado existente —
não requer estrutura nova. Útil quando o usuário tem muitas carteiras e
precisa localizar rapidamente onde um investimento específico está alocado.

---

## 20. Bloqueio de acesso ao app (MVP) vs. autenticação real (futuro)

### Decisão
Login completo (conta, autenticação, backend) não faz sentido no MVP — não
há servidor para autenticar contra, e um sistema de login sem backend real
seria só complexidade sem benefício. A autenticação de verdade (JWT, Spring
Security) já está prevista no roadmap na Phase 13, junto da camada de
segurança, quando o backend existir.

### Requisito para o MVP
- Bloqueio de app local via PIN ou biometria (Android Keystore / BiometricPrompt)
- Não requer conta nem servidor — apenas impede que alguém com acesso físico
  ao dispositivo abra o app sem autorização
- É uma melhoria real sobre o bloco de notas, que hoje não tem proteção alguma
- Configurável nas configurações do app (ativar/desativar, trocar PIN)

---

## 21. Carteiras com composição mista (renda fixa e renda variável) — pós-MVP

### Conceito
O objetivo de uma carteira (prazo, necessidade de liquidez) é o que define
quais classes de ativo fazem sentido nela — não o contrário. Carteiras de
curto/médio prazo (ex: objetivo de comprar algo em 2 anos, reserva de
emergência) exigem liquidez e por natureza só fazem sentido com renda fixa.
Carteiras de longo prazo (ex: aposentadoria) podem conter renda variável.

### Requisitos (pós-MVP, depende do módulo de renda variável)
- Uma Carteira pode conter tanto aportes de renda fixa quanto ativos de renda
  variável simultaneamente — não são domínios separados
- Um ativo de renda variável (ação, FII, cripto) pertence a exatamente uma
  Carteira, não fica solto/genérico
- Suporte a múltiplas Carteiras com propósito conceitualmente parecido mas
  rastreadas de forma independente (ex: uma carteira de longo prazo pessoal
  e uma carteira de acompanhamento/estudo separada), cada uma com sua própria
  composição e evolução
- A Carteira exibe composição percentual entre classes de ativo (base para
  gráfico de pizza/donut: % renda fixa vs. % renda variável, e dentro de
  renda variável, % ações vs. % FIIs vs. outros)
- Evolução de patrimônio rastreada por Carteira individualmente, não só
  no agregado total

### Breakdown por instituição dentro de uma carteira específica
Complementar à visão consolidada por instituição (que atravessa todas as
carteiras): dentro de uma única Carteira, mostrar quanto do seu total está
custodiado em cada instituição (ex: dentro da carteira "Aposentadoria", X%
na Corretora A, Y% no Banco B). É a mesma informação vista pela direção
oposta — de "quanto tenho na instituição A no total" para "como esta
carteira específica se distribui entre instituições".

Dentro da carteira, os aportes/ativos devem ser **agrupados visualmente por
instituição** (não apresentados como lista plana única) — cada grupo exibindo
o percentual que aquela instituição representa do total da carteira. A
intenção é permitir que o usuário identifique rapidamente, dentro de uma
carteira, onde cada parte do dinheiro está guardada. (A forma exata de
apresentação — ícones, lista expansível — é sugestão de UI, ver documento
próprio; aqui o requisito é apenas que o agrupamento e o percentual por
instituição existam como dado exposto ao usuário.)

### Carteira e instituição custodiante são dimensões independentes
Uma Carteira (objetivo) e a instituição financeira onde um aporte está
custodiado não são a mesma coisa — são dimensões ortogonais. A mesma Carteira
(ex: "Aposentadoria") pode ter ações custodiadas na corretora A, Tesouro
Direto custodiado no banco B, e um CDB custodiado no banco C. Da mesma forma,
a mesma instituição pode aparecer em várias carteiras diferentes. O modelo de
dados não deve acoplar carteira e instituição — cada aporte referencia sua
Carteira e sua instituição custodiante de forma independente.

### Tela principal de patrimônio consolidado (ponto de entrada)

**Motivação:** hoje o usuário não tem, em lugar nenhum, a visão real do
patrimônio total consolidado — o dinheiro está espalhado em múltiplas
instituições e o usuário precisa abrir cada app bancário separadamente e
somar mentalmente, sem nunca ver o número real e completo de uma vez. Ver
progresso patrimonial consolidado é um dos fatores que mais sustentam
disciplina e motivação em finanças pessoais a longo prazo — é feedback
concreto de que o esforço está funcionando. Dentro do módulo de
investimentos, essa é provavelmente a tela de maior impacto para o usuário,
não apenas mais uma visão entre outras.

O módulo de investimentos precisa de uma tela principal que sirva como ponto
de entrada, antes de navegar para qualquer carteira específica:

- Valor total do patrimônio, somando todas as Carteiras
- **Toggle para incluir ou excluir carteiras de terceiros** do total exibido
  — por padrão deveria mostrar só o patrimônio próprio, com opção de ver o
  consolidado incluindo o que é administrado para terceiros
- Gráfico de composição do total (pizza/donut) mostrando de onde vem cada
  percentual do patrimônio — por classe de ativo, por carteira, ou pela
  visão que o usuário escolher
- Capacidade de "desembraçar" (drill-down): a partir do total, navegar para
  o detalhe de uma classe específica, e de lá para o detalhe de uma carteira
  específica, e de lá para os aportes individuais — cada nível mostrando a
  composição percentual daquele recorte

### Granularidade dentro de uma classe de ativo
Além da composição por classe (ex: % renda fixa vs. % renda variável), o
app deve mostrar a composição interna de uma classe — quanto cada ativo
individual representa dentro dela (ex: dentro da carteira de ações, quanto
cada ação específica representa do total: 15%, 8%, etc.). Serve para avaliar
concentração/equilíbrio da carteira, não só a divisão macro entre classes.
Essa granularidade se aplica em cascata em qualquer nível: classe → subclasse
(ex: dentro de renda fixa: Tesouro Selic, CDB, LCI) → ativo individual —
todos usando o mesmo padrão de drill-down descrito acima.

Depende de consulta de preço unitário atualizado dos ativos (via API de
mercado), já que o peso de cada ativo na carteira é calculado por valor
monetário (quantidade × preço atual), não pela quantidade de unidades.

**Nota sobre peso por valor vs. peso por quantidade:** o peso correto para
decisões de rebalanceamento é sempre por **valor monetário** — é o padrão de
teoria de portfólio. Peso por quantidade de unidades (ex: "tenho mais ações
de X do que de Y") não indica concentração de risco real, porque o preço
unitário varia muito entre ativos diferentes — é uma métrica de curiosidade,
não de decisão financeira. Documentado aqui como possível visão secundária
de baixa prioridade, não como requisito funcional com o mesmo peso da visão
por valor.

### Visões consolidadas cruzando carteiras
Além da visão por Carteira individual, o app deve suportar visões agregadas
que atravessam várias carteiras:

- **Consolidado por instituição financeira** — todo o patrimônio agrupado
  por onde está custodiado, atravessando todas as Carteiras (ex: "quanto
  tenho na Rico" somando aportes de várias carteiras diferentes que têm
  ativos ali)
- **Consolidado por característica do ativo** — ex: "tudo em renda fixa",
  "tudo em dólar/exterior", "tudo em renda variável" — agrupando aportes de
  todas as carteiras que compartilham essa característica
- **Consolidado por produto financeiro específico** — ex: "todos os aportes
  que existem dentro deste produto financeiro específico (mesmo CDB, mesma
  instituição)", independente de qual carteira cada aporte pertence

O usuário deve poder escolher entre essas visualizações (toggle/seletor de
visualização), sem que uma substitua a outra — são lentes diferentes sobre o
mesmo dado.

A visão por produto específico resolve um problema prático real: muitas
instituições não permitem rotular ou nomear individualmente cada aporte
dentro do mesmo produto — na tela do banco, vários depósitos do mesmo CDB
aparecem misturados sem identificação, e o usuário precisa cruzar valor +
data de aplicação (e às vezes vencimento) para saber a qual carteira cada um
pertence. No app, cada aporte já nasce vinculado à sua carteira de origem —
essa visão consolidada por produto funciona como um espelho da tela confusa
do banco, mas já com a identificação de carteira resolvida, útil para
conferência contra o extrato real.

---

## 22. Estratégia de dados offline/online — pós-MVP (depende da Phase 12 do roadmap)

### Contexto
No MVP tudo é local (Room), offline-only. Quando o backend e a sincronização
existirem, os dados passam a viver na nuvem, mas o usuário não deve perder
a capacidade de acessá-los offline — de forma similar a "tornar disponível
offline" no Google Drive.

### Dois domínios de dados com granularidade diferente
- **Carteiras** — entidades persistentes, volume pequeno, consultadas o tempo
  todo. Recomendação: manter sempre disponíveis localmente (estado atual,
  saldo, aportes), independente de escolha do usuário. É dado de referência.
- **Períodos** (meses/quinzenas) — histórico transacional que cresce
  indefinidamente. Aqui faz sentido o usuário escolher quais períodos
  manter disponíveis offline, período a período.

### Problema de consistência identificado
Um período pode referenciar aportes específicos de carteiras (resgates,
composições de gasto). Se o usuário torna um período disponível offline, mas
ele referencia um aporte de uma carteira, esse aporte também precisa estar
disponível localmente — senão a tela fica incompleta. Tornar um período
disponível offline precisa **cascatear** para garantir que os aportes
referenciados por ele também estejam com dados locais.

### Limpeza de cache
- Limpar o cache de um período específico ou de tudo remove apenas a cópia
  **local** (dispositivo) — nunca apaga dados da nuvem, é só liberar espaço
  no aparelho. O dado continua acessível puxando de volta do servidor

### Exclusão permanente de dados: decisão de escopo
Exclusão de um **período inteiro** não deve ser uma ação disponível no app.
O efeito em cascata é grande demais — outros períodos referenciam o saldo
remanescente, carteiras têm aportes vinculados a itens desse período — e o
risco de inconsistência supera o benefício. Não vale abrir essa porta.

Excluir **itens simples e isolados** dentro de um período (um gasto pontual
sem composição, uma compra de crédito simples) pode ser permitido — o impacto
é baixo e não gera cascata real. A lista exata do que pode ou não ser
excluído precisa de análise própria quando o design entrar em detalhe —
registrado aqui como direção, não como decisão fechada.

### Nota
Requer definição mais aprofundada quando a camada de sincronização (Phase 12
do roadmap) for desenhada. Registrado aqui para não perder o raciocínio.

---

## Escopo do MVP (Phase 6 do Roadmap)

O MVP precisa cobrir o suficiente para o usuário fechar o mês sem abrir o
bloco de notas. Com base nas notas reais, o mínimo necessário é:

**Obrigatório no MVP:**
- Criação e navegação de meses com suporte a dois períodos (quinzenal)
- Múltiplas fontes de entrada por período
- Fluxo de subtração com recálculo automático de saldo
- Estado por item (pendente/realizado) com checkbox
- Campo de nota por item (texto livre)
- Estado "ignorado" por item (não entra no cálculo)
- Painel analítico automático do período (totais por categoria)
- Categorias básicas de item (gasto, investimento, acúmulo, fatura, transferência)
- Gastos recorrentes como sugestão automática no novo período
- Controle de saldo das contas secundárias
- Carteiras com aportes individuais, datas e resgates
- Composição de fatura (múltiplas fontes)
- Persistência local (Room), offline-only
- Bloqueio de app por PIN ou biometria (sem conta/login)

**Deixar para versões seguintes:**
- Notificações
- Múltiplos cartões com fechamento/vencimento automático
- Parcelamento automático de crédito
- Suporte completo a créditos/débitos mistos em fatura por estorno (caso raro)
- Gráficos e análise
- Exportação PDF
- Carteiras de terceiros como feature formal
- Sincronização com backend
