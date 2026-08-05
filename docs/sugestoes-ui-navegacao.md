# Sugestões de UI e Navegação (não vinculantes)

Este documento reúne ideias de interface, visual e navegação registradas nas
anotações brutas originais. São **sugestões**, não decisões de design
fechadas. Foram escritas em momentos diferentes, sem revisão a posteriori, e
devem ser reavaliadas quando o design de telas for feito de fato — podem ser
adotadas, adaptadas ou substituídas por algo melhor nessa etapa.

---

## Tema e cores

Ideia original: tema escuro com preto, verde e roxo; tema claro com branco e
azul claro. Valores sugeridos na época:

- Escuro: `#000000` (preto), `#3CBA59` (verde), `#8300A7` (roxo)
- Claro: branco + azul claro (sem valores hex definidos)

A definir/revisar quando a identidade visual do app for desenhada.

---

## Navegação principal

- Menu sanduíche (hambúrguer) como navegação principal, alternando entre
  seção de Gastos e seção de Investimentos
- Configurações fixadas na parte inferior do menu

## Personalização visual de carteiras

- Permitir foto/imagem customizada por carteira
- Alternativa: fotos padrão do sistema, com curadoria melhor do que a
  observada em outros apps (ex: Nubank)

## Ícones por instituição financeira

- Ao cadastrar uma instituição financeira associada a um investimento,
  permitir vincular um ícone/símbolo (como o ícone do app da instituição)

## Ícones genéricos por tipo de gasto

Ideia de sistema de ícones para facilitar reconhecimento visual rápido:
- Gota de água → conta de água
- Raio → conta de luz/eletricidade
- Ícone de boleto/conta genérica
- Ícone de cartão para fatura (com problema aberto: como diferenciar
  visualmente a fatura de cartões diferentes — usar dois ícones combinados?)
- Ícone da loja/marketplace quando o gasto for identificável a um
  estabelecimento específico
- Para compras no crédito: decidir se o ícone prioriza o método de pagamento
  (cartão) ou o destino da compra (loja) — problema de design ainda aberto
  nas anotações originais

## Tela dedicada por classe de ativo

- Considerar uma aba/tela específica por classe de ativo (ex: uma tela só
  para "ativos no exterior") além da visão consolidada da carteira
- Alternativa: tudo dentro do gráfico de composição da carteira, com
  filtros/visualizações diferentes ao invés de telas separadas

## Lista expansível/colapsável por instituição (padrão "acordeão")

Para visualizar investimentos agrupados por instituição financeira (tanto no
consolidado geral quanto dentro de uma carteira específica), considerar um
padrão de lista expansível, similar ao "People also ask" do Google ou uma
seção de FAQ: cada instituição aparece como uma linha recolhida, com ícone
da instituição e (possivelmente) o percentual que representa do total; ao
tocar, expande revelando os aportes/ativos individuais daquela instituição.

Exemplo de estrutura recolhida:
```
Instituição A (80%) ⌄
Instituição B (20%) ⌄
```

Ao expandir "Instituição A", revela os aportes individuais com seus ícones:
```
Instituição A (80%) ⌃
  Produto X — valor, data de aplicação
  Produto Y — valor, data de aplicação
```

Mesmo padrão se aplicaria à visão consolidada geral de investimentos (fora
do contexto de uma carteira específica): tocar no ícone/linha de uma
instituição expande para mostrar os investimentos ali custodiados,
atravessando todas as carteiras.

Também considerar ícone por instituição financeira ao lado de cada ativo
dentro da visão de uma carteira, para identificação visual rápida de onde
cada aporte está custodiado (ex: dentro da carteira do exemplo, um depósito
com ícone do Banco A, outros dois com ícone do Banco B).

## Comportamento de gráficos em tela pequena

- Pensar no comportamento de rolagem vs. troca de tela quando um gráfico
  precisa aparecer junto de um formulário longo, considerando o espaço
  limitado de tela de celular
- Decisão em aberto: gráfico abaixo do formulário com scroll, ou navegação
  para tela dedicada ao gráfico

---

## Observações

- Nenhum item deste documento deve ser tratado como requisito obrigatório —
  são pontos de partida para a fase de design de UI/UX
- Ao revisar, verificar se cada ideia ainda faz sentido dado tudo que foi
  decidido posteriormente na análise de requisitos (`analise-requisitos.md`)
