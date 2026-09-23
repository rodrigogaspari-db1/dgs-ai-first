# Histórico de Chat — Cenário 3, Exercício 3.2

**Projeto:** IA-First Certification — DGS (DB1 Global Software)
**Exercício:** 3.2 — Harness de produto para melhoria contínua
**Participante:** Rodrigo (Product Specialist)
**Ferramenta:** Claude (chat)
**Data da sessão:** 2026-09-22
**Entregável gerado:** `harness-produto-v1.0.md`

---

## Linha do tempo do processo ("humano primeiro, IA depois")

| Turno | Quem | O que aconteceu | Fase |
|---|---|---|---|
| 1 | Rodrigo | Envia o enunciado e pede ajuda para definir como realizar o exercício | Entendimento |
| 1 | Claude | Propõe o fluxo de trabalho, com perguntas-guia e estrutura sugerida. **Não gera o documento**, para que Rodrigo faça o rascunho primeiro | Orientação |
| 2 | Rodrigo | Envia o contexto (guardrails v1.1 e proposta 3.1) e o **rascunho próprio (v0)** | **Análise humana** |
| 2 | Claude | Faz a crítica do rascunho contra os critérios do exercício e os artefatos anteriores | Crítica da IA |
| 3 | Rodrigo | Pede revisão do rascunho seguindo as recomendações | Direcionamento humano |
| 3 | Claude | Produz a versão revisada v0.1, assumindo as decisões pendentes de forma explícita | Revisão |
| 4 | Rodrigo | Valida a v0.1 e pede o documento final | Aprovação humana |
| 4 | Claude | Gera o `harness-produto-v1.0.md` com marcação de origem [R], [R+C] e [C] | Consolidação |
| 5 | Rodrigo | Pede a exportação deste histórico | Registro |

**Nota de transparência sobre o turno 1:** as perguntas-guia e a estrutura sugerida no turno 1 orientaram o rascunho de Rodrigo. Algumas indicações iniciais também apareceram nesse turno:
- rodar o conjunto inteiro de testes a cada mudança;
- tolerância zero para regressão em NÃO DEVE;
- formato de matriz para o HITL.

Nenhuma solução completa, nem o documento, foi gerada antes do rascunho v0. O conteúdo do v0, transcrito integralmente no turno 2, é de autoria de Rodrigo.

---

## Artefatos anexados na sessão

| Arquivo | Turno | Papel |
|---|---|---|
| `enunciado-3.2.md` | 1 | Enunciado do exercício (transcrito abaixo) |
| `guardrails-novatech-v1.1.md` | 2 | Guardrails do cenário 2 (DEVE / NÃO DEVE / QUANDO EM DÚVIDA, com classificação D/P). Conteúdo não reproduzido aqui; artefato já existente no repositório |
| `entregavel-proposta-ajustes-harness-v1.0.md` | 2 | Entregável do exercício 3.1 (taxonomia de 4 camadas, schema, HITL-1 a HITL-4, Caso 5). Conteúdo não reproduzido aqui; artefato já existente |

---

## Turno 1

### Rodrigo

> agora dando continuidade aos exercícios de IA first sobre a simulação do projeto NovaTech recebi esse enunciado, você consegue me ajudar a definir como preencher realizar esse exercício?

**Anexo: `enunciado-3.2.md`**

> #### Exercício 3.2 — Harness de produto para melhoria contínua
>
> **Tópico:** Harness Engineering
>
> **Contexto:** O assistente vai evoluir após o go-live. Você define, do ponto de vista de produto, como garantir que ele melhore sem degradar.
>
> **Ferramentas a utilizar:** Claude (chat)
>
> **Inputs fornecidos:**
> - O cenário completo.
> - O conceito de harness de produto: *"Define quais métricas de qualidade são monitoradas, como o feedback de usuários é processado, e como mudanças no assistente são validadas antes de ir a produção (regression testing de produto)."*
> - Os guardrails formalizados no cenário 2 (DEVE / NÃO DEVE / QUANDO EM DÚVIDA), que o harness deve preservar ao longo da evolução.
>
> **Tarefa:**
> Usando o **Claude**, projete um harness de produto que cubra:
> 1. **Processo de feedback:** como o feedback do atendente vira melhoria (novo documento? ajuste de prompt? reindexação?).
> 2. **Regression testing de produto:** antes de mudar o prompt ou adicionar documentos, como verificar que as respostas existentes não pioraram E que os guardrails do cenário 2 continuam sendo respeitados.
> 3. **Ponto de human-in-the-loop:** quais mudanças no assistente exigem aprovação humana antes de ir a produção, e quem aprova.
>
> **Entregável:** O documento do harness de produto.
>
> **Critérios de avaliação:**
> - O processo de feedback é completo (do atendente até a melhoria efetiva).
> - O regression testing reconhece que mudanças em IA podem ter efeitos colaterais e verifica que os guardrails não regridem.
> - O ponto de HITL é concreto (define o que precisa de aprovação humana e quem aprova).

### Claude

Sim, dá para fazer isso bem, e boa parte do material já existe no 3.1. Antes do roteiro, um ponto de processo: no 3.1 você perdeu ponto em D4 porque sua análise apareceu depois da avaliação da IA. Aqui o risco é o mesmo, porque o enunciado diz "usando o Claude, projete". Então sugiro que eu não gere o documento agora. Você faz um rascunho próprio primeiro, e eu entro depois para criticar e completar.

**Fluxo sugerido**

1. **Rascunho próprio (humano primeiro).** Responda por escrito às perguntas-guia abaixo, mesmo em tópicos soltos. Esse rascunho vira evidência do processo.
2. **Carga de contexto.** Traga para a sessão o `proposta-ajustes-harness-v1.0.md`, os guardrails v1.1 e seu rascunho. Faça isso antes de pedir qualquer geração.
3. **Crítica da IA.** Eu comparo seu rascunho com os três critérios de avaliação e com os artefatos do 3.1, apontando lacunas e armadilhas.
4. **Consolidação.** Geramos o `harness-produto-v1.0.md`, registrando o que veio de você e o que veio da crítica.
5. **Exportação** do histórico como `historico-chat-cenario3-exercicio3.2.md`.

**Como reaproveitar o 3.1**

A taxonomia de quatro camadas (Prompt / Pipeline / Interface-Produto / Harness) pode ser o **critério de triagem do feedback**. Cada feedback é classificado em uma camada, e a camada define o tipo de correção.

A distinção **lacuna vs. erro** é o primeiro corte da triagem:
- Uma lacuna de corpus leva a um novo documento ou a uma atualização de documento.
- Um erro do assistente leva a um ajuste de prompt ou de pipeline.

O **Caso 5** entra no conjunto de regressão como teste que *deve passar*, para evitar bloqueio excessivo.

As regras **HITL-1 a HITL-4** tratam de revisão humana em tempo de execução, resposta a resposta. O HITL do 3.2 é outra coisa: é HITL de *mudança*, ou seja, quem aprova alterações no assistente antes do deploy. Deixar essa diferença explícita no documento evita que o avaliador confunda as duas.

O incidente do módulo de feedback gerado pelo Copilot, que não validava com Zod e registrava dados sensíveis, é um argumento concreto para a própria captura de feedback ter regras. Isso inclui schema validado e nenhum dado pessoal do atendente em log.

**Perguntas-guia para o seu rascunho**

**1. Processo de feedback**, do atendente até a melhoria efetiva:
- Como o atendente sinaliza o problema no Teams? Um polegar para baixo basta, ou ele precisa escolher um motivo (resposta errada, sem fonte, documento desatualizado, não encontrou)?
- Quem faz a triagem, com que frequência e com qual critério de classificação?
- Para cada tipo de causa, qual é a ação correspondente? As opções incluem novo documento, nova versão de documento, reindexação ou ajuste de chunking, ajuste de prompt e nova regra de harness.
- Como você confirma que a melhoria funcionou? E o atendente fica sabendo? Fechar o ciclo com ele costuma ser o elo esquecido, e é exatamente o que o critério "completo" cobra.
- Quais métricas de qualidade você monitora continuamente? Exemplos: taxa de feedback negativo, taxa de respostas com fonte, taxa de acionamento de HITL e a evolução dos 12% de erro.

**2. Regression testing de produto:**
- O que compõe o conjunto de referência (golden set)? Pense nos casos do 3.1, em testes específicos por guardrail, no conflito PROC-042 v1/v2, nos casos de lacuna e no Caso 5.
- Por que adicionar *um* documento exige rodar o conjunto *inteiro*? Esse é o efeito colateral que o critério quer ver reconhecido.
- Quais verificações são determinísticas e quais são probabilísticas?
- Qual é o critério de aprovação? Uma sugestão para você validar: tolerância zero para regressão em guardrail NÃO DEVE, e um limite máximo de queda na qualidade geral.

**3. HITL de mudança:**
- Liste os tipos de mudança: prompt de sistema, novo documento oficial, nova versão de documento, alteração de guardrail, troca de modelo, ajuste de chunking ou embedding, mudança de schema de saída.
- Para cada tipo, defina o nível de risco, quem aprova (papel, não nome) e o que o aprovador precisa ver antes de aprovar.
- Existe alguma mudança que pode ir para produção sem aprovação humana? Se existir, ela precisa ser justificada.

Um formato de matriz (mudança × risco × aprovador × evidência exigida) atende bem o critério "concreto".

**Estrutura sugerida para o entregável:** objetivo e escopo; métricas monitoradas; processo de feedback; regression testing; HITL de mudança; glossário curto.

---

## Turno 2 — Rascunho humano (v0)

### Rodrigo

**Anexos:** `guardrails-novatech-v1.1.md`, `entregavel-proposta-ajustes-harness-v1.0.md`

> Projeto de harness:
>
> 1 - Processo de feedback
> 1.1 - Para cada problema o usuário deve acionar um polegar para baixo para sinalizar o problema. Abrir então as opções de motivo: resposta erra, sem fonte, documento desatualizado, não encontrou, resposta sem nexo.
> 1.2 - Diariamente haverá um processo de coleta e classificação automatizada dessas respostas, agrupamento e geração de um report para análise de um especialista.
> 1.3 - A classificação deverá sugerir opções de atualização de documento, versionamento, reindexação ajuste de chunking, de prompt e nova regra de harness.
> 1.4 - Cada apontamento deverá passar por uma nova fase de validação para que faça parte oficial das fontes.
> 1.5 - Criar métricas de acompanhamento de feedbacks, negativos, corretivos e validados com sucesso, assim como a comparação da evolução sobre o acionamento de HITL x numero total de respostas.
>
> 2 - Regression testing de produto:
> 2.1 - A cada injestão de dados ou alteração nos harness ou qualquer estrutura do projeto, deve-se ter um portão de testes de regressão aonde devem ser validado o nível de conformidade de perguntas/respostas.
> 2.2 - As perguntas e respostas devem ter um conjunto envolvendo os principais harness, caso algum harness for adicionado um novo conjunto de perguntas/respostas que cubram a situações dos novos harness deve ser criado.
>
> 3 - Ponto de human-in-the-loop:
> 3.1 - Levar em consideração na atualização das mudanças a seguinte estrutura de governança de dados:
>
> Avaliar cada mudança a partir de uma matriz de aprovação:
>
> mudança (tipo: prompt, pipeline, interface-produto, harness) | risco (baixo, médio, alto) | perfil aprovador (operador, assistente, analista, supervisor), para cada combinação classificar e sugerir um tipo de aprovação específico para a mudança (ex.: tipo: prompt, risco baixo - aprovador: operador), assim sucessivamente

### Claude — Crítica do rascunho

Seu rascunho está registrado como a análise humana, feita antes da minha.

**Visão geral**

| Critério do enunciado | Situação no rascunho | Risco na avaliação |
|---|---|---|
| Feedback completo (do atendente até a melhoria) | Boa estrutura, mas o ciclo não fecha com o atendente e não há corte entre lacuna e erro | Médio |
| Regressão reconhece efeitos colaterais e garante que os guardrails não regridem | O portão existe, mas não diz por que é necessário, não cita os guardrails do cenário 2 e não tem critério de aprovação | **Alto** |
| HITL concreto (o que exige aprovação e quem aprova) | A matriz é boa, mas os aprovadores e os critérios de risco estão indefinidos | Médio/Alto |

**1. Processo de feedback**

*O que está forte:* motivo capturado de forma estruturada, classificação que sugere a camada de correção (reaproveitando a taxonomia do 3.1), etapa de validação e métrica de HITL em relação ao volume total.

*O que falta:*

- **Contexto técnico junto com o feedback.** O polegar para baixo sozinho não permite achar a causa. É preciso guardar pergunta, resposta, chunks recuperados e suas versões, versão do prompt e JSON do structured output.
- **Proteção de dados na captura.** O incidente do módulo gerado pelo Copilot foi justamente nesse fluxo. O documento deve exigir schema validado com Zod e identificador pseudonimizado.
- **Motivos mapeados aos guardrails.** "Sem fonte" corresponde a D4/HITL-4, "documento desatualizado" a D1/N3 e "não encontrou" a D3/N4. "Resposta errada" é vago demais, e falta "resposta incompleta" (casos 1 e 2 do 3.1).
- **Separação entre lacuna e erro.** "Não encontrou" pode ser falso negativo de busca (INC-03) ou lacuna real de corpus (Caso 5). A classificação precisa separar os dois, senão as métricas ficam distorcidas.
- **Só existe polegar para baixo.** Sem o polegar para cima não há denominador nem captação de novos casos positivos.
- **Viés do feedback espontâneo.** Uma resposta errada e convincente, como a do caso 6, não gera polegar para baixo. É preciso amostragem de QA independente, o que a própria v1.1 exige para os guardrails P.
- **A classificação automática também é IA.** O especialista precisa confirmar e o papel dele deve ser nomeado. Feedback em tema de risco precisa de escalonamento imediato.
- **O item 1.4 cobre só documentos.** Documento novo precisa vir da área dona e entrar na lista oficial, senão o HITL-2 bloqueia. Prompt e harness são validados pelo portão de regressão.
- **O ciclo não fecha com o atendente.** Faltam status visíveis para ele.
- **Métricas sem linha de base nem meta.** A linha de base é 12%. Vale incluir lead time, reincidência e taxa de bloqueio excessivo. Uma queda no HITL pode ser boa ou ruim, e o documento precisa dizer como interpretá-la.

*Sugestão:* cada feedback corrigido vira um caso no conjunto de regressão, o que liga a seção 1 à seção 2.

**2. Regression testing (o ponto mais frágil)**

- **Efeitos colaterais não explicados.** Um documento novo pode mudar o ranking de perguntas não relacionadas. Um ajuste de prompt para o caso 1 pode quebrar F3 ou D5. Rodar só os casos relacionados não detecta nenhum dos dois.
- **Guardrails do cenário 2 não citados.** O 2.2 fala em "principais harness", mas o critério cobra os guardrails DEVE / NÃO DEVE / QUANDO EM DÚVIDA. Sugestão: conjunto organizado pelos 14 guardrails da v1.1, mais INC-01 a INC-03, os seis casos de staging, o Caso 5 como teste que deve passar e os casos de feedback.
- **Critério de aprovação ausente.** Proposta: 100% nas checagens determinísticas, zero regressão em NÃO DEVE, guardrails P via LLM-as-judge com amostra humana e tolerância de queda na qualidade geral, e o Caso 5 continuando a passar.
- **Comparação antes/depois:** o resultado deve ser um diff contra a produção.
- **Não determinismo:** casos P precisam de várias execuções e limiar estatístico.
- **Premissa dos guardrails [D]:** sem `versao_status` ou `categoria_carga`, D1, N2 e N3 viram P. O portão de ingestão deve validar esses metadados.
- **Gatilhos externos:** atualização de modelo pelo provedor e troca de embedding também disparam o portão, além de uma execução periódica contra deriva.
- **Destino do relatório:** ele é a evidência do aprovador da seção 3.

**3. HITL de mudança**

- **Perfis de aprovador.** "Operador, assistente, analista, supervisor" parece hierarquia de atendimento, e "assistente" se confunde com o assistente de IA. Sugestão: PS/PO, tech lead, dono do conteúdo e operações/conformidade. Operador aprovando prompt é arriscado, porque todo prompt afeta todas as respostas.
- **Critério de risco objetivo.** Alto: toca `risk_topic`, guardrails, harness ou modelo. Médio: busca ou prompt global. Baixo: texto de interface ou documento sem conteúdo normativo.
- **Tipos de mudança.** Separar conteúdo/corpus de pipeline técnico e criar o tipo "alteração de guardrail".
- **Combinações que não existem.** Harness de risco baixo provavelmente não existe, e dizer isso é mais forte que preencher as 12 células por simetria.
- **Evidência e separação de papéis.** Relatório de regressão, diff e rollback. Quem implementa não aprova. Código do Copilot é revisado contra o AGENTS.md.
- **Caminho de emergência** para rollback sem a matriz completa.
- **Nomenclatura.** "Governança de mudanças" em vez de "governança de dados", com nota diferenciando do HITL-1 a HITL-4.

**Decisões pendentes:**
1. Os perfis de aprovador: manter os quatro propostos ou adotar papéis de responsabilidade.
2. Os números do critério de aprovação da regressão.
3. Separar conteúdo/corpus de pipeline e criar o tipo "alteração de guardrail".

---

## Turno 3

### Rodrigo

> ok me ajude a revisar a minha sugestão "humana" de forma que não fique tão desumana e siga as suas recomendações

### Claude — Revisão v0.1

Registro do que foi feito nesta resposta:

- Manteve a estrutura e a numeração de Rodrigo, em frases diretas.
- Recomendou guardar o rascunho original como **v0**, sem alterações, como evidência de "humano primeiro". A revisão foi identificada como **v0.1**.
- **Assumiu as três decisões pendentes de forma explícita**, deixando a Rodrigo a opção de trocá-las:
  - papéis por responsabilidade;
  - separação entre conteúdo e pipeline;
  - "Guardrail" como tipo próprio de mudança.
- Marcou como **"(a confirmar)"** os números sugeridos pela IA, sem base no cenário:
  - amostra de QA de 30 respostas por semana;
  - 3 execuções por caso probabilístico;
  - tolerância de 2 p.p.

Conteúdo da v0.1, em resumo por seção:

- **Seção 1 (feedback):** itens 1.1 a 1.12. Cobre captura com polegar positivo e negativo e seis motivos, contexto técnico automático, proteção de dados (Zod e pseudonimização), escalonamento imediato para tema de risco, triagem diária com confirmação humana, corte lacuna/erro, ação por camada, amostragem independente de QA, validação e entrada na lista oficial, feedback corrigido virando teste, status para o atendente e métricas.
- **Seção 2 (regressão):** itens 2.1 a 2.6. Cobre a justificativa de efeitos colaterais, os gatilhos (incluindo provedor e deriva), o conjunto de referência pelos 14 guardrails mais incidentes, staging, Caso 5 e feedbacks, as checagens determinísticas e probabilísticas, o critério de aprovação e o relatório comparativo.
- **Seção 3 (HITL de mudança):** itens 3.1 a 3.5. Cobre a diferença do HITL de execução, os quatro papéis, o critério de risco, a matriz com 6 tipos × 3 riscos (com as células inexistentes justificadas) e as regras gerais, incluindo rollback de emergência.

A v0.1 completa corresponde ao conteúdo das seções 1 a 3 do entregável final, com a marcação de origem adicionada na v1.0.

---

## Turno 4

### Rodrigo

> ok agora gere o documento harness-produto-v1.0.md

### Claude

Gerou o arquivo `harness-produto-v1.0.md` a partir da v0.1. Acréscimos em relação à v0.1:

- legenda de origem **[R] / [R+C] / [C]** em cada item, para auditoria da ordem humano → IA;
- seção 0.4 de cobertura dos critérios do enunciado;
- diagrama Mermaid do ciclo de feedback;
- métricas numeradas M-01 a M-08, separando taxa de erro e taxa de lacuna;
- tabela de parâmetros a confirmar (P-01 a P-04);
- glossário;
- changelog v0 → v0.1 → v1.0.

---

## Turno 5

### Rodrigo

> gerar agora o histórico desse chat para avaliação

### Claude

Gerou este arquivo.

---

## Resumo para o avaliador

| Item | Evidência |
|---|---|
| Análise humana antes da IA | Rascunho v0 de Rodrigo no turno 2, anterior a qualquer proposta de solução da IA. O turno 1 continha apenas orientação e perguntas-guia (ver nota de transparência) |
| Crítica da IA baseada em critérios | Turno 2: tabela critério × situação × risco e lacunas por seção |
| Decisão humana sobre a revisão | Turnos 3 e 4: Rodrigo pede a revisão e depois valida a v0.1 antes da geração final |
| Rastreabilidade no entregável | Marcação [R] / [R+C] / [C] em cada item do `harness-produto-v1.0.md` |
| Pontos em aberto declarados | Parâmetros P-01 a P-04 marcados como "a confirmar" no entregável |
