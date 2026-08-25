# Revisão de Coerência — Scope Boundaries do `requirements.md` (Query Endpoint) vs. Recorte de Domínio
## Projeto NovaTech

**Objeto revisado:** `requirements-query-endpoint-novatech.md` (Seções 1 a 5)
**Referência (fonte da verdade):** `recorte-dominio-bounded-contexts-novatech.md` v2.0
**Pergunta que orienta esta revisão:** todo item de escopo (dentro e fora) do requirements.md deriva de um bounded context realmente mapeado para este módulo — sem inventar responsabilidade nem esconder uma dependência real?

---

## 1. Metodologia

Cada item das seções "Dentro do escopo" e "Fora do escopo" do requirements.md foi confrontado contra três fontes do recorte de domínio v2.0:

1. As listas **"Está dentro" / "Está fora"** de BC-1 e BC-2 (Seção 2 do recorte).
2. A linha do módulo `query-endpoint` na **tabela de mapeamento SDD** (Seção 6 do recorte).
3. Os **Shared Kernels e conceitos compartilhados** (Seções 3 e 4 do recorte), que atravessam contextos e podem gerar dependência não declarada.

Um item é considerado **coerente** quando aparece, com o mesmo sentido, em pelo menos uma dessas três fontes. É considerado **inconsistente** quando o requirements.md assume uma responsabilidade ou dependência que o recorte atribui a um contexto não declarado — ou quando o recorte e o requirements.md se contradizem sobre quem é dono de quê.

---

## 2. Tabela de verificação — "Dentro do escopo"

| Item do requirements.md | Fonte no recorte de domínio | Veredito |
|---|---|---|
| Aplicar a regra de autoridade entre fontes e o cálculo de confiança | BC-2, "está dentro": "regra de precedência entre fontes"; "regra objetiva de cálculo do nível de confiança" | ✅ Coerente |
| Montar a resposta no contrato do BC-1 (fonte, versão, vigência, confiança, selos) | BC-1, "está dentro": "apresentação da resposta com fonte, versão, confiança... e selos" | ✅ Coerente |
| Aplicar o filtro de confidencialidade por perfil antes da entrega | **Não aparece em BC-1 nem BC-2.** Pertence a BC-3, "está dentro": "regra de controle de acesso por papel organizacional... e o comportamento de não revelar existência de conteúdo restrito" | ⚠️ **Inconsistente — ver Achado 1** |
| Preservar continuidade de sentido entre perguntas da mesma sessão | Não é um bullet explícito de nenhum BC. Tematicamente próximo do "critério de proximidade temática" do BC-2, mas não escrito para cobrir o eixo temporal/sessão, só o eixo documental. | ⚠️ **Gap de rastreabilidade — ver Achado 2** |

## 3. Tabela de verificação — "Fora do escopo"

| Item do requirements.md | Fonte no recorte de domínio | Veredito |
|---|---|---|
| Como um documento vira "vigente", dono e confidencialidade | BC-2, "está fora": "quem é dono de cada fonte, SLA de revisão, classificação de confidencialidade — decididos no BC-3" | ✅ Coerente |
| Como o conteúdo bruto se torna pesquisável | BC-2, "está fora": tecnologia de armazenamento/busca explicitamente excluída; Seção 6 atribui a `pipeline-ingestao` | ✅ Coerente |
| Experiência do atendente no canal (layout, notificações) | BC-1, "está fora": "qualquer tecnologia de canal, mensageria ou integração" | ✅ Coerente |
| Gestão do ciclo de vida da sinalização de erro | BC-1, "está dentro": "a gestão da sinalização em si é do BC-3"; Seção 6 atribui a `feedback-api` | ✅ Coerente |
| Conteúdo de negócio de frete/SLA/devolução | BC-2, "está fora": "conteúdo semântico das regras de frete/SLA/devolução"; relação *Conformist* com BC-4/5/6 | ✅ Coerente |

A lista de exclusões está bem derivada — todos os cinco itens de "fora do escopo" têm correspondência direta e correta no recorte. O problema está concentrado na lista de inclusões.

---

## 4. Achados

### Achado 1 (severidade alta) — dependência de BC-3 não declarada

O requirements.md inclui, dentro do escopo, "aplicar o filtro de confidencialidade por perfil" (item 3) e sustenta dois outcomes inteiros sobre esse comportamento:

- **O7** ("Informação restrita permanece restrita") reproduz, quase literalmente, o bullet de BC-3: *"comportamento de não revelar existência de conteúdo restrito"*.
- **O8** ("Toda resposta é auditável depois do fato") reproduz o bullet de BC-3: *"trilha de auditoria completa (pergunta → resposta → fonte → confiança → sinalização → resolução)"*.
- A tabela de **Prior Decisions** confirma isso ao citar explicitamente REQ-26/REQ-27 (controle de acesso) e REQ-17 a REQ-20 (rastreabilidade) — requisitos que, no recorte de domínio, pertencem à Governança.

Ou seja: **o requirements.md já reconhece implicitamente uma dependência de BC-3** (via outcomes e via Prior Decisions), mas:

1. O cabeçalho declara apenas *"Bounded contexts envolvidos: BC-1 e BC-2"* — omitindo BC-3.
2. A linha de `query-endpoint` na **Seção 6 do próprio recorte de domínio** também lista apenas *"BC-2 + BC-1"* — o recorte não previu, na sua própria tabela de mapeamento, que este módulo seria o ponto de aplicação em tempo real de duas regras de BC-3 (controle de acesso e captura do dado de auditoria).

Evidência de que isso não deveria ter sido surpresa: a própria **Seção 4 do recorte** (Shared Kernels) já registra que o conceito "fonte documental (documento, versão, vigência)", definido em BC-3, é **referenciado tanto por BC-2 quanto por BC-1** — ou seja, o recorte já sabia que BC-1 e BC-2 juntos tocam conteúdo de BC-3; só não propagou essa informação para a tabela de módulos da Seção 6.

**Padrão correto, já usado em outra linha da mesma tabela:** a linha de `pipeline-ingestao` já distingue "implementa o modelo de negócio" de "consome como input" — `BC-2 (implementa) + BC-3 (consome classificação/vigência como input)`. A linha de `query-endpoint` deveria seguir o mesmo padrão, e não foi feito na v2.0 do recorte.

**Por que isso importa na prática:** sem essa declaração explícita, um Tech Lead lendo o `requirements.md` isoladamente poderia escrever um `plan.md` que trata controle de acesso e trilha de auditoria como responsabilidade *exclusiva* de outro serviço, sem garantir que o query-endpoint carrega os dados necessários (perfil do solicitante, classificação do conteúdo recuperado) para que esse controle seja sequer possível "antes da geração da resposta" — que é exatamente o texto do REQ-26 que o próprio requirements.md cita.

### Achado 2 (severidade baixa) — continuidade de sessão sem âncora explícita no recorte

O item "preservar continuidade de sentido... sem permitir que perguntas anteriores contaminem..." (Constraint C5 / VC-10) deriva de uma decisão prévia real (ADR-0002, gerenciamento de context rot em conversas longas), mas **nenhum bounded context do recorte tem um bullet que cubra explicitamente o eixo temporal/sessão** — o BC-2 fala em "proximidade temática" entre pergunta e conteúdo (eixo documento↔tema), não entre pergunta atual e histórico da sessão (eixo tempo↔sessão). Não é uma contradição — é uma lacuna de detalhamento no recorte, que o requirements.md preencheu de forma razoável, mas sem uma frase à qual se ancorar literalmente.

### Não-achado (verificado e correto)

O restante do mapeamento — toda a lista de "fora do escopo", a atribuição de O1–O6 e O9 a BC-1/BC-2, e a moldura geral "query-endpoint é o ponto de tradução entre BC-2 e BC-1" — está coerente com o recorte e não precisa de ajuste.

---

## 5. Correções aplicadas

Para eliminar o Achado 1, os dois documentos foram atualizados juntos (a inconsistência vivia nos dois, não em um só):

1. **Recorte de domínio, Seção 6** — a linha de `query-endpoint` passou a incluir `BC-3`, no mesmo padrão já usado para `pipeline-ingestao` (contexto consumido como input, não modelado por este módulo).
2. **`requirements.md`, cabeçalho e Seção 2** — "Bounded contexts envolvidos" passou a citar a dependência de BC-3, e o item de escopo sobre confidencialidade foi reescrito para deixar explícito que o módulo é o *ponto de aplicação* de uma regra de BC-3, não o dono dela.

O Achado 2 foi tratado de forma mais leve: em vez de alterar a definição de BC-2 no recorte (mudança estrutural maior, fora do pedido desta revisão), o item de escopo correspondente no `requirements.md` foi reescrito para ancorar explicitamente a continuidade de sessão ao critério de proximidade temática já existente em BC-2, deixando registrado que é uma extensão desse critério ao eixo da sessão, não um conceito novo e solto.

Ambos os arquivos foram reenviados após o ajuste.

---

## 6. Veredito final

De 9 itens de escopo (4 dentro + 5 fora), **8 derivavam corretamente** dos bounded contexts mapeados. O único erro real (Achado 1) não era um erro de invenção — era uma dependência real e necessária que faltou declarar, presente nos dois documentos e agora corrigida nos dois. O recorte de domínio e o requirements.md estão, após esta revisão, mutuamente consistentes quanto às responsabilidades do módulo `query-endpoint`.
