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

### 1. Layout do quadro de resumo por categoria e posição da linha de Total no Crédito
**Origem:** Seção 4 — "Snapshot de 'Previsto Inicial'" e "Total no Crédito
não soma no Total Geral do período"

**Resumo neutro (para prompts que geram alternativa de UI, ex: `06`):** o
quadro de resumo do período precisa exibir, para cada categoria (Gasto,
Acúmulo, Investimento, Fatura), três valores — Planejado inicialmente,
Realizado e Previsto (vivo) — além de uma linha de Total Geral somando as
quatro categorias. O Total no Crédito também precisa aparecer no quadro,
mas não soma no Total Geral (é dado informativo, à parte). Nem a ordem dos
três valores dentro de cada linha, nem a disposição geral da tabela, nem a
posição/destaque visual da linha de Crédito estão decididos.

**Contexto completo:** o quadro de resumo pode exibir `Planejado
inicialmente (snapshot) | Realizado | Previsto (vivo, atual)` por
categoria. O documento sugere uma ordem (Realizado antes de Previsto) mas
deixa explícito: "ajustável livremente no design de UI conforme
preferência de leitura". Sobre o Total no Crédito, o documento não fixa
nenhuma disposição, só cita como exemplo "destacado da tabela principal"
ao afirmar que a forma de separá-lo visualmente "é decisão de design de
UI, não desta seção".

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

### 12. Visualizar as compras de uma fatura específica, não só suas fontes
**Origem:** Seção 9-10 — "Visualizar as compras de uma fatura específica,
não só suas fontes (ponto em aberto)"

**Resumo neutro (para prompts que geram alternativa de UI, ex: `06`):** a
Explicação de Gasto de uma fatura já tem alternância entre Visão detalhada
e Visão agrupada, mas ambas mostram apenas as fontes de pagamento (de onde
vem o dinheiro). Não está decidido se, dentro da mesma tela, deveria
existir uma segunda alternância para ver as compras que geraram aquele
total (o que foi comprado, não de onde vem o dinheiro).

**Contexto completo:** "Ainda não está decidido se, dentro dessa mesma
tela, também deveria existir uma alternância para ver as **compras** que
geraram aquele total... um segundo eixo de visão (Fontes vs. Compras),
específico da Explicação de Gasto do tipo Fatura, complementar à tela
dedicada de cartões (pós-MVP...) e à tela de movimentações... Registrado
como direção a considerar quando o design de telas da composição de
fatura for detalhado — não decidido."

### 13. Extensões pós-MVP da tela consolidada de movimentações
**Origem:** Seção 4 — "Tela consolidada de movimentações (MVP: drill-down
simples; demais pontos de acesso e filtros, pós-MVP)"

**Resumo neutro (para prompts que geram alternativa de UI, ex: `06`):** o
MVP cobre apenas abrir a lista de movimentações via drill-down de um total
do resumo, já filtrada, sem controles visíveis. Não está decidido como (ou
se) a mesma tela ganha, depois do MVP, outros pontos de acesso (ver Fluxo
específico, ver Período inteiro, ver Mês inteiro, acesso fora de qualquer
Período) e um painel de filtro explícito combinando categoria, tag e
intervalo de data.

**Contexto completo:** o documento lista essas extensões explicitamente
como "registradas para não se perder (nenhuma decidida como prioridade,
apenas direção)". Inclui também uma decisão de modelo ainda adiada sobre
como o filtro por data deveria funcionar (filtrar pela data individual da
movimentação vs. filtrar pelos Períodos cujo intervalo cruza a data
escolhida) — a preferência pelo primeiro cenário está registrada, mas
condicionada a mudanças de UX ainda não desenhadas.

### 14. Layout do painel analítico consolidado do Mês
**Origem:** Seção 4 — "Painel analítico consolidado do Mês (MVP)"

**Resumo neutro (para prompts que geram alternativa de UI, ex: `06`):**
quando um Mês tem dois Períodos (modo quinzenal), o app precisa apresentar
um painel consolidado somando os totais de ambos, além dos painéis
individuais de cada Período. Não está decidido como esse painel se
organiza visualmente em relação aos dois painéis de Período (lado a lado,
um abaixo do outro, tela própria) nem como a soma consolidada e a linha de
Total no Crédito do mês (que, como no Período, não soma no Total Geral)
são destacadas dentro dele.

**Contexto completo:** o requisito só define o cálculo (soma direta das
células correspondentes de cada Período) e que o painel tem drill-down
para a lista de movimentações do mês inteiro — a apresentação (layout,
onde acessar esse painel a partir da navegação por Mês/Período) é
explicitamente deixada para o design de UI.

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

### Definição de "parcela futura" para o cálculo de "Total em Parcelamentos Futuros"
**Origem:** `analise-requisitos.md`, Seção 9 — "Entidade própria para
definição do parcelamento e 'Total em Parcelamentos Futuros' (pós-MVP)"

O documento marca explicitamente: "decisão ainda aberta, a resolver quando
esta entidade for desenhada: o que conta como 'parcela futura' — parcela
com data de vencimento ainda não alcançada, ou parcela cujo Item ainda
está com estado `pendente`?". Não é decisão de apresentação em tela, é
regra de negócio sobre qual critério o cálculo derivado deve usar — por
isso não entra na lista principal, mas fica registrado aqui para não se
perder.
