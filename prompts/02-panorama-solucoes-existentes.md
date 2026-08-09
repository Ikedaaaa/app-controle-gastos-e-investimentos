# Panorama de soluções existentes

No treinamento original este passo era pesquisa de mercado e cenário
competitivo — análise de concorrentes para orientar posicionamento de
negócio. Aqui não existe negócio a posicionar; o objetivo é mais modesto:
ver rapidamente como apps de finanças pessoais já resolvem (ou não resolvem)
problemas parecidos com os seus, para roubar boas ideias de fluxo/UI e
confirmar que nenhum cobre o seu caso específico (caixinhas com aportes
individuais, carteira de terceiro, fatura composta por múltiplas fontes).

Isto não é benchmark de negócio. É pesquisa de UI e de gaps de
funcionalidade, para alimentar o prompt `06` (ideação de UI) com referências
concretas em vez de reinventar tudo do zero.

---

## Prompt

```
Você é um pesquisador de UX especializado em apps de finanças pessoais,
com conhecimento amplo de como os principais apps do mercado resolvem
fluxos de lançamento, dashboards e objetivos de economia.

Quero um panorama rápido de como apps de controle financeiro pessoal
existentes resolvem problemas parecidos com os meus. Não é análise
competitiva de negócio — é pesquisa de UI e de funcionalidades, para eu ter
referências concretas antes de decidir como resolver os pontos abertos do
meu projeto.

Contexto do meu projeto: docs/analise-requisitos.md (leia se ainda não leu
nesta sessão). Foque especialmente nas seções sobre fluxo de subtração em
cascata (seção 3), painel analítico (seção 4), carteiras/caixinhas (seção 7)
e composição de fatura (seção 9-10) — são as áreas com mais decisão de UI
ainda aberta.

Pesquise na web como apps conhecidos de finanças pessoais (ex.: Mobills,
Organizze, YNAB, GnuCash, Money Manager) resolvem hoje:

1. Fluxo de lançamentos com saldo projetado (equivalente à minha "cascata de
   subtração") — como apresentam a lista, como mostram saldo após cada item
2. Painéis de resumo/dashboard por período — o que mostram de cara, o que
   fica escondido atrás de navegação
3. Objetivos de economia / caixinhas — como representam progresso, como
   lidam com múltiplos aportes dentro do mesmo objetivo
4. Composição de fatura de cartão — como (ou se) permitem detalhar de onde
   vem o dinheiro que paga a fatura
5. Qualquer padrão de gesto (toque, arrastar, pressionar e segurar) usado em
   listas de lançamentos que valha considerar

Para cada ponto, diga qual app faz o quê, e se isso é ou não aplicável ao meu
caso (meu app tem particularidades que a maioria não tem — caixinha com
aportes individuais, carteira de terceiro, cascata de checkbox — sinalize
onde nenhum concorrente cobre isso, porque é esperado).

Não estou procurando validação de mercado nem espaço competitivo — é só
inventário de ideias de UI já testadas por outros produtos.

Salve o resultado em docs/discovery/01-panorama-solucoes-existentes.md.
```

---

**Verificação de conclusão:** você tem um arquivo com referências concretas
de como outros apps resolvem problemas parecidos, e sabe quais das suas
particularidades (caixinha, terceiro, cascata) não têm equivalente em
nenhuma solução existente — o que é esperado e não é motivo de preocupação.
