# Revisão de Estrutura — Verification Criteria do `requirements.md` (Query Endpoint)
## Projeto NovaTech

**Objeto revisado:** Seção 5 (Verification Criteria, VC-01 a VC-10) do `requirements-query-endpoint-novatech.md`
**Pergunta que orienta esta revisão:** (1) todo Outcome e toda Constraint têm ao menos um critério de verificação correspondente? (2) cada VC existente é, de fato, testável por um QA sem depender de interpretação pessoal?

---

## 1. Metodologia

Mesma rubrica já usada na revisão de testabilidade da spec de RAG do projeto: um VC é **testável** quando (a) tem critério de teste explícito, (b) tem resultado binário ou mensurável com limiar definido, (c) não depende de um dado ainda não fechado, e (d) não depende de um artefato não especificado. Além disso, esta revisão acrescenta um passo que a anterior não tinha: uma **matriz de rastreabilidade** ligando cada Outcome (O1–O9) e cada Constraint (C1–C6) a pelo menos um VC — é esse cruzamento que revela a lacuna apontada.

---

## 2. Matriz de rastreabilidade — Outcomes/Constraints → Verification Criteria

| Item | VC correspondente | Situação |
|---|---|---|
| O1 — Resposta em segundos | VC-01 | ✅ Coberto |
| O2 — Resposta verificável | VC-02 | ✅ Coberto |
| O3 — Conflito declarado, não escolhido em silêncio | VC-03 | ✅ Coberto |
| O4 — Ausência de informação comunicada | VC-05 | ✅ Coberto |
| O5 — Entidade inexistente não fabricada | VC-04 | ✅ Coberto |
| O6 — Regra proibitiva não invertida | VC-06 | ✅ Coberto |
| O7 — Conteúdo restrito protegido | VC-07 | ✅ Coberto |
| O8 — Auditabilidade | VC-08 | ✅ Coberto |
| O9 — Confiança do atendente cresce com o uso | *nenhum* | ⚪ Sem VC — **mas já justificado** na Seção 6 do próprio documento (é indicador de adoção pós-go-live, não critério de aceite de implementação). Ausência correta, não é lacuna. |
| C1 — Orçamento de tempo | VC-01 | ✅ Coberto (com ressalva — ver Seção 3) |
| C2 — Idioma e tom | *nenhum* | 🔴 **Lacuna** |
| C3 — Atualidade herdada da ingestão | *nenhum* | 🔴 **Lacuna — é aqui que mora a "VC de ingestão" que você identificou** |
| C4 — Confidencialidade inegociável | VC-07 | ✅ Coberto |
| C5 — Continuidade sem degradação | VC-10 | ✅ Coberto (com ressalva — ver Seção 3) |
| C6 — Toda resposta em exatamente uma categoria | *parcialmente* (VC-02/03/05/09 testam cada categoria isolada) | 🟡 **Lacuna parcial** — falta testar a exclusividade mútua, não só a existência de cada categoria |
| *(nenhum outcome/constraint cobre isto diretamente, mas decorre do escopo "aplicar o ciclo de vida de validade do documento")* | *nenhum* | 🔴 **Lacuna — segunda metade da "VC de ingestão"** |

**Confirmação direta à sua observação:** você está certo — **não existe VC cobrindo o que a ingestão de dados produz e que este módulo é obrigado a respeitar.** Duas lacunas distintas compõem isso, detalhadas na Seção 4.

---

## 3. Testabilidade dos VCs existentes

| VC | Veredito | Nota |
|---|---|---|
| VC-01 | 🟡 Parcial | "o tempo de processamento deste módulo... **permite cumprir** a meta fim-a-fim de 30s" não dá um número para a fatia deste módulo — dois QAs poderiam divergir sobre quanto tempo "permite cumprir". Falta um limiar próprio (ex.: "até Xs, a validar com o Tech Lead"), não apenas herdar o total. |
| VC-02 | ✅ Testável | Três elementos objetivos, presença/ausência binária. |
| VC-03 | ✅ Testável | Pergunta concreta, resultado binário (duas versões vs. valor único). |
| VC-04 | ✅ Testável | Caso concreto e binário. |
| VC-05 | ✅ Testável | Critério objetivo. |
| VC-06 | ✅ Testável | Caso concreto e binário. |
| VC-07 | 🟡 Parcial | "sem diferença **perceptível** de tempo de resposta" não define o limiar de latência que caracterizaria vazamento por canal lateral — precisa de um número (ex.: "diferença abaixo de X ms medida em Y execuções"), senão dois QAs podem divergir sobre o que é "perceptível". |
| VC-08 | ✅ Testável | 100% da amostra, campos objetivos. |
| VC-09 | ✅ Testável | Critério objetivo; ganharia precisão referenciando um dicionário fechado de entidades da NovaTech (mesma recomendação já feita na revisão de QA da spec de RAG), mas já é testável como está. |
| VC-10 | 🟡 Parcial | "a 5ª pergunta é avaliada com a **mesma precisão**" não define como "precisão" é medida para permitir a comparação — precisa amarrar a uma métrica operacional (ex.: taxa de acerto nos critérios de VC-02/03/04/05/06 aplicados à posição 1 vs. posição 5 da sessão). |

**Resumo:** 7 de 10 VCs existentes são plenamente testáveis; 3 (VC-01, VC-07, VC-10) têm a intenção certa mas faltam um número/definição operacional — o mesmo padrão de problema que a revisão de QA da spec de RAG já tinha identificado em outros documentos do projeto (não é um erro novo, é uma recorrência).

---

## 4. A lacuna de "ingestão" — o que exatamente falta e o que continua fora de escopo

Sua observação está correta, mas vale precisar exatamente **onde** ela mora, porque existem dois testes diferentes que poderiam ser chamados de "VC de ingestão", e só um deles pertence a este documento:

**Não pertence a este `requirements.md` (correto continuar fora):** testar se o `pipeline-ingestao` extrai, converte e indexa um documento corretamente, ou se ele cumpre o prazo de 24h úteis como processo. Isso já está — corretamente — atribuído a outro módulo, com seu próprio `requirements.md` e seu próprio verification criteria (a especificação de RAG v2.0 já classifica isso como "teste de infraestrutura/operação", REQ-13/REQ-14).

**Pertence a este `requirements.md` (e está faltando):** testar se o query-endpoint **respeita corretamente**, no momento de responder, o metadado que a ingestão produziu. São dois testes distintos, cobrindo duas pontas diferentes do mesmo problema:

1. **Aplicação do ciclo de vida de vigência.** O escopo do documento já diz, na Seção 2, que este módulo aplica "o ciclo de vida de validade de um documento" (herdado do BC-2) — mas nenhum VC verifica isso. Um documento com status `revogado` nunca deveria fundamentar uma resposta; um documento `sujeito a revisão` deveria sempre limitar a confiança a, no máximo, Média. Hoje, se o pipeline de ingestão produzir esse metadado perfeitamente, não há nada nesta spec que teste se o query-endpoint de fato o usa.
2. **Reflexo de atualização dentro do prazo.** A Constraint C3 diz que este módulo "nunca deve apresentar uma fonte desatualizada como se fosse a mais recente disponível" — mas, de novo, nenhum VC verifica isso. É o teste de integração natural na fronteira entre os dois módulos: publicar/atualizar uma fonte, esperar o prazo de disponibilização, perguntar, e confirmar que a resposta reflete a atualização.

Essas duas lacunas foram corrigidas com os VC-11 e VC-12 abaixo. Também aproveitei a matriz de rastreabilidade para fechar as lacunas de C2 e C6 (VC-13 e VC-14), já que a revisão expôs as duas ao mesmo tempo.

---

## 5. Verification Criteria adicionados

**VC-11 — Ciclo de vida de vigência respeitado na resposta.** Numa amostra de teste com documentos em cada um dos quatro estados (`vigente`, `revogado`, `vigência não confirmada`, `sujeito a revisão`): nenhuma resposta usa como fundamento um documento `revogado`; toda resposta apoiada, total ou parcialmente, em documento `sujeito a revisão` tem nível de confiança no máximo Média; nenhum documento com `vigência não confirmada` sustenta uma resposta de confiança Alta. Qualquer violação reprova o caso — este é o critério que garante que o metadado produzido pela ingestão é efetivamente respeitado por este módulo, não apenas armazenado sem uso.

**VC-12 — Reflexo de atualização de fonte dentro do prazo de ingestão.** Após uma fonte ser publicada ou atualizada e o prazo de disponibilização (24h úteis, responsabilidade do `pipeline-ingestao`) ter decorrido, uma pergunta sobre aquele conteúdo reflete a versão atualizada e a vigência corrigida — nunca apresenta a versão anterior como a mais recente disponível (C3). Este caso de teste tem uma pré-condição fora do controle deste módulo: se a ingestão atrasar, o teste é **inconclusivo para o query-endpoint**, não reprovado — a reprovação só se aplica se, com a ingestão já concluída, a resposta ainda assim ignorar a atualização.

**VC-13 — Idioma e tom.** 100% das respostas da amostra de teste estão em português formal (sem gírias, sem primeira pessoa informal); qualquer resposta em outro idioma ou tom incompatível reprova o caso (C2).

**VC-14 — Exclusividade das categorias de resposta.** Toda resposta da amostra de teste se classifica em exatamente uma das quatro categorias definidas em C6 (cita fonte / declara conflito / rotula genérica / admite não encontrou); uma resposta que não se encaixa em nenhuma delas, ou que mistura sinais de duas ao mesmo tempo sem prioridade clara, reprova o caso.

---

## 6. Ajustes de testabilidade nos VCs existentes

- **VC-01:** passa a exigir um limiar próprio do módulo, marcado como **[a validar com o Tech Lead]** em vez de herdar apenas o total de 30s sem número — consistente com a forma como outros pontos em aberto já são marcados neste projeto.
- **VC-07:** passa a exigir um limiar de latência explícito para a comparação de tempo de resposta, também **[a validar]**.
- **VC-10:** passa a amarrar "precisão" à taxa de acerto dos demais VCs (02/03/04/05/06) comparada entre a 1ª e a 5ª pergunta da sessão, em vez de um conceito solto de "mesma precisão".

---

## 7. Veredito final

Antes desta revisão: 10 VCs, 7 plenamente testáveis, 3 parciais, e **duas lacunas reais de cobertura** (C2 e a dupla ligada a C3/ingestão), mais uma lacuna parcial (C6). Sua observação identificou corretamente a ausência mais importante — não porque a ingestão devesse ser testada aqui (ela não deve), mas porque **o consumo correto do que a ingestão produz** é, sim, responsabilidade deste módulo e não tinha nenhum critério de verificação. Após os ajustes, a Seção 5 passa a ter 14 VCs, todos rastreáveis a um Outcome ou Constraint específico, e os 3 que dependiam de números ainda não definidos estão marcados como tal — não escondidos atrás de linguagem qualitativa.
