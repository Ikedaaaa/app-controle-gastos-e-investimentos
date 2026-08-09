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
2. Implementação de cada tela da Versão A como tarefas incrementais
   (ex.: navegação de período → fluxo de cascata → painel analítico →
   carteira), seguindo design.md.
3. Uma tarefa final de revisão: simular dados de exemplo realistas (não
   "lorem ipsum"), e registrar em prototype/src/versao-a-livre/NOTAS.md o
   que foi assumido ou simplificado por falta de informação, e qualquer
   decisão de docs/discovery/ que não foi bem representada.
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
