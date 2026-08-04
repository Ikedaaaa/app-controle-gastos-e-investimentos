# Estrutura das Anotações Mensais de Gastos

Este documento descreve como as anotações mensais de gastos estão organizadas
nos arquivos de referência. Serve como guia de leitura e como base para o
levantamento de requisitos do app.

---

## Separação por período

Cada mês é dividido em dois períodos de gasto:

- **Adiantamento Quinzenal** — recebido na metade do mês, cobre os últimos 15
  dias daquele mês.
- **Salário** — recebido no fim do mês, cobre os primeiros 15 dias do mês
  seguinte.

Exemplo: o salário recebido no último dia do mês A é usado para os gastos dos
primeiros 15 dias do mês B.

Os arquivos são nomeados pelo mês de referência do gasto (não pelo mês de
recebimento). Cada arquivo pode conter dois blocos: Adiantamento e Salário.

Os meses no arquivo original são separados pela sequência `$$$$$$$$$$$$$$$$$$$$$$$$$$`.

---

## Seção de resumo de gastos (cabeçalho do período)

No início de cada período existe uma seção com os gastos previstos agrupados por
categoria:

```
*************** Gastos****************
R$xxx,xx (Gasto A)
*************** Crédito ***************
R$xxx,xx (Gasto B)
************** Acúmulo **************
R$xxx,xx (Cx. Objetivo X) +*
************ Investimento ************
R$xxx,xx (Investimento Y) +
R$xxx,xx (Investimento Z) +*
```

Esta seção era atualizada manualmente no início, mas foi abandonada com o tempo.
Hoje é copiada do mês anterior e não reflete os valores reais — serve apenas
como estrutura de referência. No app, esse resumo seria gerado automaticamente.

Os símbolos ao lado dos valores têm significado informal:
- `+` — gasto confirmado/realizado
- `*` — gasto previsto ou pendente
- `+*` — artefato do processo de atualização: o gasto era pendente (`*`) e foi
  realizado, mas ao invés de substituir `*` por `+`, apenas remove-se o `*`.
  Enquanto não removido, aparece como `+*`. Não é um estado intencional.

---

## Seção de rendimentos previstos

```
Previsto rendimentos: R$xxx,xx (xxx,xx [Adiantamento] + xxx,xx [Saldo Remanescente] + xxx,xx [Fonte Extra A] + xxx,xx [Fonte Extra B])
```

Lista todas as fontes de entrada do período. O valor total deveria ser a soma
dos valores entre parênteses, mas frequentemente não está atualizado por
exigir cálculo manual.

Fontes de entrada possíveis:
- Salário
- Adiantamento Quinzenal
- Saldo Remanescente do período anterior
- Pagamento de Férias
- 13º Salário
- PLR
- Reembolsos
- Depósitos de terceiros (custódia temporária)

Algumas fontes são somadas ao montante principal e trabalhadas juntas. Outras
são mantidas separadas para decisão independente de alocação (ex: férias e 13º
tratados em blocos próprios).

---

## Fluxo de subtração do montante

O núcleo de cada período é um cálculo em cascata: parte do valor recebido e vai
subtraindo cada gasto, investimento ou aporte, mostrando o saldo após cada
dedução.

```
xxx,xx (Salário)
- xxx,xx (Investimento Y)
= xxx,xx
- xxx,xx (Cx. Objetivo X)
= xxx,xx
- xxx,xx (Gasto A)
= xxx,xx
...
= xxx,xx (Saldo Remanescente)
```

Gastos recorrentes (investimentos fixos, contas mensais) aparecem sempre no
topo da lista, pois são executados assim que o dinheiro entra. Valores como
investimentos atrelados a moeda estrangeira variam levemente, mas a estrutura
se repete.

O saldo remanescente ao final pode ser zero (planejamento fechado) ou positivo
(sobra que transita para o próximo período como "Saldo Remanescente").

---

## Contas e locais onde o dinheiro está

Além do fluxo principal, algumas contas têm seu saldo acompanhado
individualmente:

- **Conta Secundária A** — conta mantida com saldo fixo alvo. Registra
  entradas, saídas e rendimentos do período.
- **Saldo Separado (Conta Principal)** — funcionalidade usada para isolar
  dinheiro da conta principal e evitar mistura de valores com finalidade
  específica.
- **Conta Externa B** — conta em outra instituição, acompanhada separadamente,
  com histórico de entradas e saídas relevantes.

---

## Caixinhas e carteiras de acúmulo/investimento

Representadas como `Cx. Nome: { ... }`. São objetivos de acúmulo ou carteiras
de investimento. Cada caixinha lista seus aportes individuais com:

- Valor aplicado
- Data de aplicação
- Data de vencimento
- Instituição e tipo de produto (CDB, LCI, Caixinha, etc.)
- Rendimento percentual
- Saldo atual (quando atualizado)

Exemplos genéricos de carteiras:
- `Cx. Objetivo A` (objetivo de médio prazo)
- `Cx. Objetivo B` (objetivo de médio prazo)
- `Cx. Acúmulo Mensal` (sem detalhamento por aporte)
- `Cx. Reserva Compra Recorrente` (acúmulo para compra recorrente)
- `Cx. Fatura` (reserva para pagar fatura do cartão)
- `Reserva de Emergência` (carteira de renda fixa diversificada)
- `Carteira Terceiro` (carteira de terceiro administrada pelo usuário)
- `Carteira Investimentos` (investimentos em renda variável)

O separador `########################` dentro de uma caixinha indica o último
aporte incluído naquele mês — os aportes abaixo da linha foram adicionados no
período atual.

A caixinha inteira é copiada e colada a cada mês porque o bloco de notas não
tem persistência — sem essa duplicação, seria impossível saber o estado atual
da carteira. Essa é uma das principais fontes de redundância e inconsistência.

**Exceção:** Uma das caixinhas de acúmulo não é detalhada aporte por aporte
por ser grande demais. Registra apenas o total acumulado e o aporte do período.

---

## Explicação de gastos compostos

Alguns gastos precisam ser detalhados porque o valor total sai de múltiplas
fontes. Esses blocos aparecem como:

```
Pagar conta XXX:
+ xxx,xx (Parte Terceiro) [Parcela N/Total]
= xxx,xx (Total parte terceiro)
Minha parte:
+ xxx,xx (Cx. Fatura - Item A)
+ xxx,xx (Cx. Fatura - Item B)
= xxx,xx
+ xxx,xx (Compl minha parte)
= R$xxx,xx
```

A fatura do cartão tem uma parte de terceiro (resgatada da carteira do
terceiro) e uma parte própria (composta por resgates de caixinhas específicas
+ complemento da conta corrente).

Outros exemplos de gastos compostos:
- Composição da fatura de outro cartão item a item
- Compra de alto valor custeada por resgates de múltiplos aportes de uma carteira

---

## Carteira de terceiro

Carteira administrada em nome de um terceiro. O terceiro deposita dinheiro que
é aplicado em produtos de renda fixa. O valor é resgatado parcialmente mês a
mês para cobrir gastos do terceiro que passaram pelo cartão do usuário.

```
Carteira Terceiro:
(Aplicação que rende X% ao ano) {
+ xxx,xx (Depósito para finalidade Y) {
    = xxx,xx
    + (Rendimento)
    = xxx,xx
     - xxx,xx (Resgate para cobrir gasto Z)
    = xxx,xx
   } [Apl dd/mm/aaaa]
}
```

O terceiro pode ter aportes em produtos diferentes e os resgates precisam ser
rastreados por aporte individual, pois cada produto tem liquidez e rendimento
diferentes.

---

## Problema de rastreabilidade de resgates

Em algumas instituições é possível escolher de qual aporte individual sai o
resgate parcial. Em outras isso não é possível — o sistema escolhe
automaticamente e não informa quanto saiu de cada aporte.

Isso pode gerar entradas com `xxx,xx` nas notas: quando não foi possível saber
o valor exato resgatado de cada aporte individual de uma carteira.

---

## Entradas de terceiros sem carteira permanente

Casos em que dinheiro de terceiros entra na conta, é aplicado/usado e devolvido,
sem criar carteira permanente. São transações pontuais de custódia que precisam
de registro mas não de acompanhamento contínuo.

---

## Problema central: duplicidade e falta de persistência

O bloco de notas não tem memória. Para manter o controle do estado atual de
cada carteira, o usuário precisa copiar e colar o bloco inteiro a cada mês,
gerando:

- Dados duplicados entre vários meses
- Risco de inconsistência ao atualizar um mês e esquecer de atualizar outro
- Impossibilidade de ver histórico de evolução de uma carteira
- Necessidade de fazer todos os cálculos manualmente
- Nenhuma visão analítica (gráficos, tendências, comparações)

O app resolve isso mantendo os dados uma única vez e calculando tudo
automaticamente — exatamente o princípio de normalização de banco de dados.
