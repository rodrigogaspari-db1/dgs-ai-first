# Histórico de Chat — Cenário 3, Exercício 3.1

**Projeto:** IA-First Certification — DGS (DB1 Global Software)
**Fase:** Cenário 3 — Harness e Governança
**Exercício:** 3.1
**Participante:** Rodrigo (Product Specialist)
**Data de exportação:** 20/09/2026

---

## Turno 1 — Contexto do Cenário 3

**Rodrigo:**

> Cenário-3 Exercício 3.1:
>
> ```markdown
> O assistente de IA da NovaTech está em desenvolvimento. O pipeline de RAG está funcional, os primeiros endpoints foram implementados, e o bot do Teams responde perguntas de teste. Mas antes do go-live, o time precisa garantir que o sistema é confiável e governável.
>
> Esta fase usa os artefatos produzidos nas fases anteriores: as ADRs e o pipeline de RAG da fase de entendimento (cenário 1), e o AGENTS.md, as specs SDD, as skills e os guardrails da fase de estruturação (cenário 2). O harness que será trabalhado agora amarra tudo isso num sistema de governança.
> ```
>
> Armazene isso para continuarmos os exercícios de simulação do projeto IA-First agora no Cenário-3

**Claude:**

Confirmou o armazenamento do contexto do Cenário 3 (harness/governança), destacando o reaproveitamento de artefatos do Cenário 1 (ADRs, pipeline RAG) e do Cenário 2 (AGENTS.md, specs SDD, skills, guardrails), e perguntou qual seria a tarefa específica do Exercício 3.1.

---

## Turno 2 — O que foi construído até agora

**Rodrigo:**

> Armazene isso também:
>
> ```markdown
> O que foi construído até agora
>
> - O pipeline de ingestão processa 847 documentos e os indexa no Azure AI Search.
> - O query endpoint recebe perguntas via POST, busca chunks, e retorna respostas com citação de fonte.
> - O bot do Teams funciona em ambiente de staging, acessível por 5 atendentes-piloto.
> - O AGENTS.md (construído pelo time no cenário 2), as specs SDD e as skills estão no repositório e sendo usadas pelo Copilot.
> - Os guardrails de produto foram formalizados pelo Product Specialist (cenário 2) em DEVE / NÃO DEVE / QUANDO EM DÚVIDA.
> - Testes de integração cobrem ~75% do código.
> ```
>
> mas não faça nenhuma ação ainda

**Claude:**

Armazenou o contexto sem executar nenhuma ação adicional, conforme solicitado.

---

## Turno 3 — O que foi descoberto durante o desenvolvimento

**Rodrigo:**

> ```markdown
> O que foi descoberto durante o desenvolvimento:
>
> - Em testes internos, **12% das respostas estavam incorretas**: alucinação, documento desatualizado, e chunk incorreto recuperado.
> - As respostas do assistente são retornadas como texto livre. Não há um formato estruturado garantindo que campos obrigatórios (fonte, confiança) sempre estejam presentes — quando o modelo "esquece" de incluir a fonte, nada impede a resposta de seguir.
> - Um desenvolvedor gerou com o Copilot um módulo de feedback que ignorou regras do AGENTS.md (não usou Zod, logou dados sensíveis do atendente).
> - A NovaTech pediu uma demonstração para a diretoria em 2 semanas.
> ```

**Claude:**

Armazenou o contexto sem executar ação adicional.

---

## Turno 4 — O desafio desta fase

**Rodrigo:**

> ```markdown
> O desafio desta fase
>
> O time precisa:
> 1. Reforçar o harness — o conjunto de verificações e limites que torna o assistente confiável, usando **structured outputs** (forçar o modelo a responder em formato validável) e **human-in-the-loop** (pontos onde um humano valida antes de prosseguir).
> 2. Aplicar revisão crítica ao que foi gerado por IA: código, respostas do assistente, testes.
> ```
>
> Armazene mais isso para seguirmos

**Claude:**

Armazenou o desafio da fase e perguntou qual seria a próxima etapa/entrega específica.

---

## Turno 5 — Conceitos-chave desta fase

**Rodrigo:**

> ```markdown
> Conceitos-chave desta fase
>
> - **Structured Outputs:** Em vez de deixar o modelo responder em texto livre, define-se um formato (JSON) que a resposta DEVE seguir, com campos obrigatórios (ex: `answer`, `source_document`, `confidence_score`). Respostas que não seguem o formato são rejeitadas programaticamente. Reduz campos faltantes e facilita a validação automática.
> - **Human-in-the-Loop (HITL):** Pontos do fluxo onde a validação final é de um humano, não do modelo. O harness define onde HITL é obrigatório, com base no risco da decisão (ex: respostas de baixa confiança sobre temas sensíveis).
> ```

**Claude:**

Armazenou os conceitos-chave (Structured Outputs e HITL) e sinalizou que todo o contexto do Exercício 3.1 estava consolidado.

---

## Turno 6 — Avaliação dos 6 pares pergunta/resposta em staging

**Rodrigo:**

> Agora usando o cenário completo do projeto IA-First-Cenário-2 e mais o contexto desse novo chat, avalie esses 6 pares de pergunta/resposta do assistente em staging (simulados):
>
> | # | Pergunta | Resposta | Fonte Citada | Confiança |
> |---|----------|---------|--------------|-----------|
> | 1 | "Qual o prazo de devolução para produtos standard?" | "O prazo é de 7 dias úteis após o recebimento. O cliente deve abrir chamado no portal e anexar fotos." | POL-001, seção 3.2 | Alta |
> | 2 | "Meu cliente é Silver. Qual o prazo de resolução?" | "O cliente Silver tem resolução em até 48h." | SLA-2024 | Alta |
> | 3 | "Posso devolver carga perigosa classe 3?" | "Não. Cargas perigosas (classes 1 a 6 da ANTT) não podem ser devolvidas pelo processo padrão. Recomendo escalar para o supervisor." | POL-001, seção 3.2 | Alta |
> | 4 | "Qual a política para carga danificada durante transporte?" | "A política de danos prevê reembolso integral quando comprovada negligência da transportadora, mediante laudo técnico e fotos." | Nenhuma | Alta |
> | 5 | "Qual o SLA do cliente Enterprise?" | "O tier Enterprise não foi encontrado na documentação. Os tiers documentados são Gold, Silver e Standard. Sugiro confirmar a classificação ou escalar ao supervisor." | — | Baixa |
> | 6 | "Posso enviar carga perigosa com frete expresso?" | "Sim, cargas perigosas podem ser enviadas via frete expresso mediante autorização prévia do compliance e documentação ANTT atualizada." | FAQ-Atendimento, item 32 | Alta |

**Claude:**

Produziu avaliação caso a caso com veredito (✅ OK / 🟠 Risco / 🔴 Crítico):

- **Caso 1:** OK — resposta objetiva e fonte específica.
- **Caso 2:** OK com ressalva — cita SLA-2024 sem seção/página.
- **Caso 3:** Risco — possível chunk incorreto (reuso da mesma seção POL-001 3.2 da pergunta 1, para tema distinto); observação sobre classificação ANTT ("classes 1 a 6") potencialmente incompleta.
- **Caso 4:** Crítico — fonte "Nenhuma" com confiança "Alta"; alucinação plausível e operacionalmente acionável.
- **Caso 5:** Exemplo de comportamento correto — reconhece limite documental, lista tiers reais, confiança calibrada como Baixa.
- **Caso 6:** Crítico — fonte "FAQ-Atendimento, item 32" não corresponde ao formato do FAQ real do corpus; tema de alto risco regulatório com confiança Alta.

Identificou 4 padrões cross-cutting: confiança não correlacionada ao risco do tema; `source_document` não funcionando como campo verdadeiramente obrigatório; reuso de citação mascarado por confiança alta; formato de citação inconsistente como sintoma de saída não estruturada. Propôs gatilhos de harness (rejeição por structured output + regras de HITL) e ofereceu formalizar isso como matriz de critérios do harness.

---

## Turno 7 — Comparação com a avaliação pessoal de Rodrigo

**Rodrigo:**

> Agora compare a minha avaliação pessoal das resposta e a avaliação que você fez claude, e os ajustes, veja se há algo a complementar que faça sentido nas minhas avaliações:
>
> 1 - Avaliação: resposta adequada, mas cabe melhoria: A resposta não ficou clara quanto ao prazo do procedimento, a pergunta pode ser ambígua já que o prazo é para solicitar a devolução e não para "devolver" então a afirmação" o prazo é de sete dias pode levar a uma conclusão equivocada. - Tipo do erro: Informação incompleta. Tipo de ajuste para melhorar: Prompt
> 2 - Avaliação: resposta adequada, mas cabe melhoria: A resposta foi genérica, existe sim um prazo para resolução porém se tratando de resolução há também duas classificações de problemas gerais/incidentes críticos, isso deveria ser destacado na resposta - Tipo do erro: Informação incompleta. Tipo de ajuste para melhorar: Pipeline
> 3 - Avaliação: resposta adequada, mas cabe melhoria. Aqui a IA fez uma recomendação na resposta fora do que estava especificado: "escalar para o supervisor", isso não está contido na política - Tipo do erro: Alucinação. Tipo de ajuste para melhorar: Prompt
> 4 - Erro: Sem fonte formal e com confiança alta, sem lastro documental. Tipo de erro: Fonte não confiável. Tipo de ajuste para corrigir: Pipeline (melhoria na ingestão e pipeline de dados).
> 5 - Avaliação: resposta adequada, mas cabe melhoria: não apresentou erro e informou corretamente as categorias, além de sinalizar como Baixa a confiança por não ter uma resposta que atendesse a questão adequadamente. Tipo do erro: Informação incompleta. Tipo de ajuste para melhorar: Interface e Pipeline de dados.
> 6 - Erro: Confiança alta para uma informação sem um documento formal de procedimentos, também não informou que isso pode levar um tempo maior que o expresso por conta de mais autorizações necessárias - Tipo do erro: Fonte não confiável. Tipo de ajuste para corrigir o erro: Pipeline de dados

**Claude:**

Comparou as duas avaliações par a par, destacando:

- **Caso 1:** validou a observação de ambiguidade "solicitar vs. devolver" (ponto não capturado na avaliação técnica), mas apontou que o tipo de ajuste ("Prompt") depende de a ambiguidade já existir ou não na fonte (POL-001 3.2).
- **Caso 2:** concordou, acrescentando que a distinção entre problema geral/incidente crítico pode ser causa de Pipeline (se não recuperada) ou Prompt (se recuperada e omitida); notou confiança "Alta" incoerente com informação incompleta.
- **Caso 3:** destacou como o ponto mais valioso da avaliação de Rodrigo (recomendação "escalar para supervisor" sem base na política = alucinação por geração), conectando-o ao problema de reuso de citação identificado na avaliação técnica (grounding fraco → compensação gerativa); sugeriu registrar os dois problemas e ajustes juntos.
- **Caso 4:** concordância total; complementou que sem regra de harness que bloqueie `confiança alta + fonte nula`, o padrão tende a se repetir.
- **Caso 5:** apontou inconsistência interna na avaliação de Rodrigo ("não apresentou erro" vs. "tipo de erro: informação incompleta"), sugerindo que o "erro" é do corpus/produto (tier Enterprise não documentado), não da resposta do assistente — que teve comportamento exemplar.
- **Caso 6:** concordou, reforçando que o formato de citação "FAQ-Atendimento, item 32" é indício de citação inventada; somou a observação de Rodrigo sobre prazo estendido por autorizações como segundo erro empilhado; reforçou necessidade de HITL para o tema (carga perigosa + frete expresso), independentemente da qualidade da fonte.

Propôs adicionar uma quarta categoria de ajuste ("Harness — structured output / HITL") para os casos 3, 4 e 6, e ofereceu consolidar tudo numa v1.1 da tabela de avaliação.

---

## Turno 8 — Geração da proposta de ajuste consolidada

**Rodrigo:**

> gere então agora uma proposta de ajuste baseado nas duas avaliações

**Claude:**

Gerou o arquivo `proposta-ajustes-harness-v1.0.md`, contendo:

1. Tabela consolidada de avaliação (6 casos, tipo de erro, camada(s) de ajuste, observações).
2. Taxonomia de ajuste com 4 camadas: Prompt, Pipeline, Interface/Produto, e a nova categoria **Harness** (regras estruturais que bloqueiam risco independentemente da causa raiz).
3. Proposta de schema JSON de structured output (`answer`, `source_document`, `source_section`, `confidence_score`, `risk_topic`, `requires_human_review`) com regras de validação/rejeição programática.
4. 4 regras de HITL (HITL-1 a HITL-4), cada uma mapeada a um padrão de falha observado nos casos 3, 4 e 6.
5. Casos de referência para calibração (caso 5 como padrão positivo; casos 3, 4 e 6 como padrões a bloquear).
6. Changelog (v1.0).

---

## Turno 9 — Exportação do histórico

**Rodrigo:**

> gere agora um arquivo .md com todo o histórico deste chat para uma avaliação

**Claude:**

Gerou este arquivo (`historico-chat-cenario3-exercicio3.1.md`) com o histórico completo do chat, estruturado por turno, para fins de avaliação.

---

## Artefatos produzidos nesta sessão

1. `proposta-ajustes-harness-v1.0.md` — proposta consolidada de ajustes do harness (schema de structured output + regras de HITL).
2. `historico-chat-cenario3-exercicio3.1.md` — este arquivo, histórico completo da sessão.
