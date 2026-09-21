# Proposta de Ajustes — Harness de Governança do Assistente NovaTech

**Projeto:** IA-First Certification — DGS (DB1 Global Software)
**Fase:** Cenário 3 — Harness e Governança
**Exercício:** 3.1
**Autor:** Rodrigo (Product Specialist) + Claude (apoio à evolução do harness)
**Versão:** 1.0
**Base:** Avaliação técnica (Claude) + Avaliação pessoal (Rodrigo) dos 6 pares pergunta/resposta em staging

---

## 1. Objetivo

Consolidar as duas avaliações independentes dos 6 casos de staging numa única proposta de ajuste, classificando cada erro por **tipo** e por **camada de correção**, e propor as regras de harness (structured output + HITL) necessárias para que os padrões identificados não cheguem a produção antes da demonstração à diretoria.

---

## 2. Tabela consolidada de avaliação

| # | Problema identificado (consolidado) | Tipo de erro | Camada(s) de ajuste | Observação |
|---|---|---|---|---|
| 1 | Resposta usa "7 dias" sem deixar claro se o prazo é para **solicitar** a devolução ou para **efetivá-la**. Ambiguidade que pode induzir o atendente a erro. | Informação incompleta | Prompt *(condicional — ver nota A)* | Verificar se a ambiguidade já existe no texto-fonte (POL-001, 3.2) antes de tratar como problema de prompt. Se a fonte já for ambígua, o ajuste correto é de documento-fonte, não de prompt. |
| 2 | Resposta genérica; não distingue as classificações de **problema geral** vs. **incidente crítico**, que têm SLAs diferentes. | Informação incompleta | Pipeline (retrieval) e/ou Prompt | Se a distinção existe no SLA-2024 mas não foi recuperada → Pipeline. Se foi recuperada mas omitida na geração → Prompt. Também descalibrado: confiança "Alta" para resposta incompleta. |
| 3 | (a) Recomendação "escalar para o supervisor" **não consta** na política — adição gerativa sem lastro documental. (b) Cita a **mesma seção** (POL-001, 3.2) usada na pergunta 1, para um assunto distinto — indício de chunk incorreto/reuso de citação. | Alucinação **+** possível erro de retrieval | Prompt (para a adição indevida) **+** Harness/Pipeline (checagem de grounding e duplicidade de citação) | Os dois problemas provavelmente estão ligados: grounding fraco tende a levar o modelo a "compensar" com conteúdo plausível não verificado. |
| 4 | Resposta específica e operacionalmente acionável (reembolso integral, laudo técnico) apresentada com **confiança Alta** e **fonte = Nenhuma**. | Fonte não confiável (alucinação sem lastro) | Pipeline (ingestão/cobertura documental) **+** Harness (regra de bloqueio estrutural) | Corrigir a lacuna de ingestão resolve a causa deste caso; sem uma regra de harness que bloqueie `confiança: alta` + `fonte: nula`, o padrão pode se repetir a cada nova lacuna futura no corpus. |
| 5 | Tier "Enterprise" não existe na documentação. O assistente reconheceu o limite, listou os tiers reais e sinalizou corretamente confiança **Baixa**. | Não é erro do assistente — é lacuna de corpus/produto (tier não documentado) | Interface/Produto (fluxo de solicitação de novo tier) | Comportamento do assistente é o padrão-ouro a ser replicado nos demais casos. Reclassificado: o "erro" é do dado/produto, não da resposta. |
| 6 | (a) Fonte citada ("FAQ-Atendimento, item 32") não corresponde ao formato/nome do FAQ real do corpus (informal, sem itens numerados) — indício de citação inventada. (b) Não menciona que o prazo pode se estender por autorizações adicionais (compliance). Tema de alto risco regulatório (carga perigosa + frete expresso) respondido com confiança Alta. | Fonte não confiável **+** Informação incompleta | Pipeline (dados) **+** Harness (regra de HITL obrigatório) | É o caso de maior risco do lote: mesmo corrigindo a fonte, uma resposta neste tema não deveria sair ao atendente sem validação humana. |

**Nota A (Par 1):** manter como item aberto até confirmação da fonte — evita atribuir a um ajuste de prompt um problema que pode ser de conteúdo-fonte.

---

## 3. Tipos de ajuste — taxonomia consolidada

A taxonomia original (Prompt / Pipeline / Interface) cobre bem correções pontuais, mas **nenhum dos 6 casos** foi endereçado por uma camada que impeça a *recorrência* do erro. Propõe-se adicionar uma quarta categoria:

| Camada | O que resolve | Quando usar |
|---|---|---|
| **Prompt** | Instrução ao modelo (formato de resposta, o que incluir/não incluir, como parafrasear) | Erros de geração: ambiguidade evitável, adição de conteúdo não solicitado |
| **Pipeline** | Ingestão, chunking, retrieval, cobertura documental | Erros de recuperação: chunk errado, documento ausente/desatualizado, lacuna de corpus |
| **Interface/Produto** | Fluxos além da resposta em si (ex.: solicitar classificação de tier, abrir chamado) | Lacunas de processo/produto reveladas pela interação, não pela resposta |
| **Harness (novo)** | Regras estruturais que bloqueiam ou desviam a resposta antes de chegar ao atendente, independente da causa raiz | Combinações de risco que devem ser impedidas *sempre*: confiança alta sem fonte, tema sensível com fonte fraca, reuso suspeito de citação |

Casos 3, 4 e 6 devem carregar **Harness** como ajuste adicional, não substituto dos ajustes de Prompt/Pipeline já identificados.

---

## 4. Proposta de schema de structured output

```json
{
  "answer": "string, obrigatório",
  "source_document": "string, obrigatório — nome exato do documento conforme lista oficial (POL-001, PROC-042, SLA-2024, FAQ-Atendimento)",
  "source_section": "string, obrigatório quando source_document existir — seção/item específico",
  "confidence_score": "enum obrigatório: alta | media | baixa",
  "risk_topic": "boolean, obrigatório — true se o tema envolver carga perigosa, compliance, reembolso/valores ou classificação regulatória",
  "requires_human_review": "boolean, calculado — ver regras HITL na seção 5"
}
```

**Regras de validação programática (rejeição automática, não sugestão):**
- Se `confidence_score = "alta"` **e** `source_document` estiver vazio/nulo → resposta rejeitada, reprocessar ou escalar.
- Se `source_document` não pertencer à lista de documentos oficiais conhecidos → resposta rejeitada (impede citação com nome/formato inventado, como no caso 6).
- Se `source_section` estiver ausente quando `source_document` existir → aviso, não bloqueio (mas fica registrado para auditoria).

---

## 5. Regras propostas de HITL (Human-in-the-Loop)

| Gatilho | Condição | Ação |
|---|---|---|
| HITL-1 | `risk_topic = true` **e** `confidence_score != "alta"` | Bloquear resposta automática; encaminhar para validação humana antes de enviar ao atendente |
| HITL-2 | `risk_topic = true` **e** `source_document` fora da lista oficial | Bloquear (cobre o padrão do caso 6) |
| HITL-3 | Mesma seção de `source_document` citada para dois `topics` semanticamente distintos numa janela de N perguntas | Sinalizar para revisão de retrieval (cobre o padrão do caso 3) |
| HITL-4 | `source_document = null` | Bloquear independentemente da confiança (cobre o padrão do caso 4) |

---

## 6. Casos de referência para calibração

- **Padrão a reforçar:** caso 5 — reconhecimento de limite documental, listagem de alternativas reais, confiança calibrada como Baixa. Deve ser usado como exemplo positivo em few-shot/prompt.
- **Padrões a bloquear:** casos 3, 4 e 6 — cada um representa uma combinação distinta de falha (adição não lastreada, ausência de fonte com confiança alta, citação com formato inconsistente em tema de risco).

---

## Changelog

| Versão | Data | Alteração |
|---|---|---|
| v1.0 | 2026-09-20 | Consolidação inicial da avaliação técnica (Claude) e avaliação pessoal (Rodrigo) dos 6 casos de staging, com proposta de schema de structured output e regras de HITL |
