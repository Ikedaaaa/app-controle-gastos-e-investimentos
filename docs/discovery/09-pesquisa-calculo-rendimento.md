# Pesquisa: como o cálculo automático de rendimento é feito na prática

> Pesquisa realizada em 29/08/2026, motivada pela decisão registrada em
> `docs/analise-requisitos.md` (seção 7) de manter entrada manual de
> rendimento no MVP. Objetivo: confirmar, corrigir ou aprofundar as
> hipóteses preliminares levantadas antes dessa decisão, com foco especial
> no tratamento de fins de semana e feriados (ponto 3).

**Aviso geral sobre a qualidade das fontes disponíveis:** ao longo de toda a
pesquisa, a informação mais precisa e "operacional" (fórmula exata, se o
fim de semana é carregado ou absorvido, etc.) esteve quase sempre em blogs
de corretoras/fintechs, simuladores e discussões de comunidade — não em
documentação oficial acessível publicamente. B3, CETIP e ANBIMA confirmam
os conceitos gerais (o que é a taxa DI, que é divulgada em dias úteis, que
existe base 252), mas não publicam gratuitamente uma "especificação de
implementação" de como uma fintech deve tratar sábado/domingo no cálculo
exibido ao usuário. Isso é sinalizado explicitamente em cada ponto abaixo.

---

## 1. Fonte e obtenção da taxa

**O que a pesquisa encontrou:**

- CDI/Taxa DI: divulgada diariamente (em dias úteis) pela B3 (antiga
  CETIP, incorporada à B3 em 2017), como taxa média das operações de
  empréstimo interbancário de um dia ("DI over"), expressa ao ano com base
  em 252 dias úteis. Confirmado por múltiplas fontes concordantes:
  [Dicionário Financeiro](https://www.dicionariofinanceiro.com.br/taxa_di),
  [Wikipédia - CDI](https://pt.wikipedia.org/wiki/Certificado_de_Dep%C3%B3sito_Interbanc%C3%A1rio),
  [Crédito e Mercado](https://www.creditoemercado.com.br/blogconsultoriaeminvestimentos/?p=2024).
  Não encontrei uma página oficial da B3 (b3.com.br) com a metodologia
  detalhada acessível gratuitamente — a B3 vende esse dado como produto de
  market data; o que existe publicamente são descrições de terceiros sobre
  como a taxa é calculada, não a metodologia original publicada em aberto.
- SELIC: divulgada pelo Banco Central, com série "Selic acumulada no mês
  anualizada base 252" disponível na
  [API SGS/Dados Abertos do BCB](https://dadosabertos.bcb.gov.br/dataset/11-taxa-de-juros---selic)
  — essa é fonte primária e gratuita, com séries diárias, mensais e
  anualizadas. Existem wrappers de terceiros bem estabelecidos
  ([python-bcb](https://pypi.org/project/python-bcb/),
  [bcbpy](https://pypi.org/project/bcbpy/), pacote R `rbcb`) que consomem
  essa API SGS.
- IPCA: divulgado mensalmente pelo IBGE (não pelo BCB), e o Banco Central
  também mantém a série no SGS (código 433, segundo referência cruzada em
  [gist de códigos SGS](https://gist.github.com/wesleyit/b2f1d25f3f0200779a9a7399fa6ea344)
  e na [documentação do python-bcb](https://wilsonfreitas.github.io/python-bcb/sgs.html)).
  Não há "IPCA diário" divulgado oficialmente — títulos IPCA+ usam uma
  **projeção/interpolação pro rata** do índice mensal para estimar a
  variação diária entre uma divulgação mensal e a próxima (mecanismo
  descrito de forma consistente, mas apenas em fontes secundárias — ver
  ponto abaixo).

**Nível de confiança:**
- CDI divulgado pela B3/mercado interbancário, em dias úteis, base 252:
  **confirmado** (múltiplas fontes secundárias concordantes; não achei o
  documento metodológico oficial da B3 em acesso aberto).
- SELIC via API SGS do Banco Central: **confirmado** (fonte primária
  oficial, API pública e gratuita).
- IPCA mensal via IBGE, sem "taxa diária oficial": **confirmado** o fato
  de ser mensal; a forma exata como cada instituição deriva um "IPCA
  diário" projetado é **provável, não confirmado por fonte primária** —
  não encontrei documento do BCB/ANBIMA especificando uma fórmula única e
  obrigatória de pro rata para todas as instituições (ver ponto 2).

**Resposta preliminar:** confirmada quanto a CDI ser divulgado pela B3, em
dias úteis, como média de operações interbancárias de um dia. A hipótese
preliminar não mencionava SELIC/IPCA com o mesmo nível de detalhe — agora
está mais claro que cada indexador tem fonte e frequência diferentes:
SELIC é diária (via BCB/SGS), CDI é diária em dias úteis (via B3), IPCA é
mensal (via IBGE), exigindo projeção para uso diário.

---

## 2. Fórmula de cálculo do fator diário

**O que a pesquisa encontrou:**

- A conversão de taxa anual (CDI ou SELIC) para fator diário usa
  composição geométrica com base 252 dias úteis:
  `fator_diário = (1 + taxa_anual) ^ (1/252) - 1`
  Essa fórmula aparece de forma consistente em várias fontes: um post de
  comunidade Microsoft/DAX ([Fabric Community](https://community.fabric.microsoft.com/t5/DAX-Commands-and-Tips/Calculating-a-factor/m-p/2958408))
  usando exatamente essa estrutura de fator com expoente `1/252`, e
  confirmado textualmente pelo blog [Mycon](https://www.mycon.com.br/blog/post/como-calcular-o-rendimento-do-cdi)
  ("divide-se a taxa por 252, aplicam-se juros compostos") e por
  [Exame](https://exame.com/invest/guia/quanto-rende-r-1-milhao-a-100-do-cdi-por-dia/)
  ("252 é o número de dias úteis no ano utilizado pelo mercado financeiro
  brasileiro"). Nenhuma fonte encontrada defende divisão simples
  (taxa/252) sem composição — a única simplificação vista foi em textos
  didáticos que arredondam a explicação, mas a fórmula final sempre usa
  exponenciação, não divisão linear.
- **Prefixado — este é o ponto mais divergente entre fontes.** Não há
  fonte que descreva de forma explícita e inequívoca "o prefixado usa dias
  corridos ou dias úteis" para o caso genérico de CDB. O que ficou claro:
  - No mercado de **títulos públicos** (Tesouro Direto, NTN-B etc.), a
    convenção de mercado é *taxa efetiva anual, base 252 dias úteis*,
    conforme documento acadêmico
    ([slide/documento sobre NTN-B](https://www.yumpu.com/pt/document/view/47282928/t-susep/7)):
    "o mercado divulga a rentabilidade desses títulos na forma de taxa
    efetiva anual, base 252 dias úteis". Isso indica que, para títulos
    públicos negociados via ANBIMA/B3, mesmo o prefixado usa contagem de
    **dias úteis**, não dias corridos.
  - Para **CDB prefixado de varejo** (o caso citado no exemplo do prompt,
    "10% para 15 dias corridos"), não encontrei uma fonte primária ou
    secundária que resolva diretamente esse exemplo numérico. Simuladores
    de CDB (ex. [calculadora-renda-fixa.com](https://calculadora-renda-fixa.com/),
    [calculosonline.com.br](https://calculosonline.com.br/calculadora/cdb))
    mostram o resultado final mas não expõem a fórmula/base de dias usada
    internamente.
- **A fórmula de composição geométrica base 252 se aplica igualmente ao
  CDI/SELIC quanto à conversão anual→diária.** Para IPCA, a lógica é
  diferente: como só existe divulgação mensal, o pro rata diário é uma
  **interpolação/projeção**, e não uma simples divisão por dias — mas a
  fórmula exata de interpolação (linear vs. geométrica, e a fonte da
  projeção do IPCA do mês corrente antes de ele ser oficialmente
  divulgado) não foi encontrada em documento normativo público. A
  [Calculadora do Cidadão do BCB](https://www3.bcb.gov.br/CALCIDADAO/publico/metodologiaCorrigirPelaTaxaLegal.do?method=metodologiaCorrigirPelaTaxaLegal)
  descreve pro rata para correção de valores por "Taxa Legal" (não CDI/IPCA
  especificamente), usando "juros proporcionais (fração pro rata)... pela
  razão entre a Taxa [mensal] e o número de dias corridos do mês", o que é
  uma referência primária do BCB sobre pro rata em geral, mas não é a
  metodologia oficial de títulos IPCA+ do Tesouro.

**Nível de confiança:**
- Fórmula `(1+taxa)^(1/252)-1` para CDI/SELIC: **confirmado** (múltiplas
  fontes concordantes, embora nenhuma seja documento oficial da B3/BCB
  especificamente sobre "como uma fintech deve calcular"; é a convenção de
  mercado amplamente replicada).
- Base de dias (úteis vs. corridos) para prefixado de CDB de varejo:
  **sem resposta conclusiva**. Para títulos públicos, há indício
  (fonte acadêmica/técnica) de que a convenção de mercado usa base 252
  dias úteis mesmo para prefixado — mas não é uma confirmação forte, e não
  resolve o exemplo concreto de "15 dias corridos com 4 de fim de semana"
  citado no prompt original.
- Fórmula de "IPCA diário" (pro rata do índice mensal): **provável**, sem
  fonte primária que padronize a fórmula de interpolação usada por
  fintechs/bancos para exibir rendimento diário de título IPCA+.

**Resposta preliminar:** confirmada para CDI/pós-fixado
(`(1+taxa)^(1/252)-1`, composição geométrica, não divisão simples).
**Não confirmada** para prefixado — a hipótese de que a mesma fórmula
`(1,10)^(1/15)-1` valeria diretamente sobre dias corridos não encontrou
suporte nem contradição direta; ficou como lacuna real (ver seção final).

---

## 3. Tratamento de fins de semana e feriados — ponto central

**O que a pesquisa encontrou:**

Este foi o ponto mais difícil de resolver com uma fonte técnica/normativa
explícita. Não encontrei documentação oficial do Banco Central, B3 ou
qualquer fintech que descreva publicamente, em linguagem de especificação,
qual das hipóteses (A ou B, ou uma terceira) é usada.

Evidências indiretas relevantes, todas de fontes secundárias:

- Um usuário identificado como "NuMentor" (moderador voluntário da
  comunidade, não funcionário oficial do Nubank) explica em uma discussão
  do fórum da comunidade Nubank
  ([comunidade.nubank.com.br](https://comunidade.nubank.com.br/discussoes/post/caixinha-nao-rende-100-do-cdi-rgemLAXn8esQ9lN)):
  "a dificuldade está apenas no cálculo do CDI que deve ser feito com a
  taxa diária apenas em dias úteis (já desconsiderando os feriados
  oficiais da B3/ANBIMA)". Essa afirmação é consistente com a **Hipótese A
  parcialmente invertida**: não é que a instituição "carregue" a taxa de
  sexta-feira para sábado e domingo como se fossem dias que rendem
  igualmente — é que **o cálculo real considera apenas os dias úteis do
  período**, e o valor exibido no fim de semana é uma projeção/estimativa
  do que o saldo será quando a próxima taxa útil for aplicada, sem que o
  saldo "renda" de fato sábado e domingo isoladamente.
- Essa mesma pessoa menciona ter criado uma planilha de acompanhamento
  precisa "considerando as variações diárias do CDI que ocorreram em todo
  período" — reforçando que o cálculo correto exige olhar apenas os dias
  úteis, não uma divisão igualitária entre todos os dias corridos.
- Não encontrei nenhuma fonte (nem mesmo secundária) que descreva
  explicitamente a Hipótese B (todo o fim de semana sendo processado de
  uma vez só na segunda, de forma "invisível", com o saldo do
  sábado/domingo sendo apenas cosmético) como mecanismo confirmado. É
  plausível como implementação de UI (mostrar o mesmo valor sábado e
  domingo, sem que nada seja de fato calculado nesses dias, e só
  recalcular na segunda com a taxa de sexta aplicada uma única vez), mas
  isso é **indistinguível, do ponto de vista do usuário, de "carregar a
  taxa de sexta"** — o resultado numérico final seria o mesmo se a
  instituição aplicar o fator de sexta uma vez (absorvendo 3 dias
  corridos de uma vez na virada de segunda) ou se ela mostrar o saldo
  "congelado" no sábado/domingo e só atualizar na segunda. A diferença
  entre A e B, portanto, é mais uma questão de **como a UI apresenta o
  número no fim de semana** (estimativa "congelada" vs. valor que parece
  estar mudando) do que uma diferença efetiva no valor final recebido.
  Nenhuma fonte confirma qual comportamento de UI é usado — isso varia
  visivelmente entre apps (alguns mostram o saldo igual no fim de semana,
  outros mostram incrementos diários mesmo em fins de semana, sugerindo
  que pelo menos alguns produtos fazem uma distribuição — não confirmada
  oficialmente — do rendimento do período em partes iguais por dia
  corrido para fins de exibição, mesmo que o cálculo financeiro real siga
  dias úteis).
- Para **prefixado**, a mesma pergunta permanece sem resposta conclusiva:
  não encontrei fonte que resolva diretamente se um CDB prefixado com
  prazo em dias corridos tem seu fator diário calculado sobre dias
  corridos ou sobre dias úteis dentro do período. O único indício (fraco,
  de fonte acadêmica sobre títulos públicos, ver ponto 2) sugere base 252
  dias úteis mesmo para prefixados no mercado de títulos públicos — mas
  isso não foi confirmado para CDB de varejo bancário, que pode seguir
  convenção diferente (ex: "taxa ao ano linear sobre dias corridos" é uma
  convenção comum em contratos de crédito/financiamento no Brasil, mas
  não confirmei que CDBs prefixados de varejo sigam essa convenção
  especificamente).
- Diferença entre tipos de produto: não encontrei fonte que afirme uma
  regra diferente entre CDB, LCI, Tesouro Direto e conta de pagamento
  quanto a esse tratamento específico de fim de semana — a suposição mais
  sustentada pelas fontes é que todos os produtos indexados ao CDI/SELIC
  seguem a mesma lógica de "só há fator novo em dia útil", porque a fonte
  do indexador (a taxa em si) é a mesma B3/BCB para todos.

**Nível de confiança: sem resposta conclusiva**, com uma pista relevante
mas não oficial (comentário de comunidade, não documentação de fintech ou
regulador). Reformulei a busca várias vezes (termos em português e
variações sobre "carrega taxa", "absorve fim de semana", "provisão diária")
sem encontrar uma fonte técnica/oficial que resolva isso de forma
definitiva.

**Resposta preliminar:** nenhuma das duas hipóteses foi confirmada ou
refutada com uma fonte satisfatória. A evidência mais próxima (fórum
Nubank) aponta para uma variante da Hipótese A (cálculo real usa apenas
dias úteis; exibição no fim de semana é estimativa), mas isso vem de um
usuário da comunidade, não de documentação oficial — **fica marcado como
lacuna real, não decidido**.

---

## 4. Momento e forma de armazenamento do cálculo

**O que a pesquisa encontrou:**

- O conceito equivalente mais próximo e bem documentado no mercado
  brasileiro é a **marcação a mercado** (mark-to-market) de títulos e
  cotas de fundos: processo de reprecificar diariamente o valor de um
  ativo pelo preço/taxa vigente. Fontes concordantes:
  [Blog Genial](https://blog.genialinvestimentos.com.br/o-que-e-marcacao-a-mercado/),
  [Toro Investimentos](https://blog.toroinvestimentos.com.br/renda-fixa/marcacao-a-mercado/),
  [Investidor Sardinha](https://investidorsardinha.r7.com/aprender/marcacao-a-mercado-mam/),
  [Sicredi](https://www.sicredi.com.br/site/blog/investimentos/marcacao-mercado-voce-sabe-o-que-e-e-como-ela-age-nos-investimentos/).
  Todas descrevem o conceito e o fato de ser diário, mas **nenhuma
  especifica o horário exato de processamento** nem descreve a arquitetura
  de job/batch por trás (isso é detalhe de implementação interno de cada
  instituição, não documentado publicamente).
- Para fundos de investimento, o conceito de **valor da cota atualizado
  diariamente** é confirmado pela
  [Wikipédia - Fundo de investimento](https://pt.wikipedia.org/wiki/Fundo_de_investimento):
  "o valor da cota é atualizado diariamente e o cálculo do saldo do
  cotista é feito multiplicando o número de cotas adquiridas pelo valor
  da cota no dia" — mas, de novo, sem detalhar o horário ou o mecanismo
  de processamento.
- Não encontrei nenhuma fonte técnica (post de engenharia de fintech,
  paper, documentação de arquitetura) que descreva explicitamente "isso é
  processado em um job noturno" ou equivalente para CDBs/contas de
  pagamento. É uma inferência plausível dado como o resto do mercado
  funciona (marcação diária de cota, fechamento de pregão da B3 por volta
  de 17h-18h em horário padrão, conforme
  [CNN Brasil sobre horário B3](https://www.cnnbrasil.com.br/economia/mercado/b3-tem-novo-horario-de-negociacao-a-partir-de-segunda-feira-10/)),
  mas **não é uma confirmação com fonte primária ou documentação técnica
  de fintech**.

**Nível de confiança: sem resposta conclusiva** quanto ao mecanismo
específico (job agendado, horário exato). O paralelo com marcação diária
de cota de fundos é **confirmado** como conceito de mercado análogo
existente, mas isso não confirma que bancos/fintechs usem literalmente
essa mesma arquitetura para CDB/conta de pagamento — é uma inferência por
analogia, não um fato verificado.

**Resposta preliminar:** a hipótese de processamento em lote (não
recalculado a cada acesso) é consistente com o que se sabe sobre marcação
de cota de fundos, mas **não há fonte que confirme isso especificamente
para CDBs de liquidez diária/contas de pagamento**, nem o horário em que
isso ocorreria. Permanece hipótese razoável, não fato confirmado.

---

## 5. Imposto de Renda e IOF

**O que a pesquisa encontrou:**

- **IR (tabela regressiva) e IOF são efetivamente descontados apenas no
  momento do resgate**, não diariamente como retenção real — isso é
  confirmado por múltiplas fontes concordantes, incluindo o
  [Tesouro Direto (fonte oficial)](https://www.tesourodireto.com.br/blog/quais-sao-os-impostos-e-taxas-ao-investir-no-td.htm):
  "investidores devem pagar o Imposto de Renda (IR) no momento do
  resgate", e reforçado por
  [ValorFinal - Tabela IOF](https://valorfinal.com.br/tabela-iof-renda-fixa)
  (que cita a fonte primária
  [Decreto nº 6.306/2007, Anexo](https://www.planalto.gov.br/ccivil_03/_ato2007-2010/2007/decreto/d6306.htm),
  regulamento oficial do IOF) e por
  [Rankia](https://rankia.com.br/imposto-de-renda-cdb/) e
  [Renova Invest](https://renovainvest.com.br/blog/imposto-de-renda-cdb/).
- **Ordem de incidência confirmada por fonte com referência ao decreto:**
  o IOF incide primeiro sobre o rendimento bruto (tabela regressiva de 96%
  no 1º dia a 0% a partir do 30º dia corrido), e o IR incide depois, sobre
  o rendimento já líquido de IOF — exemplo numérico detalhado em
  [ValorFinal](https://valorfinal.com.br/tabela-iof-renda-fixa): "Uma
  aplicação de R$ 20.000 em CDB que rendeu R$ 120 em 15 dias corridos: o
  IOF do 15º dia é 50%, então R$ 60 ficam com a Receita. Sobre os R$ 60
  restantes incide o IR de 22,5%". Essa mesma ordem (IOF antes do IR) é
  replicada por [calculadora-renda-fixa.com](https://calculadora-renda-fixa.com/).
  A tabela regressiva do IOF é contada em **dias corridos** desde a
  aplicação (confirmado pelo texto da fonte, e consistente com o Decreto
  6.306/2007).
- **O valor "líquido" exibido no extrato do banco não é, tecnicamente, um
  valor com imposto já retido e recolhido** — é uma **estimativa/projeção**
  do que sobraria se o resgate fosse feito naquele dia, calculada com a
  tabela regressiva vigente para aquele número de dias. Não encontrei
  fonte que contradiga isso; é consistente com a lógica de que IR sobre
  renda fixa é retido na fonte apenas no evento de resgate/vencimento (a
  instituição não faz recolhimento à Receita todo dia). Essa conclusão é
  inferida a partir da combinação das fontes acima (o IR só é evento
  gerador no resgate) — nenhuma fonte usa literalmente a palavra
  "estimativa" para descrever o valor líquido do extrato diário, então
  marco como **provável**, não confirmado por citação direta.

**Nível de confiança:**
- Momento do desconto (só no resgate): **confirmado** (fonte oficial
  Tesouro Direto + decreto regulamentador do IOF).
- Ordem de incidência (IOF antes do IR, sobre o rendimento bruto):
  **confirmado** (decreto oficial + múltiplos simuladores concordantes com
  exemplo numérico).
- Valor líquido do extrato ser "estimativa, não definitivo": **provável**
  (inferência lógica bem sustentada, mas sem citação textual direta de
  banco/fintech afirmando isso).

**Resposta preliminar:** confirmada. O IR/IOF é calculado e descontado
apenas no resgate; o que aparece no extrato do dia a dia é uma projeção do
valor líquido baseada na tabela regressiva vigente para o tempo decorrido
até aquele momento, não uma retenção real já efetuada.

---

## 6. Variação entre instituições e tipos de produto

**O que a pesquisa encontrou:**

- **Regras fiscais (IR/IOF) são padronizadas por lei/decreto federal** —
  não variam por instituição, já que decorrem do Decreto 6.306/2007 (IOF)
  e da legislação de tabela regressiva de IR sobre renda fixa. Isso é
  **confirmado** e não depende de escolha de cada banco/fintech.
- **A fonte do indexador (CDI, SELIC) é a mesma para todo o mercado** —
  todas as instituições que oferecem produtos "% do CDI" usam a mesma taxa
  DI divulgada pela B3, então a taxa-base não varia entre Nubank, Inter,
  PicPay, Mercado Pago ou corretoras tradicionais. O que varia é apenas o
  **percentual contratado** (100%, 110%, 120% do CDI, etc.) e eventuais
  condições para manter esse percentual (ex: a
  ["Caixinha Turbo" do Nubank](https://www.nubank.com.br/nu/conta/caixinha-turbo)
  exige movimentação mínima mensal para manter rendimento elevado — se não
  cumprido, "o rendimento volta para 100% do CDI").
- Quanto à **frequência e momento de exibição do rendimento no dia a dia**
  (o ponto 3/4 desta pesquisa), não encontrei fonte comparando
  explicitamente a implementação entre instituições. A reclamação de
  usuário sobre "Caixinha não rende 100% do CDI"
  ([comunidade Nubank](https://comunidade.nubank.com.br/discussoes/post/caixinha-nao-rende-100-do-cdi-rgemLAXn8esQ9lN))
  mostra que mesmo dentro de uma única instituição há confusão e
  divergência de entendimento sobre o cálculo correto — o que sugere que
  a experiência do usuário (o que é exibido, quando, e como reconciliar
  com o "CDI atual" divulgado pela mídia) não é trivial nem uniforme,
  mas isso é evidência anedótica, não uma comparação sistemática entre
  instituições.
- Diferença entre CDB de liquidez diária, CDB com carência, Tesouro
  Direto e fundos: confirmado que a **liquidação** (quando o dinheiro do
  resgate efetivamente cai na conta) varia — CDBs de liquidez diária do
  Inter, por exemplo, processam resgate "todos os dias, inclusive finais
  de semana e feriados, entre 03:01h e 21:54h"
  ([Ajuda Inter](https://ajuda.inter.co/investimentos/como-resgato-um-cdb-liquidez-diaria)),
  enquanto fundos em geral têm prazo de cotização (D+0, D+1, D+30 etc,
  variando por fundo) — mas isso é sobre **liquidação do resgate**, não
  sobre a frequência de *atualização do valor exibido*, que é o que este
  ponto pergunta. Não encontrei fonte que compare diretamente a frequência
  de atualização do saldo exibido entre esses produtos.

**Nível de confiança:**
- Padronização de regras fiscais e da fonte do indexador entre
  instituições: **confirmado**.
- Uniformidade (ou não) do tratamento de exibição diária/fim de semana
  entre instituições e produtos: **sem resposta conclusiva** — não há
  fonte comparativa direta.

**Resposta preliminar:** parcialmente confirmada. O que é regulado por lei
(impostos) e o que vem da mesma fonte de mercado (CDI/SELIC) é
padronizado. O que depende de implementação de cada instituição (como e
quando o valor exibido no app é atualizado) permanece sem comparação
direta encontrada — não há evidência de que seja padronizado, nem de que
varie; simplesmente não foi encontrada uma fonte que trate esse
comparativo.

---

## Pontos ainda sem resposta conclusiva

Resumo das lacunas reais, para orientar decisões de implementação futuras:

1. **Tratamento de fim de semana/feriado no cálculo do pós-fixado (ponto
   3, a pergunta central desta pesquisa)** — não há fonte oficial ou
   técnica confiável confirmando se a instituição "carrega" a taxa de
   sexta para os dias não úteis seguintes, se absorve o fim de semana de
   forma invisível na segunda-feira, ou se distribui o rendimento do
   período proporcionalmente por dia corrido só para fins de exibição. A
   única pista encontrada é um comentário de usuário em fórum de
   comunidade (não documentação oficial), que sugere que o cálculo real
   usa apenas dias úteis — mas isso não resolve como o valor é
   apresentado visualmente sábado/domingo, nem se todas as instituições
   fazem da mesma forma.
2. **Base de dias para título prefixado (dias corridos vs. dias úteis)** —
   sem resposta para o caso concreto de CDB de varejo. Há indício (fraco)
   de que títulos públicos usam base 252 dias úteis mesmo para
   prefixados, mas não confirmado para CDB bancário de varejo.
3. **Fórmula exata de "IPCA diário" (pro rata do índice mensal)** — não
   há documento normativo público especificando a interpolação usada por
   instituições para exibir rendimento diário de título IPCA+.
4. **Mecanismo e horário exatos de processamento/armazenamento do
   cálculo** (job noturno, momento do dia, etc.) — hipótese plausível por
   analogia com marcação diária de cota de fundos, mas sem fonte que
   confirme isso especificamente para CDB/conta de pagamento, nem o
   horário.
5. **Comparação sistemática entre instituições e tipos de produto** quanto
   à frequência/momento de atualização do valor de rendimento exibido —
   nenhuma fonte compara isso diretamente entre Nubank, Inter, Mercado
   Pago, PicPay e corretoras tradicionais.

Essas lacunas reforçam que a decisão de manter entrada manual no MVP
(seção 7 do `analise-requisitos.md`) continua bem fundamentada: os pontos
mais sensíveis para gerar um número errado (tratamento de fim de semana e
base de dias do prefixado) são exatamente os que não têm confirmação
técnica sólida disponível publicamente.

---

## Fontes consultadas

### Ponto 1 — Fonte e obtenção da taxa
- [Dicionário Financeiro - Taxa DI](https://www.dicionariofinanceiro.com.br/taxa_di)
- [Wikipédia - Certificado de Depósito Interbancário](https://pt.wikipedia.org/wiki/Certificado_de_Dep%C3%B3sito_Interbanc%C3%A1rio)
- [Crédito e Mercado - Entendendo o DI Futuro](https://www.creditoemercado.com.br/blogconsultoriaeminvestimentos/?p=2024)
- [Portal de Dados Abertos do BCB - Taxa de juros Selic](https://dadosabertos.bcb.gov.br/dataset/11-taxa-de-juros---selic)
- [Portal de Dados Abertos do BCB - Selic anualizada base 252](https://dadosabertos.bcb.gov.br/dataset/1178-taxa-de-juros---selic-anualizada-base-252)
- [python-bcb - documentação SGS](https://wilsonfreitas.github.io/python-bcb/sgs.html)
- [bcbpy - PyPI](https://pypi.org/project/bcbpy/)
- [Gist - códigos de séries SGS do BCB](https://gist.github.com/wesleyit/b2f1d25f3f0200779a9a7399fa6ea344)

### Ponto 2 — Fórmula do fator diário
- [Fabric Community - Calculating a factor (fórmula com expoente 1/252)](https://community.fabric.microsoft.com/t5/DAX-Commands-and-Tips/Calculating-a-factor/m-p/2958408)
- [Mycon - Como calcular o rendimento do CDI](https://www.mycon.com.br/blog/post/como-calcular-o-rendimento-do-cdi)
- [Exame - Quanto rende R$1 milhão a 100% do CDI por dia](https://exame.com/invest/guia/quanto-rende-r-1-milhao-a-100-do-cdi-por-dia/)
- [Yumpu - documento acadêmico sobre precificação NTN-B, base 252 dias úteis](https://www.yumpu.com/pt/document/view/47282928/t-susep/7)
- [Calculadora do Cidadão do BCB - metodologia pro rata (Taxa Legal)](https://www3.bcb.gov.br/CALCIDADAO/publico/metodologiaCorrigirPelaTaxaLegal.do?method=metodologiaCorrigirPelaTaxaLegal)
- [calculadora-renda-fixa.com](https://calculadora-renda-fixa.com/)
- [calculosonline.com.br - Calculadora de CDB](https://calculosonline.com.br/calculadora/cdb)

### Ponto 3 — Fim de semana e feriados
- [Comunidade Nubank - "Caixinha não rende 100% do CDI"](https://comunidade.nubank.com.br/discussoes/post/caixinha-nao-rende-100-do-cdi-rgemLAXn8esQ9lN)
- [Ajuda Inter - Como resgato um CDB Liquidez Diária](https://ajuda.inter.co/investimentos/como-resgato-um-cdb-liquidez-diaria)
- [Yumpu - documento acadêmico sobre NTN-B, base 252 (mesma fonte do ponto 2)](https://www.yumpu.com/pt/document/view/47282928/t-susep/7)

### Ponto 4 — Momento e armazenamento do cálculo
- [Blog Genial Investimentos - O que é marcação a mercado](https://blog.genialinvestimentos.com.br/o-que-e-marcacao-a-mercado/)
- [Toro Investimentos - Marcação a mercado na renda fixa](https://blog.toroinvestimentos.com.br/renda-fixa/marcacao-a-mercado/)
- [Investidor Sardinha - Marcação a mercado (MaM)](https://investidorsardinha.r7.com/aprender/marcacao-a-mercado-mam/)
- [Sicredi - Marcação a Mercado](https://www.sicredi.com.br/site/blog/investimentos/marcacao-mercado-voce-sabe-o-que-e-e-como-ela-age-nos-investimentos/)
- [Wikipédia - Fundo de investimento (valor da cota diário)](https://pt.wikipedia.org/wiki/Fundo_de_investimento)
- [CNN Brasil - Novo horário de negociação da B3](https://www.cnnbrasil.com.br/economia/mercado/b3-tem-novo-horario-de-negociacao-a-partir-de-segunda-feira-10/)

### Ponto 5 — Imposto de Renda e IOF
- [Tesouro Direto (oficial) - Quais são os impostos e taxas ao investir no TD](https://www.tesourodireto.com.br/blog/quais-sao-os-impostos-e-taxas-ao-investir-no-td.htm)
- [Decreto nº 6.306/2007 - Regulamento do IOF (Planalto, fonte primária oficial)](https://www.planalto.gov.br/ccivil_03/_ato2007-2010/2007/decreto/d6306.htm)
- [ValorFinal - Tabela IOF Renda Fixa (com exemplo numérico e ordem de incidência)](https://valorfinal.com.br/tabela-iof-renda-fixa)
- [Rankia - Imposto de Renda CDB: Tributação e Cálculo](https://rankia.com.br/imposto-de-renda-cdb/)
- [Renova Invest - Imposto de Renda no CDB](https://renovainvest.com.br/blog/imposto-de-renda-cdb/)
- [calculadora-renda-fixa.com](https://calculadora-renda-fixa.com/)

### Ponto 6 — Variação entre instituições e produtos
- [Nubank - Caixinha Turbo (condição de manutenção do percentual do CDI)](https://www.nubank.com.br/nu/conta/caixinha-turbo)
- [Comunidade Nubank - "Caixinha não rende 100% do CDI" (mesma fonte do ponto 3)](https://comunidade.nubank.com.br/discussoes/post/caixinha-nao-rende-100-do-cdi-rgemLAXn8esQ9lN)
- [Ajuda Inter - Como resgato um CDB Liquidez Diária (mesma fonte do ponto 3)](https://ajuda.inter.co/investimentos/como-resgato-um-cdb-liquidez-diaria)

---

## Observação final sobre a decisão de manter entrada manual

Esta pesquisa não encontrou motivo para reverter a decisão de manter
entrada manual no MVP. Pelo contrário: o ponto mais crítico para a
correção do cálculo (o tratamento de fins de semana/feriados no
pós-fixado, e a base de dias no prefixado) permanece sem confirmação
técnica sólida mesmo após pesquisa aprofundada em múltiplas fontes. Essa
decisão sobre se e como atualizar `analise-requisitos.md` é deliberada e
cabe a você revisar separadamente, conforme já registrado em
`prompts/09-pesquisa-calculo-rendimento.md`.
