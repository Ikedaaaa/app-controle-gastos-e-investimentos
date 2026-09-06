# Pesquisa aprofundada: como uma compra no crédito é atribuída à fatura

Este prompt não faz parte da sequência principal (01 a 08) — é uma pesquisa
técnica independente, no mesmo padrão do prompt `09` (pesquisa de cálculo
de rendimento). Motivada por uma lacuna identificada no
`docs/analise-requisitos.md` (seção 9): o documento afirma que "toda
compra no crédito, ao ser registrada, já sabe a qual fatura pertence,
baseado na data da compra e nas datas de fechamento/vencimento do cartão",
mas nunca especificou a regra exata de atribuição — especialmente o caso
de borda de uma compra feita **no próprio dia do fechamento**.

Duas fontes informais já consultadas, com respostas contraditórias entre
si:
- Uma pesquisa rápida (Serasa) sugeriu que a compra no dia do fechamento
  entra na fatura atual (que fecha aquele dia) — só o dia seguinte cairia
  na próxima fatura
- Uma busca posterior encontrou a informação oposta: compra no dia do
  fechamento entraria só na próxima fatura (o dia do fechamento seria o
  "melhor dia de compra", com até ~40 dias de prazo), citado a partir de
  material do Nubank — com a ressalva adicional de que isso pode depender
  do **horário** em que o banco processa o fechamento naquele dia (compra
  antes do processamento cai na atual, depois cai na próxima)

Nenhuma das duas foi validada com rigor — este prompt pede uma pesquisa
mais profunda para resolver a contradição, com múltiplas fontes e nível de
confiança por afirmação, seguindo o mesmo padrão de rigor do prompt `09`.

---

## Prompt

```
Você é um pesquisador técnico especializado em mercado financeiro
brasileiro, cartões de crédito e regulação do Banco Central. Quero uma
pesquisa aprofundada sobre como instituições financeiras brasileiras
determinam a qual fatura uma compra no crédito é atribuída, especialmente
no caso de borda de uma compra feita no próprio dia do fechamento.

Contexto: estou desenvolvendo um app pessoal de controle financeiro
(docs/analise-requisitos.md, seção 9) que precisa calcular automaticamente,
para cada compra registrada, a qual fatura ela pertence, com base na data
da compra e nas datas de fechamento/vencimento configuradas para o cartão.

Duas buscas informais já feitas trouxeram respostas contraditórias sobre o
caso de borda (compra no dia exato do fechamento):
- Hipótese A: a compra no dia do fechamento entra na fatura que fecha
  naquele mesmo dia (fatura atual) — só o dia seguinte cairia na próxima
- Hipótese B: a compra no dia do fechamento já entra na próxima fatura —
  o dia do fechamento seria o "melhor dia de compra" por dar o maior prazo
  possível até o pagamento, com a ressalva de que isso pode depender do
  horário em que o banco processa o fechamento (antes do processamento cai
  na atual, depois cai na próxima)

Nenhuma das duas foi validada com rigor. Não aceite nenhuma das duas só
porque estão aqui — confirme, corrija ou refine com fontes.

Regras de profundidade, para não ficar numa pesquisa superficial:
- Para a pergunta central (caso de borda do dia do fechamento), busque em
  pelo menos 3 fontes independentes antes de considerar resolvida. Uma
  única fonte não é suficiente.
- Priorize fonte primária/oficial (Banco Central, CVM, termos de uso
  publicados por instituições financeiras específicas) sobre blog
  genérico de educação financeira, quando disponível.
- Se uma busca trouxer resultado de baixa qualidade ou contraditório,
  reformule a busca com termos diferentes antes de desistir.
- Para cada resposta, indique o nível de confiança: **confirmado** (fonte
  primária ou múltiplas fontes concordantes), **provável** (fonte
  secundária ou única, sem contradição encontrada), ou **sem resposta
  conclusiva**.

## Pontos a investigar

### 1. Regra geral de atribuição (fora do caso de borda)
- Confirme a regra básica: compras entre o dia seguinte ao último
  fechamento e o dia do fechamento atual (ou o dia anterior a ele,
  dependendo da resposta ao ponto 2) entram na fatura que fecha nesse
  ciclo.

### 2. Caso de borda: compra no próprio dia do fechamento
- A compra feita no dia exato do fechamento entra na fatura que fecha
  naquele dia (atual), ou já cai na próxima?
- Existe uma regra única de mercado, ou isso varia por instituição
  financeira? Se variar, cite exemplos de instituições diferentes com
  comportamentos diferentes, com fonte.
- É verdade que o dia do fechamento é comumente considerado "o melhor dia
  de compra" (maior prazo até o pagamento)? Isso é consistente com a
  compra naquele dia entrar na fatura atual ou na próxima? (Se entra na
  fatura atual, o prazo seria menor, não maior — investigue se há
  contradição nessa lógica ou se estou entendendo errado)

### 3. Dependência de horário de processamento
- É verdade que o resultado do ponto 2 pode depender do horário em que o
  banco processa o fechamento (compra feita antes do horário de corte cai
  na atual, depois cai na próxima)?
- Se verdade, existe informação pública sobre em que horário do dia esse
  processamento costuma ocorrer? Isso é padronizado ou varia por
  instituição?
- Isso é um detalhe geralmente documentado nos termos de uso/contrato do
  cartão, ou é comportamento não divulgado publicamente pela maioria das
  instituições?

### 4. Existe regulação do Banco Central sobre isso?
- Existe alguma norma ou resolução do Banco Central do Brasil (BCB) ou do
  CMN que regule especificamente esse ponto (atribuição de compra à
  fatura, ou o intervalo mínimo entre fechamento e vencimento), ou é
  inteiramente prática de mercado não regulada nesse nível de detalhe?

### 5. Variação entre instituições
- As respostas acima são uniformes entre bancos/fintechs brasileiras
  (Nubank, Inter, Itaú, Bradesco, C6, etc.), ou existe variação real
  documentada entre instituições específicas?

## Formato da resposta

Para cada ponto, indique:
- O que a pesquisa encontrou, com fonte(s) citada(s) — liste todas as
  fontes consultadas para aquele ponto
- O nível de confiança (confirmado / provável / sem resposta conclusiva)
- Se alguma das duas hipóteses (A ou B) foi confirmada, corrigida, ou
  permanece sem resposta definitiva
- Se não houver fonte conclusiva, diga isso explicitamente — não
  complete a lacuna com suposição apresentada como fato

Ao final, duas seções:
1. **"Pontos ainda sem resposta conclusiva"** — resumindo quais pontos não
   foram resolvidos com confiança
2. **"Fontes consultadas"** — lista consolidada de todas as fontes usadas
   (título, link, a qual ponto se refere)

Salve o resultado em docs/discovery/10-pesquisa-atribuicao-fatura.md.
```

---

## Depois da pesquisa (manual, não parte do prompt)

O prompt para aqui — ele não deve, por conta própria, alterar o
`docs/analise-requisitos.md`. Depois de gerado, leia
`docs/discovery/10-pesquisa-atribuicao-fatura.md` com calma e decida:

- Se a pesquisa trouxer resposta conclusiva para o caso de borda, você
  decide como formalizar a regra de atribuição na seção 9
- Se a resposta for "varia por instituição" ou "depende de horário não
  controlável pelo app", considere se a regra deveria ser configurável por
  cartão (ex: um campo "compra no dia do fechamento entra na fatura atual
  ou seguinte", análogo à decisão já tomada para o resto de parcela
  primeira/última) em vez de fixa

---

**Verificação de conclusão:** você tem um documento novo
(`docs/discovery/10-pesquisa-atribuicao-fatura.md`) com resposta
fundamentada (ou lacuna explicitamente sinalizada) para o caso de borda do
dia do fechamento, incluindo a dependência de horário de processamento — o
ponto de maior incerteza identificado nesta conversa. O
`analise-requisitos.md` permanece intocado até você revisar e decidir.
