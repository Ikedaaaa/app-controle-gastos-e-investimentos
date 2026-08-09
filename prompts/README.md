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
  - [ ] Conclusões objetivas extraídas para `docs/discovery/05-conclusoes-roleplay.md`
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
