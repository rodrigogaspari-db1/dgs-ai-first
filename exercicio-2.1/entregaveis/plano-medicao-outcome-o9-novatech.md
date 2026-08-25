# Plano de Medição — Outcome O9 (Confiança do Atendente Crescente)
## Projeto NovaTech — Query Endpoint

**Outcome de referência:** O9 — "A confiança do atendente na ferramenta cresce com o uso, não diminui" (`requirements-query-endpoint-novatech.md`, Seção 1).
**Natureza deste documento:** diferente das demais seções do requirements.md, O9 não gera um Verification Criteria (QA não pode testá-lo antes do go-live) nem uma tela de resposta (não é um estado observável numa única interação). Este documento existe para que O9 não fique órfão de acompanhamento só porque não se encaixa nos formatos dos outros oito outcomes.

---

## 1. Por que O9 é estruturalmente diferente de O1–O8

Os outros oito outcomes descrevem uma propriedade de uma resposta individual — citar fonte, declarar conflito, preservar uma restrição. Cada um pode ser verificado numa amostra de perguntas antes do lançamento, e por isso cada um tem um VC na Seção 5.

O9 descreve uma propriedade de uma **população de interações ao longo do tempo**: a taxa de escalonamento por falta de confiança caindo porque o atendente aprendeu a calibrar quando confiar e quando checar. Não existe amostra de teste pré-go-live que prove isso — só existe depois, com uso real, e só se estabiliza depois de meses, não de dias. É por isso que a Revisão Crítica do requirements.md (Seção 6) já recomendava tratá-lo como indicador de produto pós-go-live, não como critério de aceite de implementação.

---

## 2. Métrica principal

**Taxa de escalonamento por falta de confiança na resposta** — proporção de interações em que o atendente escala o atendimento (para um colega, supervisor, ou outro canal) declarando como motivo a falta de confiança na resposta recebida, sobre o total de interações no período.

**Baseline (discovery, Fase 1):** ~19% no geral; ~30% especificamente em temas de frete.

**Meta de direção (não um número-alvo fixo nesta fase):** tendência de queda sustentada ao longo do tempo, sem exigir um piso predefinido — o valor certo de equilíbrio (quanto de escalonamento é saudável vs. sintoma de desconfiança residual) é algo a se descobrir com os primeiros meses de operação, não a fixar agora.

---

## 3. Métrica complementar — calibração, não só volume

Uma queda na taxa de escalonamento, sozinha, é ambígua: pode significar aprendizado real (o atendente aprendeu a distinguir quando confiar) ou acomodação (o atendente passou a confiar cegamente, inclusive quando não deveria — o inverso do que O9 pretende). Por isso a taxa de escalonamento precisa ser sempre lida ao lado de uma segunda métrica:

**Escalonamento segmentado pelo nível de confiança que o assistente atribuiu à resposta** (Alta / Média / Baixa / conflito declarado). O padrão saudável esperado:

- Respostas de confiança **Alta**: escalonamento baixo e caindo — o atendente aprendendo a confiar quando é seguro confiar.
- Respostas de confiança **Média**, **Baixa** ou com **conflito declarado**: escalonamento se mantém presente — não deve cair a zero, porque nesses casos verificar é o comportamento correto, não desconfiança residual.

Se a taxa geral cair mas a queda vier concentrada em respostas de Baixa confiança ou conflito, isso é sinal de alerta (excesso de confiança), não de sucesso do outcome — o inverso do texto de O9 ("em vez de confiar cegamente em tudo").

**Verificação cruzada de qualidade (amostragem contínua):** para distinguir "aprendeu a calibrar" de "parou de checar", uma amostra periódica de respostas de confiança Alta que **não** foram escaladas deve continuar sendo auditada com os mesmos critérios de VC-02 a VC-06 do `requirements.md` — se a taxa de acerto real dessas respostas cair, o problema não é do atendente, é do módulo, e o outcome não pode ser declarado "atingido" só porque o escalonamento caiu.

---

## 4. Segmentação

- **Por tema:** geral vs. frete (a baseline já mostra que frete parte de um patamar pior, ~30%, e a curva de melhora provavelmente será mais lenta ali).
- **Por nível de confiança da resposta:** Alta / Média / Baixa / conflito declarado (Seção 3 acima).
- **Por tempo de exposição do atendente à ferramenta** (coorte por semana de uso, não só data-calendário): a curva de aprendizado é individual — medir só a média global de todos os atendentes mistura quem está na semana 1 com quem está no mês 3, e pode mascarar o efeito de aprendizado com o efeito de rotatividade de equipe (atendentes novos entrando constantemente).

---

## 5. Cadência e responsabilidade

- **Cadência de leitura:** mensal, a partir do go-live — não antes, porque o efeito de aprendizado não se manifesta em dias.
- **Dono do indicador:** Product Specialist / time de produto do assistente, em conjunto com a liderança de atendimento — não o QA (não é VC) e não o Tech Lead do módulo isoladamente (não é algo que o `query-endpoint` entrega sozinho, ver Seção 6 do requirements.md).
- **Uso pretendido:** indicador de acompanhamento de adoção pós-go-live. Não bloqueia o Gate Spec → Plano, não é critério de aceite do `plan.md` ou do `tasks.md`, e uma leitura ruim no primeiro mês não deve ser tratada como falha de implementação — deve disparar investigação de causa (calibração real dos VCs? treinamento insuficiente? UI confusa?) antes de qualquer ação corretiva.

---

## 6. O que isto não é

Para deixar explícito, evitando que este plano seja mal-lido como uma tentativa de transformar O9 num VC pela porta dos fundos: este documento **não** adiciona nada à Seção 5 (Verification Criteria) do `requirements.md`, **não** é pré-condição para a aprovação do Gate Spec → Plano, e **não** atribui ao módulo `query-endpoint` a responsabilidade por um número de escalonamento — atribui a ele, apenas, a responsabilidade indireta de fazer bem O1–O8, que é a única coisa que este módulo controla e que, por hipótese, é a causa do efeito descrito em O9.
