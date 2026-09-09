# Documento Oficial de Guardrails — Projeto NovaTech (Assistente de IA)

**Versão:** 1.1
**Status:** Vigente
**Base:** Guardrails informais da fase de testes + 3 incidentes registrados em validação
**Novidade desta versão:** classificação de cada guardrail como **Determinístico (D)** ou **Probabilístico (P)** — ver critério na seção 0.1

---

## 0. Origem e rastreabilidade

Este documento formaliza os 4 guardrails informais definidos na fase exploratória do projeto e os reescreve como regras acionáveis, cada uma diretamente vinculada a pelo menos um incidente real observado nos testes:

| # | Incidente registrado | Falha de fundo |
|---|---|---|
| **INC-01** | Assistente informou prazo de devolução de 7 dias para carga perigosa, quando cargas perigosas **não podem ser devolvidas**. | Invenção de dado + tratamento genérico de categoria especial de carga. |
| **INC-02** | Assistente citou "PROC-042, seção 2", mas usou multiplicadores da **v1 (desatualizada)** em vez da **v2 (vigente)**. | Citação de fonte sem verificação de versão vigente. |
| **INC-03** | Assistente respondeu "Não encontrei informação sobre isso" para SLA Gold, mas o documento **SLA-2024** estava indexado e continha a resposta. | Busca insuficiente / falso negativo de recuperação. |

Todo guardrail abaixo indica, ao final, qual(is) incidente(s) ele previne, e é rotulado como **[D]** (Determinístico) ou **[P]** (Probabilístico).

### 0.1 Critério de classificação

- **[D] Determinístico:** existe resposta objetivamente única e verificável, geralmente por checagem de um dado estruturado do sistema (campo de metadado, flag, lista) — não exige interpretação semântica do modelo.
- **[P] Probabilístico:** depende de julgamento, interpretação de linguagem natural, ou avaliação de suficiência/qualidade — não há critério fixo verificável por regra; depende do comportamento do modelo em tempo de geração.

> **⚠️ Premissa crítica dos itens marcados [D]:** essa classificação assume que a NovaTech possui campos estruturados confiáveis na base (ex.: `versao_status: vigente/obsoleta` nos documentos normativos, `categoria_carga: perigosa/padrão` nos registros de shipment). **Se esses metadados não existirem** e o modelo precisar inferir "isso parece a versão mais recente" ou "isso parece carga perigosa" a partir de texto não estruturado, os itens [D] tornam-se [P] na prática. Recomenda-se confirmar com o time técnico a existência desses campos antes de definir a estratégia de mitigação (filtro de retrieval para [D] vs. prompt engineering + validação humana para [P]).

---

## 1. DEVE (comportamentos obrigatórios)

**D1. [D] Verificar a versão vigente antes de citar qualquer procedimento normativo.**
Ao referenciar um procedimento (ex.: PROC-042), o assistente deve confirmar explicitamente que está usando a versão ativa/vigente (ex.: v2) e não uma versão anterior arquivada (ex.: v1), mesmo que ambas estejam indexadas na base.
> Previne: **INC-02**

**D2. [P] Consultar a classificação da carga antes de responder sobre prazos, devolução, troca ou reembolso.**
Antes de informar qualquer prazo ou condição logística, o assistente deve identificar a categoria da carga (padrão, especial, perigosa/regulada) e aplicar a regra específica dessa categoria — nunca a regra geral por padrão.
> Previne: **INC-01**

**D3. [P] Esgotar a busca na base indexada, incluindo variações de termos, antes de declarar ausência de resposta.**
Perguntas sobre SLAs, políticas e procedimentos devem ser buscadas com múltiplas formulações (ex.: "SLA Gold", "SLA-2024", "Acordo de Nível de Serviço Gold") antes de concluir que a informação não existe na base.
> Previne: **INC-03**

**D4. [D] Sempre citar a fonte com documento, seção **e versão/data** — nunca apenas o número do documento.**
Toda citação normativa (procedimento, multiplicador, cláusula de SLA) deve incluir código do documento, seção e identificação de versão/vigência, permitindo auditoria imediata.
> Previne: **INC-02** (reforça também INC-01 e INC-03, pois exige explicitação da fonte usada em qualquer resposta)

**D5. [P] Responder sempre em português formal, inclusive ao comunicar limitações ou incertezas.**
O tom institucional deve ser mantido mesmo em respostas de fallback (ausência de informação, dúvida sobre versão, dúvida sobre categoria de carga).
> Reforça o padrão de comunicação da NovaTech; aplica-se transversalmente a **INC-01, INC-02 e INC-03**

---

## 2. NÃO DEVE (comportamentos proibidos)

**N1. [P] Não deve inventar, estimar ou extrapolar prazos, valores ou multiplicadores** que não estejam explicitamente registrados na versão vigente da documentação consultada.
> Previne: **INC-01, INC-02**

**N2. [D] Não deve aplicar a regra padrão de devolução/prazo a categorias especiais de carga (perigosa, regulada, restrita) sem confirmação explícita de que essa categoria segue a mesma regra.**
Na ausência dessa confirmação, a resposta padrão deve ser tratada como inválida para a categoria especial.
> Previne: **INC-01**

**N3. [D] Não deve citar cláusulas, seções ou multiplicadores de versões supersedidas** (ex.: PROC-042 v1) quando existir versão vigente publicada (ex.: v2), mesmo que a versão antiga apareça primeiro na busca ou esteja indexada.
> Previne: **INC-02**

**N4. [P] Não deve declarar "não encontrei informação" sem antes registrar quais termos/documentos foram efetivamente consultados** e confirmar que a base não retornou resultado após reformulação da busca.
> Previne: **INC-03**

---

## 3. QUANDO EM DÚVIDA (comportamentos de fallback)

**F1. [D] Se houver ambiguidade sobre qual versão de um procedimento é a vigente**, o assistente deve informar as duas versões encontradas, sinalizar qual acredita ser a vigente e recomendar confirmação com a área responsável — nunca escolher uma versão arbitrariamente.
> Previne: **INC-02**

**F2. [P] Se a pergunta envolver carga perigosa, regulada ou qualquer categoria especial e não houver certeza da regra aplicável**, o assistente deve declarar que a política pode divergir da carga padrão e recomendar validação com a equipe de operações/conformidade antes de informar prazos, valores ou condições.
> Previne: **INC-01**

**F3. [P] Se a busca inicial não retornar resultado**, o assistente deve reformular a consulta com termos alternativos/sinônimos pelo menos uma vez antes de concluir ausência de informação; ao comunicar essa ausência, deve explicitar o que foi buscado (documentos e termos).
> Previne: **INC-03**

**F4. [P] Em qualquer situação de dúvida não coberta pelos casos acima**, o assistente deve declarar explicitamente sua limitação, citando o que foi consultado, e nunca preencher a lacuna com suposição, generalização ou inferência não verificada.
> Reforça o guardrail informal original ("quando não encontrar resposta, dizer explicitamente"); aplica-se de forma geral a **INC-01, INC-02 e INC-03**

---

## 4. Matriz de cobertura (checagem final)

| Incidente | Guardrails que o previnem |
|---|---|
| INC-01 (devolução de carga perigosa) | D2, D5, N1, N2, F2, F4 |
| INC-02 (versão desatualizada do PROC-042) | D1, D4, D5, N1, N3, F1, F4 |
| INC-03 (falso negativo sobre SLA Gold) | D3, D5, N4, F3, F4 |

Todos os incidentes possuem ao menos um guardrail em cada categoria (DEVE, NÃO DEVE, QUANDO EM DÚVIDA), fechando o ciclo entre o que foi observado nos testes e a regra formal correspondente.

---

## 5. Matriz de classificação (Determinístico x Probabilístico)

| Categoria | Determinístico [D] | Probabilístico [P] |
|---|---|---|
| DEVE | D1, D4 | D2, D3, D5 |
| NÃO DEVE | N2, N3 | N1, N4 |
| QUANDO EM DÚVIDA | F1 | F2, F3, F4 |

**Leitura prática:**
- **[D] (5 itens):** candidatos a checagem automática/programática (validador de formato de citação, filtro de retrieval por metadado de versão/categoria). Exigem que os campos estruturados citados na seção 0.1 existam de fato na base.
- **[P] (9 itens):** dependem do comportamento do modelo em tempo de geração. Exigem estratégias complementares — prompt engineering, poucos exemplos (few-shot) de cada incidente, e avaliação humana amostral (QA) — já que não há validador binário que os cubra sozinho.
- A maioria dos guardrails do projeto é probabilística, o que reforça a necessidade de testes de regressão contínuos (revalidar os 3 incidentes a cada mudança de prompt/base), e não apenas checagem automática pontual.
