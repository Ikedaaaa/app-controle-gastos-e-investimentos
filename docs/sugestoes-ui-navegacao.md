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

## Layout de item na lista do fluxo

### Layout pensado: duas colunas com valor em destaque, alça de reorder isolada

Padrão comum em apps financeiros maduros (extratos bancários em geral):
duas colunas, onde a coluna direita é dedicada exclusivamente ao valor, com
fonte maior/mais peso visual, e a esquerda concentra os controles + texto.
Diferente de um extrato bancário puro, este app precisa acomodar também uma
alça de reordenação (drag and drop) — para não virar uma sequência confusa
de ícones pequenos competindo por espaço, a alça fica isolada na margem
esquerda extrema (como um controle "da lista", não "do item"), enquanto
checkbox e ícone de categoria ficam agrupados como um bloco só, antes da
descrição:

```
[alça]  [checkbox][ícone]  Descrição (negrito, linha principal)   + R$50,00
                            Data (cinza, menor, linha secundária)
```

A alça de reorder é o único gatilho de drag and drop — tocar em qualquer
outra parte do item não deve iniciar reordenação (ver seção de requisitos
sobre conflito de gestos), evitando reordenação acidental ao tentar tocar
no item para abrir seu detalhe.

### Referência real de densidade: apps de player de música (positiva) vs. playlists de vídeo (negativa)

Validação de viabilidade de espaço comparando com apps reais: um player de
música típico usa o padrão alça (`≡`) isolada à esquerda + ícone pequeno
quadrado + bloco de texto em duas linhas (título + artista) + menu de três
pontos à direita — estrutura muito próxima do que este app precisa. Com a
correção do conflito de gestos (menu de três pontos por item removido,
substituído pelo gesto de pressionar-e-segurar), o espaço que seria do menu
fica livre para o valor, no mesmo canto direito. Contagem de elementos
fica equivalente: alça + checkbox + ícone + texto (2 linhas) + valor, muito
próxima da referência do player de música (alça + ícone + texto (2 linhas)
+ menu). Conclusão: o layout proposto é viável na largura de tela vertical
de celular, desde que checkbox e ícone fiquem visualmente agrupados como
um bloco compacto (não espalhados).

Referência negativa: apps de playlist de vídeo (ex: YouTube) usam thumbnail
grande e retangular por item — esse padrão **não se aplica aqui**, pois
ocuparia espaço excessivo sem espaço equivalente para descrição e valor.
A referência a seguir é a densidade do player de música, não a de listas
de vídeo com thumbnail.

O valor ganha destaque não por ocupar mais espaço físico, mas por ficar
isolado na própria coluna com maior peso de fonte — segue o padrão de
leitura financeira onde o olho escaneia a coluna da direita para comparar
valores rapidamente. Ícone específico (ex: loja/estabelecimento) não
aparece na lista, só na tela/modal de detalhe do item — na lista aparece só
o ícone genérico da categoria (gasto, investimento, etc.), para não
competir visualmente com muitos ícones diferentes.

### Controle expansível por item para revelar o saldo em cascata

Cada item da lista poderia ter um pequeno controle de expansão (ex: ícone
de seta "v"), distinto do toque no restante do item. Tocar nesse controle
específico expande apenas para revelar "Saldo após este item: R$xxx,xx",
sem abrir modal ou navegar. Tocar em qualquer outra parte do item (fora
desse controle) abre o modal/tela de detalhe completo (ver abaixo). Isso
evita que o saldo em cascata — dado importante para planejamento futuro,
mas irrelevante para itens já concluídos — precise estar sempre visível na
linha do item, poluindo a lista.

### Detalhe do item: modal (bottom sheet) para casos simples, tela dedicada para composições complexas

Ao invés de expandir o item inline dentro da própria lista (que quebraria a
leitura da sequência do fluxo, já que a ordem dos itens tem significado):
- Item simples (sem Explicação de Gasto) → abrir um modal/bottom sheet com
  o detalhe, sem navegar para nova tela
- Item com Explicação de Gasto (múltiplas fontes, nota longa) → abrir tela
  dedicada, por ter conteúdo demais para um modal pequeno

O padrão de lista expansível tipo "FAQ" (mencionado acima) funciona bem
para agregações planas sem ordem sequencial importante (ex: lista de
instituições financeiras), mas não é recomendado para a lista principal do
fluxo, por essa quebra de leitura da sequência.

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
