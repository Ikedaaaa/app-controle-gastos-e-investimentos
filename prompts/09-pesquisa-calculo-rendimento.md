# Pesquisa aprofundada: como o cálculo automático de rendimento é feito

Este prompt não faz parte da sequência principal (01 a 08) — é uma
pesquisa técnica independente, motivada por uma decisão já registrada no
`analise-requisitos.md` (seção 7, "Rendimento: informado manualmente, não
calculado pelo app"). O MVP usa entrada manual porque a metodologia exata de
cálculo (fonte da taxa, base de dias, tratamento de fins de semana/feriados,
momento de desconto de imposto) ainda não foi validada — este prompt existe
para preencher essa lacuna antes de uma versão futura implementar o cálculo
automático.

Uma pesquisa rápida já foi feita durante a conversa que originou este
prompt e trouxe algumas respostas preliminares (registradas no
`analise-requisitos.md`, seção 7, marcadas como "não validadas"). Este
prompt pede uma pesquisa mais aprofundada, cobrindo os mesmos pontos com
mais profundidade e tentando confirmar (ou corrigir) o que já foi
levantado.

---

## Prompt

```
Você é um pesquisador técnico especializado em mercado financeiro
brasileiro e arquitetura de sistemas bancários. Quero uma pesquisa
aprofundada sobre como aplicações financeiras (bancos, fintechs, corretoras)
calculam e atualizam o rendimento de investimentos de renda fixa
pós-fixados e prefixados no dia a dia.

Contexto: estou desenvolvendo um app pessoal de controle financeiro
(docs/analise-requisitos.md, seção 7) e decidi que, no MVP, o rendimento é
informado manualmente pelo usuário (copiado do extrato do banco), porque a
metodologia exata de cálculo automático ainda não está validada. Este
prompt é para preencher essa lacuna antes de uma versão futura.

Uma pesquisa preliminar já trouxe algumas respostas, registradas abaixo
como ponto de partida — confirme, corrija ou aprofunde cada uma, citando
fontes. Não aceite a resposta preliminar só porque está aqui; ela não foi
validada.

Regras de profundidade, para não ficar numa pesquisa superficial:
- Para cada ponto numerado abaixo, busque em pelo menos 2-3 fontes
  independentes antes de considerar a resposta resolvida. Uma única fonte
  (ex: um único blog) não é suficiente para marcar algo como confirmado.
- Priorize fonte primária/oficial (Banco Central, B3, CVM, documentação
  regulatória) sobre blog de corretora ou conteúdo de marketing quando
  disponível. Se só encontrar fonte secundária, diga isso explicitamente
  em vez de apresentá-la com o mesmo peso de uma fonte oficial.
- Se uma busca trouxer resultados de baixa qualidade ou fora de tópico
  (ex: ruído de busca, conteúdo não relacionado), reformule a busca com
  termos diferentes antes de desistir daquele ponto — não pare na primeira
  tentativa se ela não trouxe nada útil.
- Para cada resposta, indique o nível de confiança: **confirmado** (fonte
  primária ou múltiplas fontes concordantes), **provável** (fonte
  secundária ou única, sem contradição encontrada), ou **sem resposta
  conclusiva** (nenhuma fonte satisfatória encontrada, mesmo após buscas
  reformuladas).

## Pontos a investigar

### 1. Fonte e obtenção da taxa
- Como aplicações obtêm a taxa CDI diária, a taxa SELIC diária e o IPCA
  usados para calcular rendimento de produtos pós-fixados e indexados?
  Existe API pública (ex: API do Banco Central, API da B3) ou esses dados
  são obtidos de outra forma?
- (Resposta preliminar a confirmar: CDI é divulgado pela B3, apenas em dias
  úteis, como média das taxas de empréstimo interbancário de um dia.)
- Isso vale igualmente para SELIC e IPCA, ou cada indexador tem uma fonte e
  frequência de divulgação diferente? IPCA, por exemplo, é mensal — como
  isso se traduz em um "IPCA diário" usado para calcular rendimento dia a
  dia de um título indexado a IPCA+X%?

### 2. Fórmula de cálculo do fator diário
- Qual é a fórmula exata usada para converter uma taxa anual (ou o CDI
  acumulado) em rendimento diário?
- (Resposta preliminar a confirmar: fator diário por composição geométrica,
  `(1 + taxa_anual) ^ (1/252) - 1`, não divisão simples da taxa pelo número
  de dias.)
- Essa mesma fórmula vale para título prefixado? Se eu tenho um prefixado
  de 10% para 15 dias corridos, o fator diário é `(1,10)^(1/15) - 1`
  (dias corridos) ou baseado em dias úteis dentro desse período?

### 3. Tratamento de fins de semana e feriados — ponto central desta pesquisa
Este é o ponto de maior incerteza e onde a pesquisa preliminar só chegou a
uma hipótese não confirmada, que precisa ser investigada a fundo:

- A B3 não divulga uma nova taxa DI em sábados, domingos ou feriados
  (mercado fechado). Mas contas de liquidez diária (Nubank, Mercado Pago,
  PicPay, Inter, e similares) creditam rendimento visível todos os dias,
  inclusive fins de semana. Como isso é possível se não há taxa nova
  divulgada nesses dias?
  - Hipótese preliminar A: a instituição usa a taxa do último dia útil
    (ex: sexta-feira) e aplica esse mesmo fator nos dias não úteis
    seguintes, "carregando" a taxa para frente
  - Hipótese preliminar B: a instituição só credita nominalmente todos os
    dias, mas o cálculo real do período (sexta a segunda) é feito de uma
    vez só na segunda-feira, com o fim de semana sendo absorvido de forma
    invisível ao usuário — o valor exibido no sábado/domingo seria uma
    estimativa, não um valor definitivo
  - Existe alguma fonte (documentação de fintech, artigo técnico,
    regulação do Banco Central, discussão de desenvolvedores) que descreva
    qual dessas hipóteses (ou uma terceira) é a prática real?
- Para título prefixado (sem depender de taxa de mercado divulgada
  diariamente): o cálculo do fator diário desconta dias não úteis do
  período, ou considera todos os dias corridos igualmente?
  - Exemplo concreto a resolver: um prefixado de 10% para 15 dias corridos,
    dos quais 4 são fim de semana. O fator diário deveria ser calculado
    sobre 15 dias corridos, ou sobre 11 dias úteis (ignorando os 4 dias de
    fim de semana)? As duas abordagens dão resultados diferentes — qual é a
    prática de mercado?
- Isso muda entre tipos de produto (CDB, LCI, Tesouro Direto, conta de
  pagamento com liquidez diária) ou é uma regra única de mercado?

### 4. Momento e forma de armazenamento do cálculo
- (Hipótese preliminar a confirmar: o valor de rendimento exibido ao
  usuário não é calculado a cada acesso ao app — é lido de um valor já
  processado por um job/rotina agendada, rodando ao menos uma vez por dia,
  de forma parecida com a marcação diária de cota usada por fundos de
  investimento.)
- Existe alguma fonte (técnica, de arquitetura de sistemas financeiros, ou
  mesmo discussão de desenvolvedores que trabalharam com fintech) que
  discuta esse padrão — processamento em lote noturno, marcação diária de
  cota, ou termo equivalente no mercado brasileiro?
- Esse processamento acontece em qual horário do dia, tipicamente? Antes ou
  depois do fechamento do mercado?

### 5. Imposto de Renda e IOF
- Em que momento o IR (tabela regressiva por prazo) e o IOF (primeiros 30
  dias) são calculados e descontados — a cada atualização diária de
  rendimento, ou só no momento do resgate?
- O valor "líquido" exibido no extrato do banco (ex: Nubank, Inter,
  Mercado Pago) já reflete IR/IOF projetado a cada dia, ou é só uma
  estimativa que só se torna definitiva no resgate?

### 6. Variação entre instituições e tipos de produto
- As respostas acima variam significativamente entre instituições (Nubank,
  Inter, Mercado Pago, PicPay, corretoras tradicionais), ou a metodologia
  de cálculo é padronizada o suficiente para não haver diferença prática
  relevante?
- Existe diferença de tratamento entre CDB de liquidez diária, CDB com
  carência, Tesouro Direto, e fundos, quanto à frequência e ao momento de
  atualização do rendimento exibido?

## Formato da resposta

Para cada ponto, indique:
- O que a pesquisa encontrou, com fonte(s) citada(s) — liste todas as
  fontes consultadas para aquele ponto, não só a que sustenta a conclusão
  final
- O nível de confiança (confirmado / provável / sem resposta conclusiva),
  conforme definido acima
- Se a resposta preliminar (marcada acima) foi confirmada, corrigida, ou
  permanece sem resposta definitiva
- Se não houver fonte conclusiva, diga isso explicitamente — não
  complete a lacuna com suposição apresentada como fato

Ao final, duas seções:

1. **"Pontos ainda sem resposta conclusiva"** — resumindo quais dos 6
   pontos (ou sub-perguntas) não foram resolvidos com confiança, para eu
   saber exatamente onde a lacuna real está antes de qualquer decisão de
   implementação.
2. **"Fontes consultadas"** — uma lista consolidada de todas as fontes
   usadas ao longo da pesquisa (título, link, e a qual ponto numerado ela
   se refere), mesmo que já tenham sido citadas inline em cada ponto. Serve
   como referência única para eu conferir a origem de qualquer afirmação
   sem precisar procurar espalhado pelo documento.

Salve o resultado em docs/discovery/09-pesquisa-calculo-rendimento.md.
```

---

## Depois da pesquisa (manual, não parte do prompt)

O prompt para aqui — ele não deve, por conta própria, alterar o
`docs/analise-requisitos.md`. Depois de gerado, leia
`docs/discovery/09-pesquisa-calculo-rendimento.md` com calma e decida:

- Se a pesquisa trouxe respostas conclusivas, você decide se e como
  atualizar a seção 7 do `docs/analise-requisitos.md`, substituindo as
  "descobertas preliminares" pelo resultado validado
- Você também decide se a decisão de manter entrada manual no MVP ainda é
  a mais adequada, ou se cálculo automático já se tornou viável para algum
  caso mais simples (ex: só contas com liquidez diária)

Se quiser, peça numa sessão separada para a IA propor a atualização do
`analise-requisitos.md` com base no que você aprovou — mas essa é uma
etapa deliberada sua, não uma continuação automática desta pesquisa.

---

**Verificação de conclusão:** você tem um documento novo
(`docs/discovery/09-pesquisa-calculo-rendimento.md`) com respostas
fundamentadas (ou lacunas explicitamente sinalizadas como sem resposta) para
os 6 pontos levantados, especialmente o tratamento de fins de semana e
feriados — o ponto de maior incerteza da pesquisa preliminar. O
`analise-requisitos.md` permanece intocado até você revisar e decidir.
