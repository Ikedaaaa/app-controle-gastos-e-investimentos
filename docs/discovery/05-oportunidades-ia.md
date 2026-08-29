# Oportunidades genuínas de IA

Checagem cética sobre `docs/analise-requisitos.md` (seções 1 a 23, com foco
na seção 5 — categorias, seção 7 — carteiras/caixinhas e automação de coleta
já descartada, seção 15 — gráficos e análise, e seção 17 — funcionalidades
futuras) para identificar oportunidades de IA que ainda não estão
documentadas.

**Filtro aplicado a cada sugestão:** se uma query SQL e um gráfico resolvem,
é analytics. Se uma checklist ou regra simples resolve, é automação. Só
passa o filtro o que exige interpretação de padrão ambíguo, linguagem
natural real, ou inferência sobre dado incompleto.

---

## 1. Categorização automática a partir da descrição livre

**Não passa o filtro — automação/analytics, não IA. Não sugerido para
implementação.**

A descrição do item (seção 3) é texto livre, mas a **categoria** (seção 5)
não é um rótulo temático de mercado/tipo de gasto (o que exigiria
interpretar linguagem natural variada, como "alimentação" vs "transporte").
As categorias documentadas — Gasto fixo, Gasto variável, Investimento,
Acúmulo, Fatura, Transferência, Custódia de terceiros — são **classificações
estruturais do mecanismo da transação**, não do seu conteúdo semântico. O
próprio usuário já sabe, no momento em que registra o item, se é um aporte
em carteira, um pagamento de fatura ou uma transferência — não é uma
informação que precise ser inferida a partir do texto digitado.

A peça de dado mais próxima de "categorização por conteúdo" são as **tags
livres** (seção 3 — ex: `roupas`, `shopee`). Aqui existe uma tentação real
de propor "sugestão automática de tag pela descrição", mas isso não exige
IA de fato: é um app de uso pessoal e exclusivo, com vocabulário repetitivo
e consistente (o próprio usuário escrevendo do próprio jeito, mês após mês
— "mercado", "uber", "netflix" tendem a aparecer sempre com grafia
parecida). Um lookup histórico simples ("já vi essa descrição ou uma
parecida antes, e as tags usadas foram X e Y — sugerir de novo") resolve a
esmagadora maioria dos casos sem nenhuma interpretação semântica real —
é busca por similaridade textual sobre um histórico pequeno, não
compreensão de linguagem. Isso é automação/analytics com nome bonito, não
IA. Não incluído como sugestão.

## 2. Detecção de padrão anômalo (gasto fora do histórico da categoria)

**Não passa o filtro — é estatística descritiva, não IA. Confirmado como
o segundo caso apontado no prompt.**

"Um gasto muito fora do histórico da mesma categoria" é literalmente um
cálculo de desvio-padrão (ou z-score) sobre os valores históricos daquela
categoria, com um limiar de corte (ex: "avisar se o valor está a mais de 2
desvios-padrão da média"). Não há interpretação de padrão ambíguo — é uma
fórmula fechada aplicada a números. Isso já está, em espírito, coberto pela
seção 15 (Gráficos e análise, pós-MVP) e pode ser adicionado ali como um
destaque visual simples (ex: marcar em vermelho o item cujo valor
ultrapassa X desvios-padrão da média da categoria) sem qualquer motor de
IA — é uma consulta agregada mais uma regra de threshold.

## 3. Outras oportunidades avaliadas com o mesmo filtro

Duas ideias adicionais tecnicamente cruzam a linha do filtro (exigem
interpretação de linguagem natural ambígua, não apenas cálculo), mas com
ressalvas importantes de proporcionalidade para um app pessoal de uso
exclusivo com baixo volume de dados:

### 3.1. Detecção de gasto recorrente emergente a partir de variações de texto livre
**Passa o filtro tecnicamente, mas prioridade baixa/pós-MVP.**

Diferente do caso 1 (sugerir tag para um item pontual), aqui o objetivo
seria: observar ao longo de vários períodos que descrições
*textualmente diferentes mas semanticamente equivalentes* ("Internet
Vivo", "conta net", "NET - fibra") se repetem com frequência parecida, e
sugerir ao usuário formalizar isso como um gasto recorrente (seção 11) —
algo que hoje ele reconhece mentalmente, mas não é ajudado pelo app a
perceber. Isso exige inferência sobre texto ambíguo e variado (grafias,
abreviações, ordem de palavras diferentes), não um `GROUP BY descricao`
exato. Tecnicamente passa o filtro.

Ressalva: com o volume de dados de um usuário único (dezenas de itens por
período, não milhares), o próprio usuário provavelmente reconhece esses
padrões sozinho antes que qualquer modelo tenha dado suficiente para
inferir algo com confiança. Não está nas seções 11 ou 17 hoje. Registrado
como possibilidade genuína, mas não recomendado para o MVP nem como
prioridade pós-MVP — o custo (manter um motor de similaridade textual) não
parece compensar o ganho para um usuário que já conhece bem seus próprios
gastos.

### 3.2. Consulta em linguagem natural sobre o próprio histórico (notas e descrições ambíguas)
**Passa o filtro tecnicamente, mas valor prático questionável neste app.**

O campo de nota (seção 3) é texto livre sem estrutura, usado para
"cálculos auxiliares, justificativas e contexto". Uma pergunta como
"por que esse mês o gasto com fatura foi tão mais alto?" só é respondível
cruzando o valor com o *conteúdo* de notas e descrições associadas — isso
é genuinamente inferência sobre linguagem natural ambígua, não uma query
estruturada (o dado relevante pode estar só na nota, escrita de forma
livre). Tecnicamente passa o filtro.

Ressalva: o público é o próprio usuário, que escreveu as notas — ele não
precisa de um assistente para interpretar o que ele mesmo escreveu semanas
atrás em geral; o cenário de valor real (esquecer o motivo de uma nota
antiga) é raro. Isso soa mais a uma feature de produto multiusuário
(justificando investimento em NLU) do que a um app pessoal. Não recomendado
para o MVP nem para o backlog no momento — registrado apenas para constar
que foi considerado e descartado por desproporção custo/benefício, não por
falha no filtro técnico.

---

## Conclusão

Nenhuma sugestão deste documento é recomendada para implementação, nem no
MVP nem no backlog pós-MVP atual (seção 17). As duas únicas ideias
propostas no prompt (categorização por descrição e detecção de anomalia)
não passam no filtro — são automação/analytics disfarçadas de IA, e o
gráfico/regra simples correspondente já cabe naturalmente na seção 15
(pós-MVP) sem exigir nada além de SQL e cálculo estatístico.

As duas oportunidades adicionais identificadas (3.1 e 3.2) passam o filtro
técnico — exigem interpretação real de linguagem natural ambígua — mas
falham no teste de proporcionalidade: o app é de uso pessoal exclusivo,
com baixo volume de dados e um único usuário que já é a fonte e o
interpretador natural dos próprios registros. O custo de manter um motor
de interpretação de linguagem natural não se justifica pelo ganho, dado
esse contexto. Não há, hoje, uma oportunidade genuína de IA que valha a
pena registrar como requisito adicional em `docs/analise-requisitos.md`.

---

## Adendo: chatbot de consulta livre sobre os próprios dados

Ideia levantada em conversa após a leitura deste documento: uma
generalização do ponto 3.2 — não só consulta sobre notas/descrições, mas
um chatbot com acesso a todos os dados do app (fluxo, carteiras, aportes),
capaz de responder perguntas livres em linguagem natural (ex: "por que
gastei mais em fevereiro?", "quanto rendeu minha reserva de emergência
esse ano?").

**Esforço de implementação:** isso não é "só" chamar uma API de IA — exige
uma camada de function calling ou RAG, em que o modelo decide quais
consultas rodar contra o banco local (Room/SQLite), executa essas
consultas de forma segura (sem risco de gerar uma query destrutiva ou
incorreta), e só então formata o resultado em linguagem natural. É
arquitetura própria, não uma integração trivial.

**Custo recorrente, não pontual:** diferente dos prompts desta metodologia
de refinamento (executados manualmente, poucas vezes, com custo
controlado por você), um chatbot embutido no app geraria uma chamada de
API a cada pergunta feita no uso diário do app — custo proporcional ao
uso contínuo, não um gasto único de levantamento de requisitos.

**Por que a comparação com um gestor financeiro corporativo não se
aplica:** um gestor financeiro lida com dados dispersos de múltiplas
fontes, muitos usuários, alto volume — cenário onde IA genuinamente
encontra padrão que um humano não perceberia sozinho (é exatamente o
critério de "IA Dirigida/Delegada" do treinamento original, descartado na
adaptação deste projeto por não se aplicar a um app pessoal). Aqui, o
usuário é simultaneamente quem gera o dado, quem mais entende o contexto
dele, e o único consumidor da análise — o "insight" que a IA devolveria
tende a ser algo que o próprio usuário já sabe, ou que um dashboard simples
(soma, filtro, gráfico — seção 15, pós-MVP) já revela sem ambiguidade.

**Conclusão do adendo:** mesmo sendo tecnicamente viável (passa o filtro de
"exige interpretação real"), o esforço de arquitetura somado ao custo
recorrente de uso não se justifica frente ao ganho esperado, dado o baixo
volume de dados e a familiaridade do próprio usuário com seus registros.
Reforça, por um caminho diferente (custo-benefício de arquitetura e
operação, não só o filtro técnico de "é IA de fato"), a mesma conclusão já
registrada acima: não recomendado para o MVP nem para o backlog atual.
