# Controle de Gastos e Investimentos

App pessoal de controle financeiro e patrimônio, feito para uso próprio e exclusivo — sem pretensão de ser um produto para o público em geral.

> **Objetivo final:** parar de usar o bloco de notas.

**Status atual:** planejamento e levantamento de requisitos. Nenhuma linha de código foi escrita ainda — este repositório contém, por enquanto, apenas documentação de análise, requisitos e roadmap.

---

## Por que este projeto existe

Há mais de 3 anos eu controlo minhas finanças pessoais inteiramente num bloco de notas: renda, gastos, cartões, investimentos em renda fixa, carteiras de objetivo, dinheiro administrado para terceiros — tudo em texto puro, calculado à mão.

Isso funciona, mas tem um custo real:

- Todo cálculo é feito manualmente, do zero, todo mês
- Toda carteira de investimento precisa ser copiada e colada mês a mês só para manter o histórico visível
- Não existe nenhuma visão analítica — sem gráficos, sem evolução, sem comparação
- O risco de inconsistência é constante (esquecer de atualizar um valor em algum lugar)

Já tentei alguns apps de controle financeiro prontos, mas nenhum se adequou à forma como eu realmente penso e organizo meu dinheiro — principalmente o nível de granularidade que uso para rastrear cada aporte individual de cada carteira, de onde vem cada centavo, e a composição detalhada de gastos como faturas de cartão.

O gatilho definitivo para começar este projeto: o arquivo de anotações mensais já teve que ser dividido duas vezes por simplesmente ficar grande demais para o editor de texto aguentar.

Este projeto nasce de um levantamento de requisitos extraído diretamente das minhas anotações reais dos últimos anos — não é um app financeiro genérico, é a tentativa de automatizar um método de controle financeiro que já existe e funciona, só que hoje é inteiramente manual.

## O que este app pretende fazer

- Substituir o bloco de notas como ferramenta de controle financeiro mensal
- Automatizar o fluxo de "recebi X, vou subtraindo cada gasto/investimento, vejo quanto sobra" — hoje feito por cálculo manual
- Suportar múltiplas fontes de renda, cartões de crédito, composição de faturas com múltiplas fontes de pagamento
- Modelar carteiras de investimento com aportes individuais, rastreáveis por instituição, prazo e rendimento
- Suportar múltiplas carteiras com objetivos e titularidades diferentes
- Eventualmente, evoluir para um módulo completo de investimentos em renda variável, gráficos de evolução patrimonial e sincronização em nuvem

Este é, deliberadamente, um app complexo — porque o problema real que ele resolve é complexo. A filosofia de design central é **flexibilidade acima de tudo**: o app nunca deve ser mais rígido do que o bloco de notas que está substituindo.

## Documentação

Toda a análise e planejamento deste projeto está documentada em [`docs/`](docs/):

| Documento | Conteúdo |
|---|---|
| [`analise-requisitos.md`](docs/analise-requisitos.md) | Levantamento completo de requisitos funcionais, extraído da análise de anos de anotações reais. Cobre fluxo de gastos, carteiras, faturas, composição de gastos, notificações e mais. |
| [`estrutura-anotacoes.md`](docs/estrutura-anotacoes.md) | Descrição de como as anotações manuais originais eram estruturadas — a base histórica que orientou o levantamento de requisitos. |
| [`sugestoes-ui-navegacao.md`](docs/sugestoes-ui-navegacao.md) | Ideias e sugestões de interface e navegação, explicitamente não vinculantes — ponto de partida para a fase de design de telas. |
| [`Finance_App_Roadmap.md`](docs/Finance_App_Roadmap.md) | Roadmap técnico de aprendizado e desenvolvimento, em fases incrementais. |

Os dados financeiros reais que originaram esta análise (anotações mensais pessoais) não são versionados neste repositório por conterem informações sensíveis.

## Metodologia de refinamento

A pasta [`prompts/`](prompts/) contém uma sequência estruturada de prompts para refinar decisões de UI/UX ainda abertas e construir um protótipo navegável, adaptada da metodologia usada no treinamento "AI Accelerator" (mantido como referência, intocado, em `reference-files/ai-accelerator-training/`). Ver [`prompts/README.md`](prompts/README.md) para a ordem de execução e o que difere da metodologia original.

## Stack planejada

- **Mobile:** Kotlin + Jetpack Compose (Android nativo)
- **Persistência local:** Room (SQLite), offline-first
- **Backend (futuro):** Spring Boot + PostgreSQL
- **Sincronização (futuro):** arquitetura offline-first com sincronização em background
- **Web administrativa (futuro, aprendizado):** React

Ver o roadmap completo em [`docs/Finance_App_Roadmap.md`](docs/Finance_App_Roadmap.md) para as fases de desenvolvimento planejadas.

## Escopo do MVP

O critério de sucesso do MVP é simples: conseguir fechar um mês inteiro de controle financeiro sem precisar abrir o bloco de notas.

Resumo do que está planejado para a primeira versão (detalhes completos em [`docs/analise-requisitos.md`](docs/analise-requisitos.md)):

- Períodos mensais/quinzenais com múltiplas fontes de renda
- Fluxo de gastos com subtração em cascata e recálculo automático de saldo
- Carteiras com aportes individuais, resgates e histórico
- Composição de faturas com múltiplas fontes de pagamento
- Gastos recorrentes como sugestão automática
- Bloqueio de app por PIN/biometria
- Persistência local, totalmente offline

Notificações, múltiplos cartões com automação completa, gráficos, exportação e sincronização com backend ficam para versões seguintes.

## Sobre este repositório

Este é um projeto pessoal e também um projeto de estudo — aprendizado de Kotlin, arquitetura Android moderna, Spring Boot e sincronização offline-first, aplicados a um problema financeiro real que eu mesmo tenho.

A fase de levantamento de requisitos foi conduzida em conjunto com IA (Kiro), a partir da análise de anos de anotações financeiras reais — não é um app genérico gerado por um prompt único, mas o resultado de várias sessões de discussão, questionamento e refinamento de cada decisão de modelagem. O histórico completo desse processo está refletido no nível de detalhe dos documentos em [`docs/`](docs/).

O refinamento das decisões de UI e a construção do protótipo seguem uma metodologia própria, adaptada de um treinamento corporativo de ideação com IA — ver [`prompts/`](prompts/).
