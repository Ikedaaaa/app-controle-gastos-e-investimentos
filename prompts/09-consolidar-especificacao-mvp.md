# Consolidar especificação de implementação do MVP

Este prompt **faz parte da sequência principal** — é a etapa 9, a etapa
final, executada **depois** do protótipo (`08`) estar concluído e das
decisões de UI estarem fechadas. Diferente dos prompts `10` e `11`
(pesquisa técnica sobre um tópico pontual, standalone, fora desta
sequência), este prompt não investiga nada novo — ele **destila** decisões
já tomadas em `docs/analise-requisitos.md` e nas etapas 06-08 para um
formato voltado à implementação real do app em Kotlin, marcando a
transição entre a fase de planejamento e o início do desenvolvimento.

## Por que este prompt existe

`docs/analise-requisitos.md` cumpre hoje dois papéis ao mesmo tempo:

1. **Registro histórico do raciocínio** — o que foi considerado e
   descartado, correções de versões anteriores do próprio documento,
   descobertas preliminares não validadas, justificativas de por que uma
   regra existe.
2. **A especificação do que construir** — o que cada entidade precisa ter,
   quais regras valem.

Isso é adequado para a fase de decisão (ajuda a não reabrir debate
encerrado, preserva o porquê de cada escolha), mas se torna atrito na hora
de codificar: implementar uma tela exige peneirar constantemente "isso é a
decisão final ou é o caminho até ela?" em meio a parágrafos que narram
correções e alternativas descartadas antes de chegar à regra válida.

Este prompt gera um documento derivado, com propósito e formato diferentes
do original — voltado a quem vai codificar, não a quem está decidindo.

## Pré-requisitos antes de rodar este prompt

- `docs/discovery/07-conclusoes-roleplay.md` existente (decisões de UI
  testadas contra a persona)
- Protótipo (`prototype/`) concluído, com `prototype/COMPARACAO.md`
  preenchido e uma direção de design escolhida entre Versão A e Versão B
  (ou uma combinação das duas, decidida manualmente após a comparação)
- Sem pendência conhecida de requisito ainda em aberto que mude
  estruturalmente uma entidade (pendências de UI pura, ou casos de borda
  já registrados como "sem resposta conclusiva" em pesquisas como `09`/`10`,
  não bloqueiam — ver seção "O que fazer com pendências ainda abertas"
  abaixo)

Se algum desses não existir ainda, complete antes de rodar este prompt —
consolidar um MVP em movimento (com decisão de UI ainda não fechada)
recriaria o mesmo problema de manutenção duplicada que este prompt existe
para evitar.

---

## Prompt

```
Você vai gerar um documento consolidado de especificação de implementação
do MVP deste app pessoal de controle financeiro, destilando decisões já
tomadas em docs/analise-requisitos.md para um formato direcionado a quem
vai codificar — não a quem está decidindo.

Leia, nesta ordem:
1. docs/analise-requisitos.md (documento completo, fonte de todas as
   decisões de requisito e regra de negócio)
2. docs/discovery/07-conclusoes-roleplay.md (decisões de UI confirmadas ou
   ajustadas após o role-play)
3. docs/discovery/06-alternativas-ui.md (decisões de UI recomendadas, para
   pontos que não tiveram ajuste específico no role-play)
4. prototype/COMPARACAO.md (comparação entre as duas versões do protótipo)
5. Os NOTAS.md de cada versão do protótipo
   (prototype/src/versao-a-livre/NOTAS.md e
   prototype/src/versao-b-influenciada/NOTAS.md), que registram o que foi
   assumido, simplificado, ou mal representado durante a construção do
   protótipo

## Regras de destilação

- **Só o estado final decidido, nunca a narrativa de como se chegou lá.**
  Não reproduza frases como "consideramos e descartamos", "correção sobre
  a versão anterior", "descobertas preliminares não validadas". Se uma
  seção do analise-requisitos.md narra uma correção antes de chegar à
  definição válida, o documento consolidado deve conter apenas a definição
  válida — sem o histórico do erro anterior.
- **Escopo limitado ao MVP.** Use a seção "Escopo do MVP" do
  analise-requisitos.md como filtro principal. Funcionalidades pós-MVP
  entram apenas como uma lista curta de referência ao final (nome +
  uma frase, sem detalhamento de regra), não como seção desenvolvida —
  quem precisar do detalhe completo de algo pós-MVP volta ao
  analise-requisitos.md.
- **Organize por domínio/entidade, não por ordem cronológica de
  descoberta.** O documento original segue a ordem em que os requisitos
  foram descobertos (Período, depois Entradas, depois Fluxo...). Este
  documento deve agrupar por natureza:
  1. Modelo de dados (todas as entidades do MVP, campos, relações, tipos —
     incluindo a regra de centavos inteiros vs. BigDecimal da seção 23)
  2. Regras de cálculo (cascata de saldo, painel analítico, parcelamento,
     rateio, etc.)
  3. Regras de UI/interação já decididas (gestos, navegação, layout — a
     partir das fontes de discovery listadas acima, não do
     analise-requisitos.md, que não é a fonte de verdade para UI)
  4. Fora de escopo do MVP (lista curta, sem detalhamento)
- **Sempre referencie a seção de origem no analise-requisitos.md**, para
  cada regra ou entidade (ex: "ver analise-requisitos.md, seção 9, para o
  raciocínio completo por trás desta regra"). O documento consolidado não
  substitui o original — é um resumo de trabalho que sempre permite
  voltar à fonte quando o porquê de uma decisão precisar ser revisitado.
- **Não invente nem infira nada que não esteja decidido.** Se uma pendência
  ainda estiver aberta (ex: caso de borda sem resposta conclusiva do prompt
  `10`), registre-a explicitamente como pendência dentro do domínio
  correspondente, não tente resolvê-la nem omiti-la silenciosamente.
- **Prefira tabelas e listas a texto corrido**, sempre que a informação for
  estruturada por natureza (campos de entidade, regras de cálculo com
  entrada/saída definidas) — o formato deve favorecer consulta rápida
  durante a implementação, não leitura linear.

## Formato esperado

Um único documento markdown, com sumário no topo, organizado pelos quatro
blocos de domínio listados acima. Cada entidade do modelo de dados deve
aparecer com uma tabela de campos (nome, tipo, obrigatório, observação) e
suas relações com outras entidades.

Salve o resultado em docs/especificacao-mvp.md.
```

---

## Depois de gerado (manual, não parte do prompt)

Revise o documento com atenção ao que mais importa numa especificação de
implementação: nenhuma regra de negócio real deve ter sido simplificada a
ponto de perder precisão (ex: o algoritmo de rateio de resto de parcela
precisa continuar exato, mesmo resumido). Se encontrar imprecisão, corrija
pontualmente — não peça regeneração do documento inteiro.

Este documento não substitui `docs/analise-requisitos.md`, que continua
sendo a fonte de verdade e o histórico de decisões. `docs/especificacao-mvp.md`
é um artefato derivado, útil enquanto o MVP estiver sendo implementado —
se o app evoluir significativamente após o MVP, um novo documento
consolidado (ou uma atualização deste) pode ser gerado repetindo o mesmo
processo.

---

**Verificação de conclusão:** você tem um documento novo
(`docs/especificacao-mvp.md`) organizado por domínio (modelo de dados,
cálculo, UI, fora de escopo), sem narrativa histórica, com referências de
volta ao `analise-requisitos.md` para contexto, cobrindo exatamente o
escopo do MVP já validado pelo protótipo.
