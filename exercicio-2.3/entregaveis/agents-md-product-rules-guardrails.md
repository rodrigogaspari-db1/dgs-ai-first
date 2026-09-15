## Product Rules & Guardrails (Product Specialist)

**Versão da seção:** 1.1 · **Última atualização:** 2026-09-14 · **Responsável:** Product Specialist · **Aprovação requerida de:** Tech Lead

> **Escopo:** regras de comportamento do assistente NovaTech que todo agente de IA (Copilot, Claude Code, ou qualquer LLM operando neste repositório) DEVE seguir ao gerar prompts, código de resposta, dados de teste ou documentação de produto. Esta seção é normativa: prescreve o que fazer, não descreve o que existe.
>
> **Fonte de verdade:** `docs/product/guardrails.md` (v1.1), validada contra os incidentes INC-01, INC-02, INC-03 e a documentação de negócio em `docs/novatech/` (Anexo A). Qualquer mudança na fonte de verdade exige atualização desta seção — ver Seção 5 (Controle de versão).

---

### 0. Bloco machine-readable

> Bloco de parsing programático. Agentes de IA e scripts de validação (ex: linter de guardrails no CI) devem consumir **este bloco**, não o texto em prosa das seções 1–4, para checagens automatizadas. As tabelas nas seções seguintes são a versão legível para humanos do mesmo conteúdo — os `id` são compartilhados entre as duas.

```yaml
section_id: product_rules_guardrails
section_version: "1.1"
last_updated: "2026-09-14"
source_of_truth: docs/product/guardrails.md
source_of_truth_version: "1.1"
owner_role: product_specialist
approver_role: tech_lead

rules:
  - id: D1
    category: MUST
    enforcement: deterministic
    statement: "Every response MUST cite the source document identifier and section."
    schema_field: source_document, source_section
    prevents_incident: INC-03
  - id: D2
    category: MUST
    enforcement: deterministic
    statement: "Every response JSON MUST include source_document, even at low confidence or no match."
    schema_field: source_document
    prevents_incident: [INC-02, INC-03]
  - id: D3
    category: MUST
    enforcement: probabilistic
    statement: "Every response MUST be written in formal Portuguese."
    schema_field: null
    prevents_incident: null
  - id: D4
    category: MUST
    enforcement: conditional_deterministic
    condition: "requires versao_status metadata in index"
    statement: "When two document versions exist, MUST use the most recent and MUST disclose that a prior version exists."
    schema_field: contradiction_flag
    prevents_incident: INC-02
  - id: D5
    category: MUST
    enforcement: deterministic
    statement: "Queries combining categoria_carga=perigosa with intent=devolucao MUST return an explicit negative, pre-LLM."
    schema_field: null
    prevents_incident: INC-01
  - id: N1
    category: MUST_NOT
    enforcement: probabilistic
    statement: "MUST NOT generate numeric values (deadlines, multipliers, SLA percentages) not literally present in indexed documentation."
    prevents_incident: [INC-01, INC-02]
  - id: N2
    category: MUST_NOT
    enforcement: deterministic
    statement: "MUST NOT state that carga perigosa (ANTT classes 1-6) is eligible for standard-process devolution."
    prevents_incident: INC-01
  - id: N3
    category: MUST_NOT
    enforcement: deterministic
    statement: "MUST NOT reference a customer tier outside the closed enum {Gold, Silver, Standard}."
    schema_field: tier_mentioned
    prevents_incident: null
  - id: N4
    category: MUST_NOT
    enforcement: probabilistic
    statement: "MUST NOT treat docs/novatech/FAQ-atendimento.md as normative-equivalent to POL/PROC/SLA documents."
    prevents_incident: INC-03
  - id: F1
    category: FALLBACK
    enforcement: probabilistic
    statement: "IF confidence=low THEN response MUST be prefixed with an explicit low-confidence warning."
    schema_field: confidence
    prevents_incident: INC-03
  - id: F2
    category: FALLBACK
    enforcement: probabilistic
    statement: "IF confidence=low THEN response MUST suggest escalation to the relevant sector."
    prevents_incident: [INC-01, INC-03]
  - id: F3
    category: FALLBACK
    enforcement: conditional_deterministic
    condition: "deterministic below similarity threshold; threshold-crossing decision is probabilistic"
    statement: "IF no match above threshold THEN response MUST explicitly state 'não encontrei essa informação'."
    prevents_incident: INC-03
  - id: F4
    category: FALLBACK
    enforcement: conditional_deterministic
    condition: "requires contradicao_pendente metadata in index"
    statement: "IF unresolved document contradiction exists THEN response MUST expose both versions with sources and flag the divergence."
    schema_field: contradiction_flag
    prevents_incident: INC-02

glossary_terms:
  - cliente_gold_silver_standard
  - carga_perigosa
  - carga_refrigerada_cadeia_de_frio
  - frete_especial
  - multiplicador_regional
  - sla_primeira_resposta
  - sla_resolucao
  - incidente_critico
  - cte
  - documento_normativo_vs_informal
  - versao_vigente_vs_obsoleta
```

---

### 1. Regras de comportamento

Cada regra referencia o `id` do bloco machine-readable (Seção 0). Formato: **um comando, um verbo, um efeito verificável** — a justificativa de negócio vem em nota separada, não misturada ao comando.

#### DEVE

| ID | Comando | Enforcement | Incidente prevenido |
|---|---|:---:|---|
| D1 | Citar a fonte com identificador do documento e seção em toda resposta (ex: `SLA-2024, seção 2`). | `[D]` — campo obrigatório de schema, validado antes do envio. | INC-03 |
| D2 | Incluir o campo `source_document` no JSON de retorno em toda resposta, inclusive quando a confiança for baixa ou não houver match. | `[D]` — rejeição de payload por validação de schema (Zod). | INC-02, INC-03 |
| D3 | Redigir toda resposta em português formal. | `[P]` — instrução de estilo no system prompt; sem verificação automatizada nesta fase. | — |
| D4 | Ao existir mais de uma versão de um documento, usar os valores da versão mais recente e declarar explicitamente a existência da versão anterior. | `[D]` **condicional** — determinístico somente se `versao_status` existir no índice; caso contrário, regride a `[P]`. | INC-02 |
| D5 | Retornar negativa explícita, sem prazo padrão, para toda consulta que combine categoria=carga-perigosa com intenção=devolução. | `[D]` — interceptado por `response-validator.ts` antes da resposta do LLM ser exposta. | INC-01 |

#### NÃO DEVE

| ID | Comando | Enforcement | Incidente prevenido |
|---|---|:---:|---|
| N1 | Não gerar valor numérico (prazo, multiplicador, percentual de SLA, valor de frete) ausente na documentação indexada. | `[P]` — sem grounding-check automatizado nesta fase; risco residual conhecido. | INC-01, INC-02 |
| N2 | Não afirmar, em nenhuma circunstância, que carga perigosa é elegível para devolução pelo processo padrão. | `[D]` — mesma trava de D5. | INC-01 |
| N3 | Não referenciar tier de cliente fora do conjunto fechado {Gold, Silver, Standard}. | `[D]` — validação contra enum fechado no schema. | — |
| N4 | Não citar `docs/novatech/FAQ-atendimento.md` como fonte normativa equivalente a POL/PROC/SLA; citar apenas como "prática informal do time". | `[P]` — depende do metadado `tipo: normativo \| informal` ser respeitado pelo prompt. | INC-03 |

#### QUANDO EM DÚVIDA (fallback)

| ID | Comando | Enforcement | Incidente prevenido |
|---|---|:---:|---|
| F1 | Prefixar a resposta com aviso de baixa confiança sempre que a confiança da recuperação for baixa. | `[P]` — o limiar de "baixa confiança" é decisão do modelo/score, não uma regra fixa. | INC-03 |
| F2 | Sugerir escalação ao setor responsável (ex: Gestão de Riscos, ramal 4500) sempre que a confiança for baixa. | `[P]` | INC-01, INC-03 |
| F3 | Responder explicitamente "não encontrei essa informação" quando não houver match acima do threshold de busca — nunca inferir silenciosamente. | `[D]` parcial — o fallback é determinístico dado o threshold, mas o cálculo do threshold é `[P]`. | INC-03 |
| F4 | Expor ambas as versões de um documento em contradição não resolvida, com fontes, e declarar a divergência pendente de Compliance. | `[D]` **condicional** — determinístico somente se `contradicao_pendente` existir no índice. | INC-02 |

> **Nota de rastreabilidade (Cenário 1):** toda regra marcada `[D]` **condicional** depende de ADR-0002 (context budget) e ADR-0003 (tratamento de contradições) serem implementadas no pipeline de ingestão com os campos `versao_status`, `categoria_carga` e `contradicao_pendente`. Ausência desses campos = regressão automática de `[D]` para `[P]` — tratar como risco de produto (não como detalhe técnico) e revalidar na aprovação de `specs/pipeline-ingestao/plan.md`.

---

### 2. Glossário de linguagem ubíqua

Cada termo inclui o motivo pelo qual um LLM sem contexto de domínio erraria — essa coluna existe para justificar por que o termo precisa estar aqui, não é decorativa.

| Termo | Definição vinculante | Risco de confusão para o LLM sem este glossário | Fonte |
|---|---|---|---|
| **Cliente Gold / Silver / Standard** | Único conjunto válido de tiers, por volume mensal e valor de contrato. Não existe "Platinum". | O modelo pode inventar um tier plausível (ex: "Platinum") por analogia com programas de fidelidade genéricos que viu em treino. | SLA-2024, seção 1 |
| **Carga perigosa** | Classes 1–6 da ANTT (Res. 5.947/2021): explosivos, gases, inflamáveis, oxidantes, tóxicos/infectantes. Nunca elegível para devolução padrão. | O modelo pode tratar "perigosa" como julgamento subjetivo de risco em vez de classificação regulatória fechada, e aplicar a regra geral de devolução por engano. | POL-001, seção 3.2 |
| **Carga com cadeia de frio rompida** | Temperatura fora da faixa da nota fiscal por mais de 30 min contínuos (sensor IoT). Também inelegível para devolução padrão. | O modelo pode confundir com "carga refrigerada" genérica e assumir que toda carga refrigerada é inelegível, quando só é a que rompeu a cadeia. | POL-001, seção 3.2 |
| **Frete especial** | Frete para cargas **acima de 500kg**, calculado por `Valor base × Multiplicador regional × Fator de peso`. | O modelo pode generalizar "frete especial" como qualquer frete fora do padrão (expresso, internacional, etc.), quando o termo tem definição fechada por peso. | PROC-042 / PROC-042-v2 |
| **Multiplicador regional** | Fator por região de destino. **Diverge entre PROC-042 v1 e v2** sem marcação formal de vigência. | O modelo pode escolher a v1 ou v2 arbitrariamente, ou fazer média das duas, achando que está "conciliando" fontes — deve tratar como contradição pendente (regra F4), nunca resolver por conta própria. | PROC-042, seção 2.1 / PROC-042-v2, seção 2.1 |
| **SLA de primeira resposta** | Prazo do primeiro retorno (mesmo que "estamos verificando"). | O modelo tende a tratar "resposta" e "resolução" como sinônimos em linguagem natural — aqui são métricas distintas com prazos diferentes. | SLA-2024, seção 2 |
| **SLA de resolução** | Prazo para o problema ser efetivamente resolvido; sempre maior que o de primeira resposta. | Mesmo risco acima — confusão resposta/resolução pode fazer o modelo informar o prazo errado ao cliente. | SLA-2024, seção 2 |
| **Incidente crítico** | Definição fechada por 4 critérios (valor > R$100k + status desconhecido > 6h; carga perigosa com irregularidade; >5 chamados/24h; risco a pessoas). | O modelo pode usar "crítico" como avaliação subjetiva de urgência do texto do cliente, em vez de aplicar os 4 critérios objetivos. | SLA-2024, seção 3 |
| **CT-e** | Identificador obrigatório em toda solicitação de devolução. | O modelo pode aceitar outros identificadores (nota fiscal, número de pedido) como substitutos válidos sem saber que o CT-e é especificamente exigido. | POL-001, seção 3.3 |
| **Documento normativo vs. informal** | Normativo = POL/PROC/SLA (responsável formal, uso obrigatório). Informal = FAQ (sem responsável formal, não validado). | O modelo, ao fazer RAG, trata todo chunk recuperado com o mesmo peso de autoridade — sem esta distinção, pode citar o FAQ como se fosse regra oficial. | Anexo A, metadados por documento |
| **Versão vigente vs. obsoleta** | Documentos obsoletos são marcados, não excluídos do índice (ADR-0003). Ausência de marcação não implica vigência. | O modelo pode assumir que "o documento mais recente por data" é automaticamente o vigente, quando a marcação formal (ausente no caso PROC-042) é o critério real definido pelo projeto. | ADR-0003; PROC-042-v2, seção 5 |

**Gaps documentais** (fora do glossário por não terem documento normativo): seguro de carga, carga danificada em trânsito, frete padrão abaixo de 500kg. Qualquer resposta sobre esses temas é low-confidence por definição (regras F1/F2), independentemente do que o FAQ informal diga.

---

### 3. Restrições que impactam geração de código

Aplicam-se a `src/functions/query/`, `src/functions/feedback/` e `src/services/`. Cada restrição inclui exemplo DO/DON'T para orientar diretamente o Copilot/Claude Code — não apenas descrição textual.

**3.1 — Schema de resposta obrigatório (implementa D1, D2)**

```typescript
// DO
interface AssistantResponse {
  answer: string;
  source_document: string;        // OBRIGATÓRIO mesmo em low-confidence ou "não encontrado"
  source_section?: string;
  confidence: "high" | "low";
  tier_mentioned?: "Gold" | "Silver" | "Standard"; // enum fechado — nunca string livre
  contradiction_flag?: boolean;   // true quando F4 se aplica
}

// DON'T — omite source_document e usa string livre para tier
interface AssistantResponse {
  answer: string;
  confidence: string;
  tier?: string;
}
```
PR que gerar o padrão DON'T acima NÃO DEVE passar em code review (ver Testing Standards).

**3.2 — Validação de carga perigosa é pré-LLM (implementa D5, N2)**

```typescript
// DO — trava determinística antes de qualquer chamada ao modelo
if (categoria_carga === "perigosa" && intent === "devolucao") {
  return buildNegativeResponse({ source_document: "POL-001", source_section: "3.2" });
}
const llmResponse = await completion(...);

// DON'T — depende só do prompt para o modelo "lembrar" da regra
const llmResponse = await completion({
  systemPrompt: "Lembre-se: carga perigosa não pode ser devolvida.",
  ...
});
return llmResponse; // sem trava de código, N2 pode falhar silenciosamente
```

**3.3 — Enum fechado de tier (implementa N3)**

```typescript
// DO
const TIER = z.enum(["Gold", "Silver", "Standard"]);

// DON'T
const tier: string = extractTierFromText(answer); // aceita qualquer valor, inclusive "Platinum"
```

**3.4 — Sem hardcoding de valores de negócio**

```typescript
// DO — lê da camada de dados/índice
const multiplier = await getRegionalMultiplier(region, activeVersion);

// DON'T — valor de negócio congelado no código-fonte
const multiplier = region === "Sul" ? 1.2 : region === "Norte" ? 1.6 : 1.0;
```
Justificativa: uma correção documental (ex: resolução da contradição PROC-042) não pode depender de novo deploy de código.

**3.5 — Metadados de vigência como pré-condição de enforcement determinístico (implementa D4, F4)**

`src/pipeline/indexer.ts` DEVE gravar `versao_status`, `categoria_carga` e `contradicao_pendente` no índice. `src/services/search.ts` que não propague esses campos para a resposta compromete a classificação `[D]` de D4/D5/N2/F4 — tratar como **bug de guardrail**, com a mesma severidade de um bug funcional, não como detalhe de implementação.

**3.6 — Testes de guardrail são obrigatórios em PR**

Todo PR que altere `response-validator.ts` ou `response-builder.ts` DEVE incluir teste cobrindo ao menos um dos incidentes INC-01/02/03, usando fixtures de `tests/fixtures/`. PR sem esse teste é bloqueado no Gate 3 (Code → Merge).

---

### 4. Referências a documentos de spec no repositório

| Documento | Caminho | Relação com esta seção |
|---|---|---|
| Guardrails formalizados (fonte de verdade) | `docs/product/guardrails.md` (v1.1) | Origem de todas as regras da Seção 1. |
| Requirements do query endpoint | `specs/query-endpoint/requirements.md` | Deve referenciar D1, D2, D5, N1, N2 como verification criteria. |
| Requirements da API de feedback | `specs/feedback-api/requirements.md` | Deve usar o mesmo schema `AssistantResponse`. |
| Plano do pipeline de ingestão | `specs/pipeline-ingestao/plan.md` | Responsável por implementar `versao_status`, `categoria_carga`, `contradicao_pendente`. |
| ADR-0002 — Estratégia de contexto | `docs/adr/0002-*.md` | Sustenta o context budget que limita as citações de D1/D2. |
| ADR-0003 — Documentos contraditórios | `docs/adr/0003-*.md` | Base da regra F4 e do glossário "vigente vs. obsoleto". |
| Documentação de negócio NovaTech | `docs/novatech/` (Anexo A) | Fonte de verdade do glossário e das regras; toda atualização deve disparar revisão desta seção. |
| Fixtures de teste de guardrail | `tests/fixtures/expected-responses.ts` | Deve conter ao menos um caso por incidente citado na Seção 1. |

---

### 5. Controle de versão desta seção

Esta seção é um artefato vivo — toda mudança em uma regra, no glossário ou nas restrições de código DEVE ser versionada aqui, não apenas no arquivo-fonte (`docs/product/guardrails.md`).

**Processo de atualização:**
1. Qualquer mudança de regra (nova, removida, ou reclassificação `[D]`↔`[P]`) exige um novo incidente documentado, uma decisão de ADR, ou aprovação explícita do Tech Lead — nunca uma edição silenciosa.
2. Toda mudança incrementa a versão: **patch** (x.x.N — correção de texto, sem mudança de regra), **minor** (x.N.0 — nova regra ou novo termo de glossário), **major** (N.0.0 — mudança que altera contrato de schema/API consumido por código já implementado).
3. O campo `section_version` e `last_updated` no bloco YAML (Seção 0) DEVEM ser atualizados na mesma alteração — agentes automatizados dependem desses campos para detectar que precisam reprocessar a seção.
4. Toda entrada do changelog abaixo referencia o `id` de regra afetado, quando aplicável.

| Versão | Data | Autor | Mudança |
|---|---|---|---|
| 1.0 | 2026-09-14 | Product Specialist | Versão inicial: regras D1–D5/N1–N4/F1–F4, glossário, restrições de código e referências de spec. |
| 1.1 | 2026-09-14 | Product Specialist (revisão solicitada pelo Tech Lead) | Adicionado bloco machine-readable (Seção 0) com IDs para parsing automatizado; separado comando de justificativa nas regras; adicionada coluna "risco de confusão para o LLM" no glossário; adicionados exemplos DO/DON'T nas restrições de código; adicionado este processo de controle de versão. |
