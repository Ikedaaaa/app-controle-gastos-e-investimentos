# Setup

**Execute isto primeiro, no início de uma nova sessão de Kiro para este trabalho.**

Copie o prompt abaixo para o Kiro.

---

```
Você vai me ajudar a refinar decisões de produto e UI para o meu app pessoal
de controle de gastos e investimentos, usando uma metodologia adaptada de um
treinamento de ideação com IA. Antes de começar, se contextualize.

## Etapa 1: Leia o contexto do projeto

Leia, na ordem:
- docs/analise-requisitos.md — documento consolidado de requisitos. Esta é a
  fonte de verdade sobre o que o app deve fazer. Já cobre modelagem de dados,
  regras de negócio e decisões de escopo do MVP.
- docs/Finance_App_Roadmap.md — trilha de desenvolvimento geral, para saber
  em que fase o projeto está.
- prompts/README.md — explica por que esta sequência de prompts existe e o
  que difere do treinamento original.

## Etapa 2: Identifique o que está genuinamente aberto

O documento de requisitos já é maduro — a maioria das decisões de modelagem
de dados e regra de negócio já está fechada. O que resta para os próximos
prompts é principalmente decisão de UI/UX (como apresentar em tela algo que
já está definido no back-end) e alguns pontos que o próprio documento marcou
explicitamente como "a definir no design" ou "sugestão de UI, decisão
pendente".

Liste esses pontos abertos que você encontrou no documento, com a seção de
origem de cada um. Não invente pontos abertos que não estão sinalizados no
texto — se o documento já decidiu algo, não é um ponto aberto.

## Etapa 3: Confirme antes de avançar

Me mostre a lista de pontos abertos e pergunte se falta algo que eu tenha em
mente e que não esteja no documento. Não comece nenhum exercício de ideação
ainda — isso acontece nos prompts seguintes (04, 05, 06).

## Etapa 4: Prepare as pastas de trabalho

Confirme que existem (crie se não existirem):
- docs/discovery/ — para os artefatos versionados desta sequência
- reference-files/discovery/ — para os artefatos não versionados (persona,
  mensagens brutas, diálogo de role-play). Esta pasta já está dentro de
  reference-files/, que está no .gitignore — não precisa de configuração
  adicional.
```

---

**Verificação de conclusão:** você tem uma lista clara e específica dos
pontos que o `analise-requisitos.md` deixou abertos, cada um rastreável a uma
seção do documento. As pastas `docs/discovery/` e `reference-files/discovery/`
existem.
