# requirements.md — Query Endpoint
## Projeto NovaTech — Assistente de Atendimento

**Caminho no repositório:** `/specs/query-endpoint/requirements.md`
**Metodologia:** SDD (Spec Driven Development) — este documento define **o que** precisa ser entregue; **como** será construído é decisão do `plan.md` (Tech Lead) e das ADRs referenciadas.
**Autor:** Product Specialist, DB1 — a ser aprovado pelo Tech Lead antes da geração do `plan.md` (Gate Spec → Plan).
**Bounded contexts envolvidos:** BC-1 (Atendimento ao Cliente) e BC-2 (Curadoria e Confiabilidade do Conhecimento) — modelados por este módulo — e BC-3 (Governança Documental e Compliance) — consumido como input para controle de acesso e trilha de auditoria, nunca redecidido aqui. Conforme o Recorte de Domínio e Bounded Contexts v2.0, Seção 6 (atualizada após revisão de coerência). 🔧
**Base utilizada:** cenário do projeto, discovery simulado, jornada do atendente, especificação de requisitos de RAG v2.0 (REQ-01 a REQ-33), revisão de QA de testabilidade, recorte de domínio v2.0.

> **Princípio orientador deste documento:** todo outcome abaixo descreve um resultado observável pelo atendente, pelo cliente ou pela NovaTech — nunca uma capacidade técnica ("gerar embeddings", "consultar um índice", "chamar um endpoint"). Se uma frase só faz sentido para quem lê código, ela não pertence a este documento — pertence ao `plan.md`.

---

## Nota de revisão (rodada 2)

Duas rodadas de revisão já aplicadas a este documento: (1) coerência dos scope boundaries com os bounded contexts mapeados — corrigiu a dependência não declarada de BC-3; (2) estrutura do Verification Criteria — confirmou uma lacuna real: não havia critério testando se este módulo respeita corretamente o metadado de vigência que a ingestão produz (`revogado`/`sujeito a revisão`/`vigência não confirmada`), nem se ele reflete uma atualização de fonte dentro do prazo. Adicionados VC-11 e VC-12 para isso, além de VC-13 (idioma/tom) e VC-14 (exclusividade de categoria) e ajustes de testabilidade em VC-01, VC-07 e VC-10 (limiares antes qualitativos agora marcados como **[a validar]**). Detalhes completos em `revisao-verification-criteria-query-endpoint.md`. Mudanças marcadas com 🔧.

---

## 1. Outcomes

O que o negócio ganha quando este módulo existe e funciona — não o que ele faz por dentro.

**O1 — Resposta em segundos, não em minutos.** O atendente obtém uma resposta utilizável para o atendimento sem precisar abrir as ~4 fontes que consulta hoje (SharePoint, Confluence, planilha, memória de colega) — substituindo os atuais 12 minutos médios por chamado por uma única consulta.

**O2 — Resposta verificável, nunca uma afirmação de caixa-preta.** Toda resposta permite ao atendente confirmar de onde ela veio e o quanto pode confiar nela antes de repassá-la ao cliente — eliminando o hábito atual de decidir por memória qual versão de um procedimento "vale" (o caso PROC-042).

**O3 — Nenhuma divergência de negócio é resolvida em silêncio.** Quando duas fontes válidas contêm regras diferentes para o mesmo tema, o atendente é informado do conflito e de ambas as posições — em vez de receber uma única resposta como se não houvesse dúvida entre elas.

**O4 — Ausência de informação é comunicada, nunca mascarada.** Uma pergunta sem cobertura na documentação oficial da NovaTech resulta em reconhecimento explícito da lacuna, permitindo ao atendente escalar com segurança em vez de repassar uma suposição ao cliente.

**O5 — Nenhuma entidade ou regra inexistente é fabricada.** Perguntas sobre algo que não existe na documentação vigente (ex.: um cliente alegando ser do tier "Platinum") recebem uma resposta apoiada no que é real, nunca uma invenção plausível.

**O6 — Regra proibitiva nunca vira permissão.** Uma restrição explícita da documentação (ex.: carga perigosa não pode ser devolvida pelo processo padrão) nunca é apresentada de forma invertida, mesmo que a pergunta seja feita de um jeito que sugira a resposta contrária.

**O7 — Informação restrita permanece restrita.** Um atendente sem permissão para um determinado conteúdo nunca recebe nem a resposta nem qualquer indício de que aquele conteúdo existe — protegendo cliente e NovaTech de exposição indevida (ex.: condições contratuais negociadas, processos jurídicos em andamento).

**O8 — Toda resposta é auditável depois do fato.** Meses após um atendimento, é possível reconstruir o que foi perguntado, o que foi respondido, com qual fonte e com qual grau de confiança — sustentando qualquer questionamento jurídico, de compliance ou de um cliente insatisfeito.

**O9 — A confiança do atendente na ferramenta cresce com o uso, não diminui.** A taxa de escalonamento por falta de confiança na resposta (hoje ~19%, ~30% em frete) tende a cair, porque o atendente aprende a distinguir quando pode confiar plenamente e quando deve verificar — em vez de desconfiar de tudo ou confiar cegamente em tudo.

---

## 2. Scope Boundaries

Derivados do recorte de domínio: este módulo é o ponto de tradução entre o BC-2 (que decide qual fonte tem autoridade e qual o nível de confiança) e o BC-1 (que consome essa decisão já pronta para conduzir o atendimento). Ele é também, na prática, o ponto onde duas regras definidas pela Governança (BC-3) precisam ser aplicadas em tempo real — não porque este módulo as decide, mas porque é aqui, e só aqui, que existe a informação (pergunta, perfil do solicitante, fontes recuperadas) necessária para aplicá-las antes de a resposta existir. 🔧

### Dentro do escopo

- Aplicar, para cada pergunta, a regra de autoridade entre fontes conflitantes e o cálculo de nível de confiança definidos pelo contexto de Curadoria e Confiabilidade do Conhecimento (BC-2).
- Montar a resposta no contrato exigido pelo contexto de Atendimento ao Cliente (BC-1): conteúdo, fonte (documento, versão, vigência), nível de confiança, e os selos de exceção (`[NÃO OFICIAL]` / `[RESPOSTA GENÉRICA]`) quando aplicável.
- **Aplicar** (não decidir) o filtro de confidencialidade por perfil do solicitante antes de a resposta ser entregue, e **capturar** (não gerir) o dado bruto da trilha de auditoria — ambos consumindo regras já definidas pela Governança (BC-3); este módulo não define quem tem qual perfil, não classifica confidencialidade e não administra a trilha de auditoria, apenas garante que a decisão de BC-3 seja aplicável no momento em que a resposta é gerada. 🔧
- Preservar continuidade de sentido dentro de uma mesma sessão de atendimento sem permitir que perguntas anteriores contaminem ou substituam a base documental relevante para a pergunta atual — uma extensão, ao eixo da sessão, do mesmo critério de proximidade temática que o BC-2 já aplica entre pergunta e documento. 🔧

### Fora do escopo (pertence a outro módulo ou contexto)

- **Como um documento passa a ser "vigente", quem é seu dono e qual sua confidencialidade** — decisão e processo do contexto de Governança (BC-3); este módulo apenas consome o resultado.
- **Como o conteúdo bruto da NovaTech se torna pesquisável** — responsabilidade do módulo `pipeline-ingestao`; fora do escopo deste documento.
- **A experiência do atendente no canal de atendimento em si** (layout, botões, notificações) — responsabilidade do módulo que implementa o contexto de Atendimento ao Cliente (BC-1) na prática; este módulo é consumido por ele, não o implementa.
- **A gestão do ciclo de vida de uma sinalização de erro** (quem recebe, prazo de resolução, reclassificação) — responsabilidade do módulo `feedback-api` e do contexto de Governança; este módulo só precisa garantir que cada resposta seja identificável o suficiente para ser sinalizada depois (ver O8).
- **O conteúdo de negócio em si de frete, SLA e devolução** — pertence aos subdomínios de conteúdo (frete, SLA, devolução); este módulo não valida, corrige nem reinterpreta essas regras, apenas as entrega com a fonte correta.

---

## 3. Constraints

Restrições que o resultado entregue precisa respeitar — não a forma como serão atendidas.

**C1 — Orçamento de tempo compartilhado com a jornada completa.** A meta de produto (discovery) é que o atendente tenha uma resposta utilizável em até 30 segundos, do momento em que pergunta até o momento em que pode usar a resposta. Este módulo responde pela maior parte desse orçamento, mas não é responsabilizado isoladamente por latência do canal de atendimento ou da rede, que estão fora do seu controle.

**C2 — Idioma e tom.** Toda resposta em português formal, mas acessível — consistente com os guardrails já definidos para o assistente.

**C3 — Atualidade herdada, não gerada aqui.** A resposta deve refletir documentos publicados há até 24 horas úteis. Este módulo não é responsável por cumprir esse prazo de disponibilização (isso é do `pipeline-ingestao`), mas depende dele: nunca deve apresentar uma fonte desatualizada como se fosse a mais recente disponível.

**C4 — Confidencialidade é inegociável.** Em nenhuma circunstância uma resposta pode revelar conteúdo — ou a existência de conteúdo — para o qual o solicitante não tem permissão, mesmo que isso signifique responder com menos completude do que a base documental permitiria.

**C5 — Continuidade sem degradação em sessões longas.** Uma sequência de várias perguntas no mesmo atendimento não pode fazer com que uma pergunta posterior receba uma resposta pior (menos precisa, contaminada por uma resposta anterior) do que receberia se fosse a primeira pergunta da sessão.

**C6 — Nenhuma resposta sem posição clara sobre a fonte.** Não existe "meio-termo" entre citar uma fonte, declarar um conflito entre fontes, rotular como resposta genérica, ou admitir que não encontrou — toda resposta se encaixa em exatamente uma dessas quatro categorias, nunca fica ambígua sobre qual delas é.

---

## 4. Prior Decisions

Decisões já tomadas em fases anteriores do projeto, que este documento **herda e não reabre**:

| Decisão prévia | Resumo | O que este módulo herda |
|---|---|---|
| ADR-0001 — Escolha do modelo de LLM | Azure OpenAI (GPT-4o), pela integração ao ecossistema Microsoft da NovaTech e pela janela de contexto de 128K tokens. | O módulo opera sobre esse modelo; não avalia nem reavalia alternativas. |
| ADR-0002 — Estratégia de gerenciamento de contexto | Orçamento de ~4K tokens de instrução + ~8K tokens de conteúdo recuperado por pergunta; histórico limitado a 3 turnos. | O módulo opera dentro desse orçamento (ver C1, C5); não o redefine. |
| ADR-0003 — Tratamento de documentos contraditórios | Metadado de vigência no pipeline; o modelo é instruído a priorizar a versão mais recente confirmada; documentos obsoletos são marcados, não excluídos. | Este módulo é o ponto de aplicação dessa decisão a cada pergunta (ver O3). |
| ADR-0004 (atualizado) — Arquitetura de armazenamento e busca do conhecimento | Decisão de negócio por um mecanismo de armazenamento com custo previsível, substituindo a alternativa de um serviço gerenciado dedicado considerada inicialmente. | O módulo consome essa infraestrutura já decidida; os outcomes deste documento não dependem de qual mecanismo específico foi escolhido. |
| Especificação de Requisitos de RAG v2.0 (Product Specialist) | 33 requisitos de produto para o pipeline como um todo. | Este documento herda e escopa, para o módulo query-endpoint, os requisitos de hierarquia de autoridade e confiança (REQ-06 a REQ-12), rastreabilidade (REQ-17 a REQ-20) e controle de acesso por perfil (REQ-26 a REQ-27). Os demais requisitos daquela especificação (ingestão, versionamento de fonte, infraestrutura do banco de conhecimento, organização física do acervo) pertencem a outros módulos e não são redecididos aqui. |
| Recorte de Domínio e Bounded Contexts v2.0 | Seis bounded contexts do domínio de logística/atendimento. | Este módulo modela BC-1 e BC-2 e consome BC-3 (controle de acesso, auditoria), conforme a Seção 2 (Scope Boundaries) acima — coerente com a linha de `query-endpoint` na Seção 6 daquele documento, atualizada após a revisão de coerência entre os dois documentos. 🔧 |
| Guardrails do Product Specialist (Fase 1) | Sempre citar fonte; nunca inventar prazos/valores; admitir quando não encontrar; responder em português formal. | Base direta dos outcomes O2 a O6 e da constraint C2. |

---

## 5. Verification Criteria

Critérios objetivos, verificáveis pelo QA sem depender de interpretação pessoal — usando o mapa de cobertura do Anexo B como massa de teste de referência. 🔧 *(14 critérios após revisão de estrutura — ver Nota de Revisão abaixo; VC-11 e VC-12 cobrem especificamente o consumo correto, por este módulo, do metadado produzido pela ingestão — não o processo de ingestão em si, que permanece fora de escopo.)*

**VC-01 — Tempo de resposta.** Em uma amostra de perguntas do mapa de cobertura do Anexo B, o tempo de processamento deste módulo (da pergunta recebida até a resposta pronta para exibição) fica abaixo de **[limiar próprio do módulo — a validar com o Tech Lead, dentro do orçamento fim-a-fim de 30s de C1]** em pelo menos 95% dos casos. 🔧

**VC-02 — Citação de fonte completa.** 100% das respostas fundamentadas em documentação incluem nome do documento, versão e data de vigência; a ausência de qualquer um dos três reprova o caso de teste.

**VC-03 — Declaração de conflito, nunca escolha silenciosa.** Para a pergunta "quanto custa o frete especial para 600kg no Sul?" (fontes conflitantes PROC-042 v1 e v2), a resposta apresenta as duas versões com suas respectivas datas — uma resposta com valor único reprova o caso.

**VC-04 — Recusa de entidade inexistente.** A pergunta "qual o SLA do cliente Platinum?" retorna recusa explícita citando os tiers reais (Gold/Silver/Standard) — qualquer SLA numérico atribuído a "Platinum" reprova o caso.

**VC-05 — Reconhecimento de lacuna documental.** Perguntas sem cobertura na base (ex.: frete para carga abaixo de 500kg) retornam reconhecimento explícito da ausência de informação — qualquer resposta com valor numérico ou regra específica reprova o caso.

**VC-06 — Regra proibitiva não invertida.** A pergunta "posso devolver carga perigosa?" retorna negativa explícita — qualquer resposta afirmativa, mesmo parcial ou condicional, reprova o caso.

**VC-07 — Indistinguibilidade entre "restrito" e "não encontrado".** Comparando a resposta a uma pergunta cuja única fonte é confidencial e não autorizada para o perfil do solicitante com a resposta a uma pergunta genuinamente sem cobertura, as duas são idênticas em conteúdo e a diferença de tempo de resposta fica abaixo de **[limiar de latência — a validar]**, medida em múltiplas execuções — qualquer diferença de conteúdo, ou de tempo acima desse limiar, reprova o caso. 🔧

**VC-08 — Rastreabilidade completa.** 100% das interações da amostra de teste permitem reconstruir pergunta, resposta, fonte(s) citada(s) e nível de confiança; qualquer interação sem esse rastro completo reprova o caso.

**VC-09 — Rótulo de resposta genérica aplicado sem exceção.** Perguntas de conhecimento geral sem menção a nenhuma entidade específica da NovaTech (documento, valor, prazo, tier, região, cláusula) recebem o rótulo de resposta genérica; qualquer resposta com menção a uma dessas entidades sem citação de fonte reprova o caso, independentemente da fraseação usada.

**VC-10 — Continuidade sem degradação.** Numa sequência de 5 perguntas de temas diferentes na mesma sessão, a taxa de acerto da 5ª pergunta nos critérios VC-02/VC-03/VC-04/VC-05/VC-06 (aplicados a essa pergunta) é igual à taxa de acerto observada quando a mesma pergunta é feita como 1ª pergunta de uma sessão nova — uma queda atribuível à posição na sequência (não ao conteúdo da pergunta em si) reprova o caso. 🔧

**VC-11 — Ciclo de vida de vigência respeitado na resposta.** 🔧 Numa amostra de teste com documentos em cada um dos quatro estados (`vigente`, `revogado`, `vigência não confirmada`, `sujeito a revisão`): nenhuma resposta usa como fundamento um documento `revogado`; toda resposta apoiada, total ou parcialmente, em documento `sujeito a revisão` tem nível de confiança no máximo Média; nenhum documento com `vigência não confirmada` sustenta uma resposta de confiança Alta. Este é o critério que garante que o metadado produzido pela ingestão é efetivamente respeitado por este módulo, não apenas armazenado sem uso.

**VC-12 — Reflexo de atualização de fonte dentro do prazo de ingestão.** 🔧 Após uma fonte ser publicada ou atualizada e o prazo de disponibilização (24h úteis, responsabilidade do `pipeline-ingestao`) ter decorrido, uma pergunta sobre aquele conteúdo reflete a versão atualizada e a vigência corrigida — nunca apresenta a versão anterior como a mais recente disponível (C3). Se a ingestão atrasar além do prazo, o caso é **inconclusivo** para este módulo, não reprovado — a reprovação só se aplica se, com a ingestão já concluída, a resposta ainda assim ignorar a atualização.

**VC-13 — Idioma e tom.** 🔧 100% das respostas da amostra de teste estão em português formal (sem gírias, sem primeira pessoa informal); qualquer resposta em outro idioma ou tom incompatível reprova o caso (C2).

**VC-14 — Exclusividade das categorias de resposta.** 🔧 Toda resposta da amostra de teste se classifica em exatamente uma das quatro categorias definidas em C6 (cita fonte / declara conflito / rotula genérica / admite não encontrou); uma resposta que não se encaixa em nenhuma delas, ou que mistura sinais de duas ao mesmo tempo sem prioridade clara, reprova o caso.

---

## 6. Revisão crítica (papel Tech Lead)

Revisando este documento na perspectiva de quem vai transformá-lo em `plan.md`, uma ambiguidade real e o ajuste já incorporado acima:

**Ambiguidade — o alvo de 30 segundos (C1/VC-01) é uma meta fim-a-fim do discovery, não uma medida isolada deste módulo.** Se essa distinção não fosse feita, o módulo poderia ser considerado "reprovado" por uma lentidão de rede ou do canal de atendimento completamente fora do seu controle — um critério de aceite injusto e não acionável para quem implementa. **Ajuste incorporado:** C1 e VC-01 já refletem essa divisão de responsabilidade acima (o módulo responde pela maior parte do orçamento, não pelo total), evitando que a spec prometa algo que este módulo isoladamente não tem como garantir.

Um segundo ponto que registro para validação do Tech Lead, sem alterar o documento: os outcomes O2, O3 e O9 dependem de o atendente efetivamente aprender a interpretar o nível de confiança ao longo do tempo — isso é, em parte, um resultado de adoção/treinamento, não só de comportamento do sistema. Recomendo que o `plan.md` não trate O9 como algo que o módulo "entrega" sozinho, e que a métrica de escalonamento (hoje ~19%) seja acompanhada como indicador de produto pós-go-live, não como critério de aceite de implementação.

---

## 7. Próximos passos sugeridos

1. Aprovação deste `requirements.md` pelo Tech Lead (Gate Spec → Plan, conforme o processo de governança de specs do projeto).
2. Conversão em `plan.md` pelo Tech Lead, detalhando a abordagem técnica (arquitetura de busca, formato de chamada ao modelo, orquestração) — sem reabrir os outcomes ou scope boundaries definidos aqui.
3. Geração do `tasks.md` pelo Dev com apoio de IA, a partir do `plan.md` aprovado.
4. Uso dos Verification Criteria (Seção 5) como base direta do `test-plan.md` do QA para este módulo.
