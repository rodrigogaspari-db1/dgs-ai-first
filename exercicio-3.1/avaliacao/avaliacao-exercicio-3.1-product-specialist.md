# Avaliação do Exercício 3.1 — Product Specialist

> **Programa:** Trilha de Certificação AI First — DGS (DB1 Global Software)
> **Cenário:** 3 — Governança e Validação
> **Exercício:** 3.1 — Revisão crítica das respostas do assistente
> **Participante:** Rodrigo (Product Specialist)
> **Data da avaliação:** 20/09/2026

---

## Resumo

O entregável apresenta uma avaliação técnica sólida (via Claude) e uma avaliação pessoal com insights próprios e específicos, consolidadas num artefato de proposta de harness bem estruturado, versionado e com schema de structured output e regras de HITL concretas. O ponto de atenção mais relevante é de processo: a tarefa pedia avaliação própria **antes** de acionar o Claude, mas no histórico a avaliação pessoal de Rodrigo (turno 7) é apresentada **depois** da avaliação do Claude (turno 6), invertendo a sequência prescrita no enunciado — ainda que o conteúdo demonstre independência real (traz observações que o Claude não fez).

---

## Scores por Dimensão

| Dimensão | Score | Justificativa |
|----------|-------|---------------|
| D1 — Domínio Conceitual | 3 | Demonstra domínio específico ao projeto: distingue corretamente alucinação (caso 3, recomendação "escalar supervisor" sem base na política), fonte não confiável (caso 6, FAQ informal) e informação incompleta (casos 1 e 2), e no artefato final formaliza `confidence_score`, `source_document` e `risk_topic` como campos obrigatórios ligados a regras de rejeição — mostrando entendimento de *por que* structured output é mais confiável que apenas pedir a fonte no prompt. |
| D2 — Uso de Ferramentas | 3 | Uso iterativo e específico do Claude ao longo de 9 turnos: contexto carregado em blocos controlados ("não faça nenhuma ação ainda"), pedido de avaliação técnica, pedido de comparação crítica, e geração de proposta consolidada. O output do Claude não foi aceito acriticamente — Rodrigo complementa, discorda parcialmente (ex.: nota sobre ambiguidade em POL-001 3.2 no caso 1) e o próprio Claude aponta inconsistência na avaliação de Rodrigo no caso 5, que é corrigida no artefato final. |
| D3 — Qualidade do Entregável | 3 | `proposta-ajustes-harness-v1.0.md` é completo, específico ao NovaTech (usa nomes reais dos documentos: POL-001, SLA-2024, FAQ-Atendimento), acionável (schema JSON com regras de rejeição programática, 4 regras de HITL mapeadas a padrões concretos dos casos 3, 4 e 6) e versionado com changelog e rastreabilidade às duas avaliações de origem. |
| D4 — Pensamento Crítico | 2 | A análise própria tem qualidade real e original (ambiguidade "solicitar vs. devolver" no caso 1, distinção problema geral/incidente crítico no caso 2, observação sobre autorizações adicionais no caso 6 — nenhuma dessas aparece na avaliação técnica do Claude), o que evidencia julgamento independente e não cópia. Porém, o enunciado pede explicitamente avaliação própria **antes** do Claude como segundo avaliador; no histórico, a ordem está invertida (Claude avalia no turno 6, a avaliação pessoal de Rodrigo só aparece no turno 7, em resposta à do Claude). Isso é uma violação do processo "humano primeiro" que a skill de avaliação trata como ponto de corte, mesmo que o conteúdo não seja idêntico ao da IA. |
| D5 — Aplicabilidade ao Projeto | 3 | Conecta profundamente com os artefatos anteriores: referencia explicitamente o AGENTS.md e os guardrails DEVE/NÃO DEVE/QUANDO EM DÚVIDA do cenário 2 no contexto carregado, usa os documentos reais do corpus NovaTech, e liga os problemas identificados (12% de erro, incidente do módulo de feedback do Copilot) ao desenho das regras de HITL propostas. |

**Score do exercício: 2,8**

---

## Verificação de Armadilhas

| Armadilha | Identificada? | Observação |
|---|---|---|
| **Resposta #4 — Alucinação** (política de danos inexistente na base) | ✅ Sim, mas com nuance | Na avaliação pessoal de Rodrigo (turno 7), o caso 4 é classificado como "Fonte não confiável", não como "Alucinação" — divergindo do rótulo exigido pelo enunciado. No entanto, a avaliação técnica do Claude usa o termo "alucinação plausível", e o artefato final consolida corretamente como *"Fonte não confiável (alucinação sem lastro)"*, capturando a substância exigida (confiança alta + fonte nula = invenção). |
| **Resposta #6 — Fonte não confiável** (FAQ informal, tema de risco) | ✅ Sim, integralmente | A avaliação pessoal de Rodrigo usa exatamente o rótulo esperado ("Fonte não confiável") e complementa com observação própria sobre o prazo estendido por autorizações — reforçada no artefato final com a regra HITL-2 dedicada a esse padrão. |

---

## Pontos Fortes

- Observações originais que vão além do que a avaliação técnica do Claude cobriu (ambiguidade "solicitar vs. devolver" no caso 1; distinção de SLA por severidade no caso 2), evidenciando leitura própria do Anexo A e não mera repetição.
- Proposta de harness tecnicamente correta e específica: regras de rejeição programática (`confiança alta + fonte nula`, `source_document fora da lista oficial`) resolvem exatamente os padrões identificados nos 6 casos, não são genéricas.
- Introdução da categoria "Harness" como quarta camada de ajuste (além de Prompt/Pipeline/Interface) é um avanço conceitual real: reconhece que corrigir a causa raiz não impede a recorrência do padrão de risco.

---

## Pontos de Melhoria

- **Ordem do processo:** inverter a sequência nas próximas simulações — registrar e travar a avaliação própria completa *antes* de pedir a avaliação do Claude, para que a comparação no enunciado ("compare com a sua avaliação") seja de fato entre duas análises independentes e não influenciadas.
- **Consistência de rótulo no caso 4:** ao classificar o tipo de erro, usar terminologia alinhada ao Anexo A/enunciado (Alucinação) desde a avaliação pessoal, e não só na consolidação final — evita ambiguidade sobre se o problema é "fonte fraca" (que pode ter causa em pipeline) ou "invenção de conteúdo" (que exige tratamento de geração + harness).
- **Caso 5:** a avaliação pessoal chamou de "informação incompleta" um caso que, na prática, é comportamento correto do assistente diante de uma lacuna de produto (tier não documentado) — a autoinconsistência foi bem capturada pelo Claude na comparação, mas vale já entrar na primeira análise com essa distinção mais clara (erro do assistente vs. lacuna do corpus).

---

## Classificação

**Aprovado com distinção (2,5–3,0)**

---

## Tópicos da Trilha para Reforço

Não aplicável — score acima de 2,5. Recomenda-se apenas reforço metodológico sobre a ordem de execução de exercícios "humano primeiro, IA depois", não sobre os conceitos de Harness Engineering ou Revisão Crítica em si.
