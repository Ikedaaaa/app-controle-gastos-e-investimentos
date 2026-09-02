# Prompts de refinamento — App de Controle de Gastos e Investimentos

Esta pasta contém uma adaptação pessoal da metodologia usada no treinamento
"AI Accelerator" (ver `reference-files/ai-accelerator-training/apoio/prompts/`,
mantida intocada como referência). Os prompts originais foram desenhados para
grupos descobrindo um problema de negócio do zero. Aqui o objetivo é
diferente: já existe um documento de requisitos consolidado
(`docs/analise-requisitos.md`) para um projeto pessoal — a metodologia serve
para **refinar decisões que ainda estão abertas** (principalmente de UI/UX) e
para produzir, ao final, um protótipo navegável.

Por isso a sequência tem 8 passos em vez de 15, e algumas etapas do original
(analytics de clientes, pesquisa de mercado competitiva completa) foram
removidas ou reduzidas por não se aplicarem a um app de uso pessoal sem
usuários, dados de suporte ou concorrência real a vencer.

## Ordem de execução

1. `01-setup.md` — onboarding do Kiro no contexto do projeto
2. `02-panorama-solucoes-existentes.md` — scan rápido de apps similares (referência de UI/fluxo, não benchmark competitivo)
3. `03-persona.md` — construção da persona (você mesmo) a partir dos requisitos + mensagens suas em tom natural
4. `04-problema-e-hmw.md` — problema central + perguntas "Como poderíamos" focadas nas decisões de UI ainda abertas
5. `05-oportunidades-ia.md` — checagem rápida de oportunidades genuínas de IA não cobertas ainda
6. `06-ideacao-e-priorizacao.md` — geração e priorização de soluções para as decisões abertas
7. `07-persona-roleplay.md` — teste de estresse das decisões escolhidas contra a persona
8. `08-construir-prototipo.md` — construção do protótipo navegável (duas versões, comparadas)

Execute em ordem — cada prompt lê a saída do anterior.

## Mapa de dependências entre artefatos

Diferente do treinamento original, onde os 15 prompts formam uma sequência
estritamente linear (cada um depende só do anterior, como camadas de uma
construção), aqui a estrutura é mais parecida com bases paralelas que
convergem num ponto central, não uma pirâmide de um andar por vez:

```
Fundação (já existia antes de qualquer prompt):
  docs/analise-requisitos.md + docs/sugestoes-ui-navegacao.md
        │
        ▼
01 → docs/discovery/01-pontos-abertos.md ──┐
        │                                   │
        ▼                                   ▼
04 → docs/discovery/04-problema-e-hmw.md    │
        │                                   │
02 → docs/discovery/02-panorama-...md ──────┼──→ 06 → docs/discovery/06-alternativas-ui.md
        │                                   │              │
03 → reference-files/discovery/persona.md ──┘              ▼
        │                                        07 → docs/discovery/07-conclusoes-roleplay.md
        └────────────────────────────────────────────────────┘
                                                              │
                                                              ▼
                                              08 → prototype/ (spec + código)

05 → docs/discovery/05-oportunidades-ia.md — isolado, não entra nessa cadeia
09 → docs/discovery/09-pesquisa-calculo-rendimento.md — standalone, sem relação com os demais
```

Pontos-chave:
- **02, 03 e 05 são independentes entre si** — não dependem de nenhum outro
  prompt gerado, só da fundação. Podem ser rodados em qualquer ordem entre
  eles, ou até em paralelo em sessões diferentes.
- **06 é o nó de convergência** — depende de 01, 02, 03 e 04 ao mesmo tempo,
  sendo o ponto onde as bases paralelas se encontram.
- **05 nunca entra na cadeia principal** — é uma checagem isolada, não
  insumo para nenhum outro prompt.
- **08 é o topo da pirâmide** — depende de 06 e 07 (e da fundação
  diretamente), fechando a sequência com o protótipo navegável.

## Checklist de execução

Marque conforme for concluindo. Para o prompt `08`, marque as sub-etapas
conforme o mapa de sessões definido dentro do próprio arquivo.

- [ ] `01-setup.md`
- [ ] `02-panorama-solucoes-existentes.md`
- [ ] `03-persona.md`
  - [ ] Coleta de mensagens brutas em `reference-files/discovery/persona-mensagens-brutas.md`
  - [ ] Persona gerada
- [ ] `04-problema-e-hmw.md`
- [ ] `05-oportunidades-ia.md`
- [ ] `06-ideacao-e-priorizacao.md`
  - [ ] Alternativas geradas
  - [ ] Priorização feita
- [ ] `07-persona-roleplay.md`
  - [ ] Role-play executado
  - [ ] Conclusões objetivas extraídas para `docs/discovery/07-conclusoes-roleplay.md`
- [ ] `08-construir-prototipo.md`
  - [ ] Sessão 1 — requirements.md
  - [ ] Sessão 1 — design.md (arquitetura compartilhada + Versão A)
  - [ ] Sessão 1 — tasks.md (setup + Versão A) executadas
  - [ ] Sessão 2 (chat novo) — design.md (Versão B)
  - [ ] Sessão 2 — tasks.md (Versão B) executadas
  - [ ] Comparação final (`prototype/COMPARACAO.md`)

## Onde os artefatos são salvos, e o que é versionado

| Artefato | Local | Versionado? |
|---|---|---|
| Panorama de soluções, problema/HMW, oportunidades de IA, decisões de UI priorizadas, conclusões do role-play, protótipo final | `docs/discovery/` e `prototype/` | Sim |
| Mensagens brutas suas (fonte da persona) | `reference-files/discovery/persona-mensagens-brutas.md` | **Não** |
| Persona gerada | `reference-files/discovery/persona.md` | **Não** |
| Diálogo literal do role-play | `reference-files/discovery/roleplay-dialogo.md` | **Não** |

`reference-files/` já está no `.gitignore` do projeto — nada criado ali exige
configuração adicional. A regra geral: se o conteúdo revela como você pensa,
fala ou reage (não só o que o produto faz), fica de fora do controle de
versão.

## Diferenças em relação aos prompts originais

- Sem lógica de grupo/workshop (missão, workspaces por grupo, prework compartilhado)
- Sem análise de analytics/tickets de suporte (não existem dados de uso)
- Pesquisa de mercado reduzida a um panorama de UI/fluxo, não análise competitiva de negócio
- Ideação focada em decisões de UI já sinalizadas como abertas no `analise-requisitos.md`, não em descoberta de features novas (o escopo do MVP já está definido)
- Priorização por clareza de uso x esforço de implementação, não valor de negócio x viabilidade comercial
- Persona construída a partir de mensagens pessoais reais, não de entrevistas com clientes

### Exemplo real validando a remoção da escada "IA Dirigida/Delegada"

Ao rodar o prompt `05` neste projeto, o resultado foi: nenhuma oportunidade
de IA genuína passou o filtro de proporcionalidade (ver
`docs/discovery/05-oportunidades-ia.md`). Isso contrasta diretamente com um
exercício equivalente feito durante o próprio treinamento original, sobre
um sistema de medicina do trabalho: ali, uma IA analisando dados de um
colaborador (histórico de atestados, afastamentos, exames) durante um
atendimento médico *era* uma oportunidade genuína — o médico tinha ~10
minutos por consulta, cruzava dados de terceiros que ele mesmo não gerou, e
a decisão (afastar, liberar, restringir) carregava risco legal e de saúde
real.

As diferenças que explicam os resultados opostos:
- **Volume/dispersão através de pessoas** — o valor da IA no caso médico
  vinha de cruzar padrão entre *vários* colaboradores (ex: "3 pessoas do
  mesmo setor com queixa parecida"). Um app pessoal de usuário único não
  tem população para comparar padrão.
- **Pressão de tempo real** — 30 atendimentos/dia justificam automatizar
  síntese de contexto. Não há urgência equivalente ao consultar os próprios
  dados financeiros.
- **Consequência da decisão** — afastar/liberar um colaborador tem risco
  legal e de saúde. Uma decisão financeira pessoal não carrega esse mesmo
  peso de responsabilidade sobre terceiros.
- **Quem gerou o dado vs. quem precisa interpretá-lo** — o médico interpreta
  dado de terceiros que nunca viu antes. No app pessoal, o usuário é autor
  e interpretador da mesma informação — não há lacuna de contexto a
  preencher.

Isso confirma, com um caso concreto e não só teoricamente, que remover a
escada de maturidade de IA do prompt `05` foi a adaptação certa: o critério
original não estava errado, só não se aplica a este domínio.
