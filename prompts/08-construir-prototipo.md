# Construir o protótipo

Passo final da sequência. Você chega aqui com: os requisitos consolidados
(`docs/analise-requisitos.md`), as decisões de UI para os pontos que
estavam abertos, confirmadas ou ajustadas pelo role-play
(`docs/discovery/05-conclusoes-roleplay.md`), e as suas próprias ideias de
tela já registradas antes deste processo (`reference-files/sugestoes-ui-navegacao.md`).

Diferente do treinamento original (uma única PoC em HTML/CSS/JS puro), aqui
vale gerar **duas versões comparáveis**: uma deixando a IA livre para
desenhar a tela só com base nos requisitos, outra usando
`sugestoes-ui-navegacao.md` como influência direta.

> **Atenção com dados de exemplo.** Diferente da persona (markdown, não
> versionado), o protótipo é código que você pretende commitar e mostrar
> como portfólio — dado financeiro real seu ali seria exposto
> publicamente, sem chance fácil de reverter depois. Toda instrução deste
> prompt que pede "dados de exemplo realistas" significa fictícios mas
> plausíveis (nomes de categoria genéricos como "Internet", "Aluguel",
> "Mercado"; valores redondos ou aleatórios dentro de uma faixa razoável)
> — nunca copiados ou inspirados em valores, nomes de caixinha, ou
> instituições específicas mencionados nesta conversa, no
> `analise-requisitos.md`, ou em qualquer arquivo de `reference-files/`.
> Para não repetir esse risco em dois lugares, os dados são gerados **uma
> única vez**, como módulo compartilhado (ver "Arquitetura compartilhada"
> abaixo) — a Versão B importa os mesmos dados da Versão A, não gera os
> próprios.
>
> Os dados fictícios são gerados **sem nenhuma consulta** às anotações
> reais em `reference-files/gastos*`. Não é "disfarçar" um dado real — é
> nunca ler o dado real para gerar o fictício. Isso não é só cautela:
> disfarçar dado real preservando estrutura (proporção entre categorias,
> quantidade de itens, valores recorrentes) tem risco real de
> reidentificação mesmo depois de trocar nomes e valores — anonimização
> segura é disciplina técnica própria, não algo garantido por uma
> instrução informal de prompt. O objetivo do protótipo é testar
> estrutura de tela e fluxo de interação, não fidelidade ao seu perfil
> financeiro real — um dado genérico plausível cumpre esse objetivo
> igualmente bem, sem o risco.

> **Sobre a tecnologia:** Vite + React (sem TypeScript, sem persistência),
> Tailwind para estilização. Ver justificativa completa na conversa que
> originou este prompt — resumo: o domínio tem estado bem mais
> interdependente do que um protótipo típico de workshop (cascata de
> subtração com invariante de checkbox recalculado a cada reorder, saldo
> recalculado a cada item, painel analítico derivado de todos os itens do
> fluxo), e um framework reativo com componentes separados por arquivo
> evita que o protótipo vire um arquivo monolítico difícil de editar.

---

## Por que isto é feito em Spec, e por que em duas sessões

Rode esta etapa em sessão **Spec**, não Vibe — o volume de regras de
interação específicas (seções 3, 4, 7, 9-10 do documento de requisitos) tem
mais chance de ficar consistente com um `design.md` revisável antes do
código e `tasks.md` incrementais do que com um prompt único gerando tudo de
uma vez.

**Estrutura de arquivos: uma spec só, não duas.** `requirements.md`,
`design.md` e `tasks.md` são únicos — cobrem as duas versões dentro do mesmo
conjunto de arquivos, evitando duplicar documentação que seria quase
idêntica entre A e B.

**Mas a execução acontece em duas sessões de chat separadas, nunca uma
só.** O motivo é contaminação de contexto: uma vez que
`sugestoes-ui-navegacao.md` é lido numa conversa, o conteúdo fica na memória
daquela sessão e pode influenciar sutilmente qualquer coisa gerada depois
nela — mesmo que a seção da Versão A no `design.md` já esteja escrita e
"fechada" no arquivo. Isolamento real exige que a Versão A seja decidida e
implementada inteira numa sessão que **nunca** leu esse arquivo.

Mapa de quem gera o quê, em qual sessão:

| Artefato | Conteúdo | Sessão |
|---|---|---|
| `requirements.md` | Requisitos funcionais (compartilhados — idênticos para A e B, não fala de layout) | 1 |
| `design.md` — seção "Arquitetura compartilhada" | Componentes, fluxo de estado, stack | 1 |
| `design.md` — seção "Decisões de layout — Versão A" | Layout sem influência externa | 1 |
| `tasks.md` — setup + tarefas da Versão A | Scaffold do projeto + implementação A | 1 |
| *(execução das tarefas acima)* | Versão A construída e revisada | 1 |
| `design.md` — seção "Decisões de layout — Versão B" | Layout influenciado pelas sugestões | **2 (chat novo)** |
| `tasks.md` — tarefas da Versão B (adicionadas ao arquivo existente) | Implementação B | 2 |
| *(execução das tarefas acima)* | Versão B construída e revisada | 2 |
| `prototype/COMPARACAO.md` | Comparação entre A e B | 2 (ou nova sessão — sem risco, ambas já estão em código) |

**Sobre o fluxo nativo de botões da interface de Spec do Kiro ("Continue" /
"Generate design" / "Generate tasks"):** cada clique nesses botões abre um
chat novo, sem memória da conversa anterior — só lê os arquivos da spec já
salvos em disco. Isso é compatível com a Sessão 1: como nenhum dos prompts
1.1/1.2/1.3 menciona `sugestoes-ui-navegacao.md`, pode usar o fluxo nativo
normalmente (clicar em "Generate design", colar o prompt do Passo 1.2 no
chat que abrir, e assim por diante) — o isolamento se mantém, porque
nenhuma das janelas envolvidas toca nesse arquivo.

Para a **Sessão 2**, que não é geração inicial (é edição incremental de um
`design.md`/`tasks.md` que já existem, adicionando só a seção da Versão B),
não há confirmação de como o fluxo nativo de botões se comporta nesse
cenário de atualização — abra um chat novo manualmente e cole o prompt da
Sessão 2 diretamente, em vez de depender do botão "Continue" da spec.

Requisitos funcionais não precisam dessa separação por sessão — são
idênticos entre A e B e não têm relação com `sugestoes-ui-navegacao.md`, daí
`requirements.md` inteiro sair pronto na sessão 1, sem risco.

---

## Sessão 1 — Requirements, design (compartilhado + Versão A), tasks (setup + Versão A)

### Passo 1.1 — Iniciar a spec e gerar requirements.md

```
Quero criar uma spec para um protótipo navegável (React) do meu app pessoal
de controle financeiro.

Regra válida para toda esta sessão, do início ao fim — não só para este
prompt: NUNCA leia, abra ou referencie
reference-files/sugestoes-ui-navegacao.md em nenhum momento desta sessão —
não no requirements, não no design, não nas tasks, não durante a execução,
nem mesmo "só para checar" ou "só para ter uma ideia". Esse arquivo é
reservado exclusivamente para uma sessão futura, dedicada à Versão B. Se em
algum momento desta sessão parecer útil consultá-lo, isso é exatamente o
sinal de que não deve ser feito.

Antes de gerar requirements.md, leia:

- docs/analise-requisitos.md (documento completo de requisitos do produto)
- docs/discovery/05-conclusoes-roleplay.md (decisões de UI confirmadas/ajustadas)
- docs/discovery/04-alternativas-ui.md (decisões recomendadas, para pontos
  que não passaram pelo role-play)

O protótipo precisa representar o fluxo central do app: navegação de
período (mês/quinzena), fluxo de subtração em cascata com estado de
checkbox e reorder, painel analítico do período, e pelo menos uma carteira
com aportes.

Gere requirements.md cobrindo o que o protótipo precisa fazer
funcionalmente. NÃO inclua nada sobre layout, disposição visual ou
aparência de tela nesta etapa — isso é decisão de design, não requisito
funcional, e ainda não deve ser abordado. NÃO leia
reference-files/sugestoes-ui-navegacao.md nesta sessão, em nenhum momento
— vou pedir isso explicitamente numa sessão futura, para a segunda versão
do protótipo.
```

Revise o `requirements.md` gerado antes de seguir. Só avance para o design
depois de aprovar.

### Passo 1.2 — Gerar design.md: arquitetura compartilhada + Versão A

```
Com requirements.md aprovado, gere design.md em duas partes:

1. Uma seção "Arquitetura compartilhada": os componentes React que serão
   usados, como o estado flui entre eles (ex.: onde vive a lista de itens
   do fluxo, como o saldo em cascata é recalculado ao reordenar ou marcar/
   desmarcar, como o invariante de prefixo contíguo do checkbox é mantido),
   e a stack (Vite + React sem TypeScript, Tailwind, sem persistência).

   Inclua também um componente seletor na raiz do app (ex.: App.jsx com
   estado local simples, sem biblioteca de rotas): uma tela inicial com
   duas opções ("Ver Versão A" / "Ver Versão B"), que renderiza a árvore de
   componentes da versão escolhida. Um único `npm run dev` deve servir as
   duas versões através desse seletor — não dois servidores separados.
   Nesta sessão, a opção "Versão B" pode existir desabilitada ou apontando
   para um placeholder, já que essa versão ainda não existe.

   Envolva o conteúdo (tanto o seletor quanto qualquer versão renderizada)
   num container fixo simulando a proporção de tela de um celular Android
   (largura de referência ~390px, altura ~ viewport de um celular comum,
   centralizado na página, com borda/sombra visual imitando uma moldura de
   celular). Isso deve ficar sempre visível assim ao abrir `npm run dev`,
   sem depender de abrir o modo responsivo do navegador (F12) para ver a
   proporção correta.

   Defina também a estrutura de **dados e cálculo compartilhados**, em
   prototype/src/shared/ (fora das pastas versao-a-livre/ e
   versao-b-influenciada/, para não duplicar entre as duas versões):
   - mockData.js — módulo JS (não .json puro, para poder comentar e não
     ter limitação de sintaxe) exportando os dados fictícios do protótipo
     (períodos, itens do fluxo, carteiras, aportes), fictícios mas
     plausíveis, seguindo a regra de dados de exemplo do início deste
     documento. NÃO leia nem consulte reference-files/gastos* para gerar
     esses dados — são inteiramente inventados, sem nenhuma base em dado
     real, mesmo disfarçado ou anonimizado
   - calculos.js — funções puras que ambas as versões vão importar e usar
     igualmente: recálculo do saldo em cascata, manutenção do invariante
     de prefixo contíguo do checkbox (seção 3 do analise-requisitos.md),
     e qualquer outra regra de negócio que não deva variar entre A e B —
     só a apresentação varia entre as versões, não o comportamento
     subjacente

   Descreva no design.md a forma exata dos dados (estrutura de cada
   objeto exportado por mockData.js), para que a Versão B, gerada numa
   sessão futura, importe exatamente a mesma estrutura sem reinterpretar
   ou duplicar.

   Os dados fictícios precisam ser suficientes para testar a interface de
   verdade, não apenas genéricos. "Genérico" (fictício, não meu dado real)
   e "suficiente" (cobre os cenários abaixo) são requisitos independentes
   — uma lista de 3 itens soltos seria genérica, mas insuficiente para
   perceber problema real de UI. Garanta que os dados cubram, no mínimo:
   - **Navegação entre meses, cobrindo modo mensal e quinzenal, e
     diferentes graus de estado vazio** — não deixe a IA decidir livremente
     entre "meses ou quinzenas"; gere exatamente estes 4 meses distintos
     (nenhum repetido — cada um testa algo que os outros não testam),
     para cobrir tanto a alternância de modo (seção 1 do
     analise-requisitos.md) quanto diferentes formas de "vazio":
     1. Um mês no **modo mensal** (um único período), com 8-12 itens
        cobrindo pelo menos 4 categorias diferentes das listadas na
        seção 5 (gasto fixo, gasto variável, investimento, acúmulo,
        fatura, transferência) — não "itens normais" genéricos, itens
        com descrição concreta (ex: "Aluguel", "Internet", "Aporte CDB",
        "Fatura cartão")
     2. Um mês no **modo quinzenal**, com os dois períodos (1ª e 2ª
        quinzena) completos, cada um com 5-8 itens cobrindo categorias
        diferentes entre si (não repita a mesma combinação do mês 1)
     3. Um mês no **modo quinzenal**, com a 1ª quinzena tendo só 1-2
        itens (dado escasso, não vazio) e a 2ª quinzena **sem nenhum
        item** (como se o período tivesse sido criado automaticamente,
        mas o usuário ainda não preencheu nada) — um único mês cobrindo
        os dois graus de "quase vazio" ao mesmo tempo
     4. Um mês **futuro**, sem nenhum período criado ainda — só existe
        como próxima posição no carrossel de navegação, nada preenchido
   - **Um período com lista longa de itens no fluxo** (15+ itens), em
     algum dos meses acima, para testar comportamento de rolagem e se a
     hierarquia visual do saldo em cascata se mantém legível numa lista
     extensa — não só num exemplo curto e confortável
   - **Itens com estado pendente e realizado misturados**, respeitando o
     invariante de prefixo contíguo (seção 3), para testar o checkbox em
     cascata de verdade, não só itens todos no mesmo estado
   - **Pelo menos uma carteira com múltiplos aportes** (3+), incluindo
     aportes com datas e rendimentos diferentes, para testar a listagem
     de aportes dentro de uma carteira, não só uma carteira vazia ou com
     um único aporte
   - **Pelo menos um item abaixo de R$ 20 e um item acima de R$ 3.000**
     dentro da mesma categoria (ex: uma compra pequena e uma fatura alta),
     para testar se a coluna de valor se mantém legível com números de
     ordens de grandeza bem diferentes — não só valores próximos entre si
   - **Ao menos uma fatura com múltiplas fontes de composição** (seção
     9-10), para testar a explicação de gasto composta, não só itens
     simples sem composição
   - **Todas as categorias da seção 5 representadas em algum item, em
     algum mês** (gasto fixo, gasto variável, investimento, acúmulo,
     fatura, transferência entre contas, custódia de terceiros) — não é
     necessário estar todas no mesmo mês, mas nenhuma categoria deve
     ficar totalmente ausente de todo o conjunto de dados

   Se, ao longo da implementação, você perceber que uma tela específica
   precisaria de outro cenário de dado para ser testada de verdade (ex:
   uma tela que só faz sentido com muitas carteiras cadastradas), sinalize
   isso e adicione ao mockData.js — a lista acima é o mínimo, não o teto.

2. Uma seção "Decisões de layout — Versão A": como cada tela deve ser
   disposta visualmente, decidido só a partir dos requisitos e das decisões
   em docs/discovery/ — sem nenhuma influência externa de layout.

   IMPORTANTE: não leia, não abra e não referencie
   reference-files/sugestoes-ui-navegacao.md nesta etapa nem em nenhuma
   outra desta sessão. Isso vale para o restante desta sessão inteira, não
   só para este passo específico — inclusive durante a execução das tasks
   mais adiante.

Princípios para a Versão A:
- Hierarquia visual clara: o saldo em cascata (seção 3 do
  docs/analise-requisitos.md) é o elemento central, não deve ficar escondido
- Fluxo intuitivo, poucos cliques até o valor principal
- Feedback visual em toda interação (estados vazio, com dados, erro)
- Sinal +/- sempre explícito junto ao valor, nunca só cor (requisito de
  acessibilidade não negociável, seção 3)
- Consistência visual do início ao fim
```

Revise antes de seguir.

### Passo 1.3 — Gerar tasks.md: setup + implementação da Versão A

```
Gere tasks.md cobrindo:

1. Setup do projeto: inicializar Vite + React (JS, sem TypeScript),
   configurar Tailwind, estruturar pastas de forma que a Versão A e uma
   futura Versão B (ainda não existente) fiquem em diretórios separados
   dentro de prototype/src/ (ex.: versao-a-livre/, versao-b-influenciada/),
   sem misturar componentes de uma com os da outra. Inclua o componente
   seletor na raiz (App.jsx), com a opção "Versão A" funcional e "Versão B"
   desabilitada/placeholder por enquanto.
2. Criação de prototype/src/shared/mockData.js e
   prototype/src/shared/calculos.js, seguindo a estrutura descrita em
   design.md. Dados fictícios mas plausíveis (não "lorem ipsum", mas
   também nunca meus valores ou nomes reais — nada copiado ou inspirado
   em qualquer número, nome de carteira/caixinha ou instituição
   mencionados no analise-requisitos.md, nesta conversa, ou em qualquer
   arquivo de reference-files/). Esta tarefa vem antes das tarefas de
   tela, já que elas vão consumir esses dados.
3. Implementação de cada tela da Versão A como tarefas incrementais
   (ex.: navegação de período → fluxo de cascata → painel analítico →
   carteira), seguindo design.md e importando de
   prototype/src/shared/mockData.js e prototype/src/shared/calculos.js —
   não gere dados nem lógica de cálculo dentro da pasta versao-a-livre/.
4. Uma tarefa final de revisão: registrar em
   prototype/src/versao-a-livre/NOTAS.md o que foi assumido ou
   simplificado por falta de informação, e qualquer decisão de
   docs/discovery/ que não foi bem representada.
```

Execute as tarefas até a Versão A estar completa, sem em nenhum momento
abrir `reference-files/sugestoes-ui-navegacao.md` nesta sessão — nem antes,
durante ou depois da execução. Ao terminar, encerre o chat.

> **Acessar pelo celular real (opcional):** para testar no seu próprio
> celular em vez de só no navegador do PC, rode `npm run dev -- --host`
> (ou configure `server.host: true` no `vite.config.js`). O terminal vai
> mostrar um endereço `Network: http://<seu-ip-local>:5173/` — acesse esse
> endereço no navegador do celular, com celular e PC na mesma rede Wi-Fi.
> Se não conectar de primeira, verifique se o firewall do Windows não
> bloqueou a porta. Útil para comparar a proporção do "phone frame"
> simulado no desktop com a tela real. Vale para as duas versões, já que o
> servidor é compartilhado entre elas.

---

## Sessão 2 (chat novo) — design.md: Versão B, tasks.md: Versão B

Abra uma conversa nova. Não reaproveite a sessão 1.

```
Estou continuando a spec do protótipo do app de controle financeiro. Leia
requirements.md e design.md já existentes (têm a arquitetura compartilhada
e a seção "Decisões de layout — Versão A", decididas numa sessão anterior).

A seção "Decisões de layout — Versão A" já está finalizada e não deve ser
alterada, revisada ou usada como base de decisão para o que vem agora — ela
existe só como contexto de que já foi implementada, não como referência de
estilo a seguir ou evitar.

Agora leia também reference-files/sugestoes-ui-navegacao.md — são minhas
próprias ideias de tela e navegação, registradas antes deste processo.

Adicione a design.md uma nova seção "Decisões de layout — Versão B",
**sem modificar nenhuma outra seção do arquivo**: como cada tela seria
disposta usando as sugestões de reference-files/sugestoes-ui-navegacao.md
como influência direta, sempre que não contradizerem uma decisão já tomada
em docs/discovery/. Mesmos princípios de hierarquia, feedback,
acessibilidade e consistência já usados na Versão A (mas decida o layout de
cada tela de forma independente, sem replicar a solução específica da
Versão A por padrão).

Antes de gerar as novas tarefas, releia a lista completa e já existente de
tarefas da Versão A em tasks.md, tarefa por tarefa. Use essa lista **apenas
como checklist estrutural de cobertura** — garantir que toda tela/
funcionalidade que a Versão A tem uma tarefa dedicada também tenha uma
tarefa equivalente na Versão B, na mesma granularidade, sem nada esquecido.
Não confie na descrição abstrata do padrão ("navegação → cascata → painel
→ carteira") como referência suficiente para a cobertura — use a lista
real de tarefas de A para isso.

Isso é só sobre COBERTURA (quais telas/partes precisam de tarefa), nunca
sobre CONTEÚDO. O conteúdo de cada tarefa da Versão B — como aquela tela
específica deve ser implementada — vem exclusivamente da seção "Decisões
de layout — Versão B" do design.md (que já reflete
reference-files/sugestoes-ui-navegacao.md), nunca da forma como a tarefa
equivalente da Versão A foi implementada. As decisões de layout da Versão B
sempre têm precedência sobre qualquer semelhança estrutural com a Versão A
— só a lista de "quais tarefas existem" é espelhada, o "como fazer" de
cada uma é independente e específico da Versão B.

Depois de garantir essa paridade, adicione a tasks.md **apenas** as
tarefas novas de implementação da Versão B, em
prototype/src/versao-b-influenciada/, sem modificar ou marcar como
concluídas as tarefas já existentes da Versão A. Inclua também uma tarefa
para habilitar a opção "Versão B" no componente seletor (App.jsx),
apontando para os componentes recém-criados em vez do placeholder.

Os dados fictícios e a lógica de cálculo já existem em
prototype/src/shared/ (mockData.js, calculos.js), criados na Sessão 1 — a
Versão B importa exatamente os mesmos, não gera dados próprios nem
duplica lógica de cálculo. Isso também significa que os dados fictícios
não são influenciados por reference-files/sugestoes-ui-navegacao.md, já
que foram criados antes de esse arquivo ser lido — só o layout é
influenciado por ele, nunca o dado.
```

Execute as tarefas até a Versão B estar completa.

---

## Comparação final

Pode ser feita na sessão 2 (ou uma nova) — sem risco de contaminação, já
que as duas versões já existem em código, não é mais uma decisão a tomar.

```
Terminei as duas versões do protótipo (prototype/src/versao-a-livre/ e
prototype/src/versao-b-influenciada/). Me ajude a comparar:

1. Onde as duas versões resolveram a mesma tela de forma visivelmente
   diferente?
2. Qual versão representa melhor a hierarquia do saldo em cascata (o
   elemento mais importante, segundo docs/analise-requisitos.md seção 3)?
3. Existe uma terceira opção óbvia que combina o melhor das duas?

Salve a comparação em prototype/COMPARACAO.md.
```

---

**Verificação de conclusão:** você tem uma spec com `requirements.md` único,
`design.md` com três seções (arquitetura compartilhada, layout Versão A,
layout Versão B) e `tasks.md` único cobrindo as tarefas de ambas. O projeto
React roda as duas versões (`npm run dev`), cada uma com seu `NOTAS.md`, e
`prototype/COMPARACAO.md` ajuda a decidir qual caminho de design levar para
a implementação Android real.
