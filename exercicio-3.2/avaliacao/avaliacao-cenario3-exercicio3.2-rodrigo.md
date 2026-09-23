# Avaliação — Cenário 3, Exercício 3.2

**Programa:** Trilha de Certificação AI First — DGS (DB1 Global Software)
**Papel:** Product Specialist
**Cenário:** 3 — Governança e Validação
**Exercício:** 3.2 — Harness de produto para melhoria contínua
**Participante:** Rodrigo
**Entregável avaliado:** `harness-produto-v1.0.md`
**Evidência de processo:** `historico-chat-cenario3-exercicio3.2.md`
**Skills de avaliação:** `avaliacao-foundation.md` + `avaliacao-product-specialist.md`
**Avaliador:** Claude (IA)
**Data:** 2026-09-22

> **Nota de transparência do avaliador:** o avaliador é o mesmo modelo que criticou e consolidou este entregável na sessão avaliada. Por isso os critérios foram aplicados com rigor extra, separando o que é do participante ([R]) do que veio da IA ([C]).

---

## Resumo

O entregável é um harness de produto completo e ligado ao NovaTech. Atende com folga aos três critérios do enunciado: ciclo de feedback fechado até o atendente, regressão que justifica os efeitos colaterais e preserva os guardrails, e matriz de HITL de mudança com papéis e risco objetivos.

A ordem "humano primeiro, IA depois" foi respeitada e está bem documentada. O limite está na autoria das partes mais importantes: o rascunho v0 é enxuto, e os pontos que respondem diretamente aos critérios vieram quase todos da crítica da IA, aceita sem nenhuma divergência registrada.

---

## Scores por Dimensão

| Dimensão | Score | Justificativa |
|----------|-------|---------------|
| D1 — Domínio Conceitual | 3 | O documento mostra domínio específico de Harness Engineering. Distingue HITL de execução (HITL-1 a HITL-4) de HITL de mudança (3.1). Separa checagens determinísticas de probabilísticas e reconhece que [D] vira [P] sem os metadados `versao_status` e `categoria_carga`. Trata o não determinismo com múltiplas execuções (P-02) e a deriva por atualização do provedor. Ressalva: esses conceitos aparecem principalmente em itens [C]. O v0 mostra conceitos corretos, porém genéricos. |
| D2 — Uso de Ferramentas | 2 | O Claude foi usado com evidência e iteração estruturada (v0 → crítica → v0.1 → v1.0), com rastreabilidade [R]/[R+C]/[C]. Os prompts do participante, porém, foram genéricos ("siga as suas recomendações", "ok gere o documento"). As três decisões pendentes foram delegadas à IA, que "assumiu" as escolhas. Não há análise crítica do output da IA, que é o que D2=3 exige. |
| D3 — Qualidade do Entregável | 3 | O documento é completo, específico e acionável. Tem ciclo em Mermaid, métricas M-01 a M-08 com regra de leitura para M-06, golden set G1–G6 com o Caso 5 como calibração positiva, critério de aprovação do portão, matriz 6×3 com células inexistentes justificadas, caminho de emergência limitado a rollback e parâmetros declarados como "a confirmar". Há inconsistências pontuais (ver Pontos de Melhoria), mas elas não comprometem o uso. |
| D4 — Pensamento Crítico | 2 | O v0 é próprio, anterior à IA e tem ideias válidas: matriz tipo × risco × aprovador, portão a cada ingestão, novo harness exigindo novos casos de teste e HITL comparado ao total de respostas. Porém ele não cobre os dois pontos centrais do critério: não cita os guardrails do cenário 2 e não explica efeitos colaterais. Também não aplica a distinção lacuna vs. erro que o próprio participante estabeleceu no 3.1. Nenhuma recomendação da IA foi contestada, e as inconsistências abaixo passaram sem revisão. Não se aplica regra de corte, porque há análise própria substantiva e anterior. |
| D5 — Aplicabilidade ao Projeto | 3 | O documento é profundamente conectado ao projeto. Referencia guardrails v1.1 (D/N/F), proposta do 3.1 (taxonomia, schema, HITL, Caso 5), AGENTS.md e o incidente Zod/dados sensíveis, INC-01 a INC-03, POL-001, PROC-042 v1/v2, SLA-2024, FAQ e a linha de base de 12%. Lacuna menor: não referencia as ADRs (ADR-0002/0003) nem o context budget, por exemplo o impacto de novos few-shots no prompt. |

**Score do exercício: 2.6**

---

## Verificação de Armadilhas

A skill do papel não lista armadilhas obrigatórias para o 3.2. Os critérios do checklist foram verificados assim:

| Critério (skill PS 3.2) | Situação | Onde |
|---|---|---|
| Processo de feedback completo | ✅ Atendido | Da captura (1.1) até o retorno ao atendente (1.11); cada correção vira teste (1.10) |
| Regressão com efeitos colaterais e sem regressão de guardrails | ✅ Atendido | Seções 2.1, 2.3 (G1) e 2.5 (zero regressão em N1–N4) |
| HITL concreto (o quê e quem aprova) | ✅ Atendido | Seções 3.2 a 3.5, com segregação de funções e evidências obrigatórias |
| Guardrails do cenário 2 como invariantes | ✅ Atendido | G1; tipo "Guardrail" só existe em risco alto, com a regressão atualizada antes da mudança |

### Regras de corte

| Regra | Aplicável? |
|---|---|
| "Humano primeiro" sem análise própria → D4 ≤ 1 | Não. O v0 é próprio e anterior à solução da IA |
| Armadilha intencional não identificada → D4 ≤ 1 | Não há armadilhas listadas |
| Artefato ignora decisões dos cenários 1 e 2 → D5 ≤ 2 | Não. Guardrails v1.1 e artefatos do 3.1 são referenciados |

---

## Pontos Fortes

- **HITL de mudança bem delimitado.** A seção 3.1 separa explicitamente HITL de execução e de mudança. A matriz justifica por que prompt, harness e guardrail não têm risco baixo, o que é mais forte do que preencher as células por simetria.
- **Métricas que não se enganam.** Separar M-01 (erro) de M-02 (lacuna), incluir M-07 (bloqueio excessivo) e M-08 (erro não percebido pelo atendente) e dar a regra de leitura conjunta para M-06 mostra maturidade de produto.
- **Rastreabilidade do processo.** A marcação [R]/[R+C]/[C], o changelog e a nota de transparência sobre o turno 1 permitem auditar a ordem humano → IA. Isso corrige diretamente a dedução de D4 do 3.1.

---

## Pontos de Melhoria

- **Contagem de guardrails inconsistente.** D1–D5, N1–N4 e F1–F4 somam **13**, mas o documento diz "14 guardrails" nas seções 0.2 e 2.3. Verifique na v1.1 se falta um guardrail na lista ou se o número está errado. Esse erro veio da crítica da IA e não foi pego na revisão, o que é exatamente o tipo de checagem que eleva D4.
- **Casos do 3.1 descritos de forma divergente da avaliação de referência.** A seção 1.8 chama o caso 6 de "fonte inventada com confiança alta", mas o caso 6 é **fonte não confiável** (FAQ informal). O exemplo correto de erro convincente que o atendente não percebe é o **caso 4** (alucinação). A seção 1.1 também associa os casos 1 e 2 a "resposta incompleta", mas eles eram respostas adequadas. Alinhe os exemplos com a classificação que você mesmo fez no 3.1.
- **M-01 não é comparável à linha de base.** Os 12% vêm de testes internos com avaliação completa. M-01 depende de feedback espontâneo, que subnotifica, como o próprio documento reconhece na 1.8. Use M-08 (amostragem de QA) como comparação com os 12%. Defina também qual métrica é a "qualidade geral" a que o P-03 se aplica.

---

## Classificação

**Aprovado com distinção (2.6)**

---

## Tópicos da Trilha para Reforço

O score está acima de 2.5, então não há reforço obrigatório.

Recomendação de desenvolvimento: **Revisão Crítica de Outputs de IA aplicada ao próprio fluxo de coautoria**. Nos próximos exercícios:

1. Registre ao menos uma decisão em que você divergiu ou ajustou a recomendação da IA.
2. Tome você as decisões pendentes, em vez de delegá-las.
3. Faça uma checagem de consistência factual (contagens, classificação de casos, linhas de base) antes de aprovar a consolidação.
