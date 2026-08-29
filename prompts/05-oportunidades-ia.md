# Oportunidades genuínas de IA

No treinamento original, este passo buscava oportunidades de IA "Dirigida"
e "Delegada" para reposicionar um produto no mercado. Aqui a motivação é
mais simples: o `analise-requisitos.md` já mapeia funcionalidades futuras
(seção 17) e já descartou explicitamente uma automação (extração de dados
do app do banco, seção 7). Este prompt serve só para checar se existe algo
genuinamente útil de IA para o seu app pessoal que ainda não foi
considerado — sem forçar IA onde uma regra simples ou um gráfico já bastam.

---

## Prompt

```
Você é um consultor técnico cético, especializado em distinguir
oportunidades genuínas de IA de automação ou analytics disfarçada de IA.
Sua postura padrão é desconfiar de qualquer sugestão de "IA" até que ela
prove que exige de fato interpretação de padrão, linguagem natural, ou
inferência sobre dado incompleto — não aceite pelo simples fato de envolver
dados ou cálculo.

Quero checar se existe alguma oportunidade genuína de IA para o meu app
pessoal de controle financeiro que ainda não está registrada no
docs/analise-requisitos.md.

Leia o documento inteiro primeiro, especialmente a seção 17 (funcionalidades
futuras) e a seção sobre automação de coleta de dados do banco (seção 7, já
avaliada e descartada).

Antes de sugerir qualquer coisa, aplique este filtro: se uma query SQL e um
gráfico resolvem, não é IA, é analytics — não sugira. Se uma checklist ou
regra simples ("se valor > X, avisar") resolve, não é IA, é automação —
não sugira. Só sugira algo que exija de fato interpretação de padrão,
linguagem natural, ou inferência sobre dado incompleto/ambíguo.

Considerando os dados que o app vai ter (lançamentos categorizados, valores,
datas, carteiras, aportes — ver seção 5 e 7), pense em:

1. Categorização automática de gastos a partir da descrição livre (texto
   digitado pelo usuário, sem categoria fixa — ver seção 3 sobre descrição
   livre)
2. Detecção de padrão anômalo (um gasto muito fora do histórico da mesma
   categoria) — isso é genuinamente IA ou é só um desvio-padrão calculado?
   Seja honesto se for o segundo caso
3. Qualquer outra oportunidade que você identificar, aplicando o mesmo
   filtro rigoroso

Para cada sugestão: diga se passa no filtro (IA de fato) ou não (regra/
analytics disfarçada), e se é MVP ou fica para depois — comparando com o que
já está nas seções 17 e "Escopo do MVP" do documento.

Se nada passar no filtro além do que já está documentado, diga isso
explicitamente — não force uma sugestão para preencher a etapa.

Salve o resultado em docs/discovery/05-oportunidades-ia.md.
```

---

**Verificação de conclusão:** você tem uma lista curta (ou vazia, se for o
caso honesto) de oportunidades de IA que passam no filtro rigoroso, sem
duplicar o que o `analise-requisitos.md` já cobre.
