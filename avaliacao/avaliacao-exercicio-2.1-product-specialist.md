# Avaliação do Exercício 2.1 — Recorte de domínio e spec SDD (Product Specialist)

> **Programa:** Trilha de Certificação AI First — DGS / DB1 Global Software
> **Papel:** Product Specialist
> **Cenário:** 2 — Estruturação do Trabalho
> **Exercício:** 2.1 — Recorte de domínio e spec SDD (query endpoint)
> **Data da avaliação:** 25/08/2026

---

## Resumo

Entregável de nível muito alto. O recorte de domínio separa corretamente negócio de tecnologia (inclusive corrigindo, numa autocrítica genuína, um erro real de vazamento técnico da própria v1 — BC-2 nomeado "RAG", menções a "Teams" e "Azure AD/SharePoint"), e o `requirements.md` do `query-endpoint` passou por duas rodadas de revisão documentadas com diffs concretos (não cosméticos): uma sobre coerência dos scope boundaries com os bounded contexts (achou e corrigiu uma dependência de BC-3 não declarada) e outra sobre estrutura do Verification Criteria (de 10 para 14 VCs, com limiares marcados como "[a validar]" em vez de escondidos). O mockup no Claude Design está ancorado outcome a outcome, inclusive tratando com maturidade o caso limite do O9 (outcome que não cabe numa tela única).

---

## Scores por Dimensão

| Dimensão | Score | Justificativa |
|----------|-------|----------------|
| D1 — Domínio Conceitual | 3 | Distingue corretamente subdomínio de negócio vs. bounded context vs. módulo técnico do Anexo C; usa corretamente Shared Kernel, relações Customer/Supplier, Conformist e Upstream/Partnership no mapa de contexto; aplica um teste operacional próprio e correto para validar um bounded context ("deve sobreviver a uma troca de stack"). Nuance específica ao projeto, não genérica. |
| D2 — Uso de Ferramentas | 3 | Transcrição mostra iteração real e sequencial (recorte → revisão de coerência → requirements.md → revisão de scope boundaries → revisão de verification criteria → mockup → ajuste do O9). Diferenças entre versões do `requirements.md` (`_1` → `_2` → `_3`) e do recorte de domínio foram verificadas linha a linha: são estruturais (dependência de BC-3 adicionada, 4 VCs novos, 3 VCs reescritos), não cosméticas. Claude Design usado com iteração (8º artboard adicionado depois, republicação no mesmo link). |
| D3 — Qualidade do Entregável | 3 | Completo e específico ao NovaTech: outcomes sem nenhuma menção a "endpoint/embedding/API", scope boundaries com remissão cruzada a BCs específicos, Prior Decisions citando ADR-0001 a ADR-0004 e REQs específicos, 14 Verification Criteria majoritariamente binários e testáveis (limiares não fechados ficam marcados como "[a validar]", não maquiados por linguagem qualitativa). Mockup confirma na prática fonte, confiança, PROC-042, Platinum, carga perigosa e sinalização/feedback. |
| D4 — Pensamento Crítico | 3 | Autocrítica genuína em pelo menos 3 pontos: reconhece e corrige o próprio erro técnico da v1 do recorte; identifica que a meta de 30s (C1) não pode ser cobrada isoladamente do módulo; e trata o O9 como categoria estrutural diferente dos demais outcomes (propriedade de população ao longo do tempo, não de uma interação), inclusive diferenciando calibração de acomodação. Não há aceitação acrítica em nenhum ponto revisado. |
| D5 — Aplicabilidade ao Projeto | 3 | Profundamente conectado: referencia as 4 ADRs simuladas (inclusive o orçamento de contexto da ADR-0002), usa a linguagem ubíqua do domínio de ponta a ponta, respeita a estrutura de `/specs/` do Anexo C e liga explicitamente cada scope boundary a um bounded context nomeado. |

**Score do exercício: 3.0**

---

## Verificação de Artefatos Machine-Readable

Este exercício não pede AGENTS.md/skill (isso é 2.2/2.3), mas o `requirements.md` já é razoavelmente acionável por outro agente/membro do time: seções numeradas e nomeadas conforme o padrão SDD, VCs com ID (`VC-01`...`VC-14`) e critério binário explícito, tabela de Prior Decisions com referência direta a ADRs.

Pontos bons concretos: VC-04 ("qualquer SLA numérico atribuído a 'Platinum' reprova o caso") e VC-11 (regras objetivas por estado de vigência) são diretamente conversíveis em casos de teste sem interpretação.

Ponto a observar: VC-01 e VC-07 ainda dependem de um número "[a validar com o Tech Lead]" — isso é tratado corretamente como lacuna assumida (não escondida), mas significa que esses dois critérios especificamente ainda não são 100% executáveis por um QA sem essa definição externa.

---

## Pontos Fortes

1. **Autocorreção documentada e verificável** do próprio erro de vazamento técnico no recorte de domínio (v1: "RAG"/"Teams"/"Azure AD" → v2: vocabulário de negócio puro), com o teste "sobrevive a uma troca de stack?" registrado como critério permanente de revisão.
2. **Duas rodadas de revisão cruzada** entre `requirements.md` e recorte de domínio (scope boundaries e verification criteria), cada uma com metodologia explícita, achados classificados por severidade e correção aplicada nos dois documentos simultaneamente quando a inconsistência vivia nos dois.
3. **Tratamento maduro do Outcome O9**: em vez de forçar um Verification Criteria ou uma tela de mockup onde não cabe, reconhece a diferença estrutural e produz um plano de medição pós-go-live separado, com métrica de calibração (não só volume) para distinguir aprendizado real de excesso de confiança.

---

## Pontos de Melhoria

1. Fechar os limiares "[a validar]" de VC-01 e VC-07 antes do Gate Spec → Plan, já que hoje dependem de uma decisão externa (Tech Lead) para serem de fato testáveis por QA — vale registrar quem e quando isso será decidido.
2. O recorte de domínio marca BC-1 e BC-2 como "Core" simultaneamente; a própria autocrítica já levanta a alternativa de tratar BC-1 como camada de apresentação — vale levar essa decisão ao Tech Lead/Delivery Manager explicitamente, como o próprio documento já sugere nos "Próximos passos".
3. O Shared Kernel de tier do cliente é citado como ponto de maior risco de divergência silenciosa entre módulos, mas ainda não há uma ADR abrindo essa decisão (serviço compartilhado vs. tabela única) — os "Próximos passos" já apontam isso; vale garantir que não fique só como recomendação.

---

## Classificação

**Aprovado com distinção** (2.5–3.0)

---

## Tópicos da Trilha para Reforço

Não aplicável — score acima de 2.5.
