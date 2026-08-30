# Pontos abertos — decisões de UI/UX pendentes

Levantamento produzido a partir da leitura de `docs/analise-requisitos.md` e
`docs/sugestoes-ui-navegacao.md` (prompt `01-setup.md`). Lista apenas pontos
explicitamente sinalizados nos documentos como "a definir no design",
"decisão pendente" ou "problema aberto" — nenhum ponto foi inferido ou
adicionado por iniciativa própria.

Esta lista é o insumo de entrada para os prompts `04-problema-e-hmw.md`,
`05-oportunidades-ia.md` e `06-ideacao-e-priorizacao.md`.

---

## Originados em `docs/analise-requisitos.md`

### 1. Ordem de exibição dos três valores no quadro de resumo do período
**Origem:** Seção 4 — "Snapshot de 'Previsto Inicial'"

**Resumo neutro (para prompts que geram alternativa de UI, ex: `06`):** o
quadro de resumo do período precisa exibir três valores — Planejado
inicialmente, Realizado e Previsto (vivo). A ordem/disposição entre eles
está em aberto.

**Contexto completo:** o quadro de resumo pode exibir `Planejado
inicialmente (snapshot) | Realizado | Previsto (vivo, atual)`. O documento
sugere uma ordem (Realizado antes de Previsto) mas deixa explícito:
"ajustável livremente no design de UI conforme preferência de leitura".

### 2. Forma de apresentação do breakdown de totais do período
**Origem:** Seção 4 — quadro de resumo (Total no Crédito, separação
Meu/Terceiro, etc.)

O dado é totalmente derivado e não requer preenchimento manual, mas "a forma
de apresentação na tela (seção fixa, aba própria, popup) é decisão de design
a definir depois — o requisito aqui é o dado, não o layout".

### 3. Visão de navegação por calendário
**Origem:** Seção 4 — "Terceira visão: navegação por calendário (opcional,
baixa prioridade)"

Não requer estrutura de dado nova (a data já existe em cada item). O
documento avalia valor questionável para o padrão de uso real, mas registra
explicitamente: "não descartada, mas sem motivo para priorizar frente a
outras visões".

### 4. Ordenação da lista de carteiras por proximidade de prazo/meta
**Origem:** Seção 7 — "Campos de meta da carteira: prazo e quantia desejada"

Filtro por faixa de valor foi descartado, mas ordenação por proximidade do
prazo ou distância até a meta é apontada como possível ganho de uso: "Sem
decisão fechada sobre implementar a ordenação agora; registrado para o
design de telas."

### 5. Lista do que pode ser excluído dentro de um período
**Origem:** Seção 22 — "Exclusão permanente de dados: decisão de escopo"

Exclusão de período inteiro está descartada. Exclusão de itens simples e
isolados é apontada como de baixo risco, mas "a lista exata do que pode ou
não ser excluído precisa de análise própria quando o design entrar em
detalhe — registrado aqui como direção, não como decisão fechada".

---

## Originados em `docs/sugestoes-ui-navegacao.md`

O documento inteiro é rotulado como sugestões não vinculantes. Os pontos
abaixo têm, além disso, um problema ou alternativa explicitamente deixada
sem escolha no texto (não é só a ressalva genérica do documento).

### 6. Tema e paleta de cores
**Origem:** "Tema e cores"

**Resumo neutro (para prompts que geram alternativa de UI, ex: `06`):** o
app precisa de uma paleta de cores para tema escuro e tema claro. Nenhuma
cor está decidida.

**Contexto completo:** valores hex sugeridos para tema escuro (`#000000`,
`#3CBA59`, `#8300A7`) e claro (branco + azul claro, sem hex definido). "A
definir/revisar quando a identidade visual do app for desenhada."

### 7. Diferenciação visual entre faturas de cartões diferentes
**Origem:** "Ícones genéricos por tipo de gasto"

**Resumo neutro (para prompts que geram alternativa de UI, ex: `06`):**
quando o usuário tem mais de um cartão de crédito, a lista do fluxo precisa
diferenciar visualmente de qual cartão é cada fatura. Nenhuma solução
decidida.

**Contexto completo:** "Ícone de cartão para fatura (com problema aberto:
como diferenciar visualmente a fatura de cartões diferentes — usar dois
ícones combinados?)"

### 8. Prioridade do ícone de compra no crédito: método de pagamento vs. destino
**Origem:** "Ícones genéricos por tipo de gasto"

"Para compras no crédito: decidir se o ícone prioriza o método de pagamento
(cartão) ou o destino da compra (loja) — problema de design ainda aberto nas
anotações originais."

### 9. Tela dedicada por classe de ativo vs. tudo dentro do gráfico de composição
**Origem:** "Tela dedicada por classe de ativo"

**Resumo neutro (para prompts que geram alternativa de UI, ex: `06`):** o
usuário precisa poder ver o detalhe de uma classe de ativo específica (ex:
exterior) dentro da carteira. Não está decidido se isso é uma tela própria,
parte do gráfico de composição, ou outra abordagem.

**Contexto completo:** duas alternativas descritas, nenhuma escolhida:
aba/tela específica por classe de ativo (ex: exterior) vs. tudo dentro do
gráfico de composição da carteira com filtros.

### 10. Tela inicial ao abrir o app
**Origem:** "Tela inicial / o que aparece ao abrir o app"

**Resumo neutro (para prompts que geram alternativa de UI, ex: `06`):** o
que o usuário vê no primeiro instante ao abrir o app não está decidido.

**Contexto completo:** três hipóteses levantadas, nenhuma testada ou
escolhida: abrir direto no Período atual; tela de patrimônio/dashboard
consolidado (provavelmente fora do MVP); ou uma combinação das duas. O
próprio documento aponta este ponto como candidato a entrar na lista de
refinamento.

### 11. Comportamento de gráficos em tela pequena
**Origem:** "Comportamento de gráficos em tela pequena"

"Decisão em aberto: gráfico abaixo do formulário com scroll, ou navegação
para tela dedicada ao gráfico."

---

## Fora da lista de refinamento de UI (registrado, não incluído)

### Modelo de dados de vinculação entre resgate de aporte e fonte de composição
**Origem:** `analise-requisitos.md`, Seção 10 — "Composição com múltiplos
aportes de uma mesma carteira de terceiro"

O documento marca explicitamente como nota para o design: "detalhar o modelo
de dados que representa essa vinculação entre resgate de carteira e item de
fatura ou gasto composto". Não é uma decisão de UI/UX (como apresentar algo
já definido), é modelagem de dados pendente — por isso não entra na lista
principal, mas fica registrado aqui para não se perder.
