# Proposta de Jornada Melhorada do Atendente — Projeto NovaTech

**Base:** achados das Etapas 1-3 (conflito PROC-042, erro do item 45 do FAQ, gap do processo de sinistro) e da simulação de discovery com stakeholders (`discovery-simulado-stakeholders-novatech.md`).
**Objetivo:** redesenhar o caminho que o atendente percorre entre a dúvida do cliente e a resposta, substituindo a busca manual em 4 fontes (12 min/chamado, 19% de escalonamento, nota 6,1) por um fluxo único, com fonte rastreável, rota de exceção clara e um canal formal para corrigir a documentação na origem.

---

## 1. Princípios de design

Antes dos fluxos, três decisões que vêm direto do discovery e moldam tudo abaixo:

Primeiro, toda resposta carrega sua fonte e seu nível de confiança — nunca uma resposta "seca". Segundo, quando duas fontes conflitam (o cenário PROC-042), o assistente declara o conflito em vez de escolher uma versão silenciosamente. Terceiro, o atendente tem, dentro do próprio fluxo de atendimento, um jeito de sinalizar problema — sem depender de mensagem avulsa no grupo do Teams, como acontece hoje.

---

## 2. Fluxo principal

1. **Cliente traz a dúvida** (prazo, frete, devolução, reclamação, sinistro etc.) durante o atendimento.
2. **Atendente consulta o assistente** em linguagem natural, direto do Teams, sem sair da conversa com o cliente.
3. **Assistente busca na base indexada**, respeitando a hierarquia de autoridade documental: política/procedimento oficial vigente → FAQ validado → FAQ não validado (sinalizado como tal).
4. **Assistente responde com três elementos sempre juntos:**
   - o conteúdo da resposta;
   - a fonte exata (documento, versão e data de vigência — ex.: "PROC-042-v2, vigente desde 01/12/2023");
   - o nível de confiança (Alta / Média / Baixa — critério na Seção 3).
5. **Atendente confere a fonte** (um clique expande o trecho original do documento, sem precisar abrir o SharePoint) e usa a resposta no atendimento.
6. **Chamado é encerrado** com o registro automático de qual resposta e qual fonte foram usadas — isso cria o rastro de auditoria que hoje não existe e que o Jurídico e o Compliance apontaram como necessário.

Resultado esperado: o tempo de busca deixa de ser de 12 minutos em 4 fontes e passa a ser de uma consulta única, com a decisão de confiar ou não na resposta apoiada em critério explícito, não em memória do atendente (como hoje acontece com "decorei que a v2 é a que vale").

---

## 3. Fluxo de fallback — baixa confiança ou discordância do atendente

O fallback cobre dois gatilhos distintos, porque a natureza do problema é diferente em cada um:

### 3.1 Quando o assistente não tem confiança suficiente

Isso acontece em três situações mapeadas no discovery: (a) fontes conflitantes sem vigência clara — o caso PROC-042; (b) pergunta cai em área sem documento oficial, só prática informal — o caso do processo de sinistro; (c) pergunta toca em conteúdo não certificado, como o seguro de carga que só existe no FAQ.

Nesses casos, o assistente **não entrega uma resposta única como se fosse certa**. Em vez disso:

- Se houver conflito entre fontes, apresenta as duas versões lado a lado, com suas respectivas datas e a inconsistência explícita ("PROC-042 e PROC-042-v2 têm valores diferentes para este cálculo; a v2 se declara vigente desde 01/12/2023, mas isso não está confirmado como política formal atual").
- Se não houver base documentada, diz isso claramente ("não encontrei um documento oficial que cubra este ponto") em vez de tentar compor uma resposta plausível.
- Em ambos os casos, a interação é **roteada automaticamente para o supervisor ou para o dono do documento** (conforme o comitê de governança do discovery: Comercial, Operações, Compliance, Jurídico, TI, Atendimento), já com a pergunta do cliente e as fontes candidatas anexadas — eliminando o retrabalho de hoje, em que o supervisor recebe só a dúvida, sem contexto, e às vezes também não sabe responder.

### 3.2 Quando o atendente discorda da resposta

O atendente pode ter conhecimento tácito que a documentação ainda não reflete (o processo de sinistro é o exemplo concreto: existe na prática, não está no POL-001). Nesse caso, o atendente pode:

- **Registrar a divergência no momento**, indicando o que usou no lugar da resposta do assistente e por quê;
- **Seguir com o atendimento** usando seu julgamento, mas o registro dessa divergência alimenta automaticamente o fluxo de feedback (Seção 4) — o desacordo pontual de hoje vira sinal formal, sem exigir uma ação extra do atendente.

Isso evita dois riscos opostos: o atendente ignorar a divergência (e o problema nunca ser corrigido) ou tratar a divergência como incidente isolado sem avisar ninguém.

---

## 4. Fluxo de feedback — sinalização de resposta errada, desatualizada ou incompleta

Hoje isso não existe de forma formal; é o gap que a Compliance e a Atendimento levantaram no discovery. A proposta:

1. **Toda resposta do assistente tem um botão de sinalização** ("Esta resposta está errada, desatualizada ou incompleta?"), sem sair do Teams.
2. **Atendente escolhe o motivo** entre categorias curtas: desatualizada, incorreta, incompleta, ou conflito entre fontes — com campo de texto livre opcional para contexto (ex.: "cliente com 8-9 fretes/mês, o FAQ diz que não tem desconto, mas a v2 indica que teria").
3. **A sinalização abre um chamado automático** para o dono da fonte correspondente (definido no comitê de governança), com SLA de resposta definido previamente.
4. **Enquanto não resolvido, o conteúdo sinalizado passa a exibir um aviso** para qualquer outro atendente que consultar aquele mesmo trecho — o que evita que o erro do item 45 do FAQ se repita silenciosamente para outros atendentes enquanto ninguém mais percebe.
5. **Quando o dono resolve** (corrige o documento, confirma que estava certo, ou formaliza uma prática até então informal), o atendente que sinalizou é notificado e o aviso é removido.
6. **Métricas do próprio fluxo** — número de sinalizações por fonte, tempo médio de resolução, taxa de sinalizações procedentes — viram insumo direto para a priorização de revisão documental do comitê de governança, fechando o ciclo que hoje termina em mensagem perdida no grupo do Teams.

---

## 5. Guardrails de comportamento do assistente

1. **Nunca inventar informação que não esteja documentada** — prazos, valores, percentuais e regras só podem vir de uma fonte indexada. Se não houver base, o assistente declara explicitamente que não encontrou a informação, em vez de compor algo plausível (isso é o que teria evitado o erro do item 45 do FAQ se ele tivesse sido gerado por um assistente, e é a mesma lógica que impede o assistente de "inventar" uma regra de seguro de carga que nunca foi formalizada).
2. **Nunca responder sem citar a fonte exata e sua vigência** — documento, versão e data. Uma resposta sem essa referência não é entregue ao atendente.
3. **Declarar conflito entre fontes em vez de escolher uma versão silenciosamente** — se duas fontes válidas divergem (o cenário PROC-042), o assistente expõe o conflito e reduz seu nível de confiança, não decide sozinho qual versão vale.
4. **Tratar conteúdo não certificado com um selo visível de "não oficial"** — respostas baseadas em FAQ não validado nunca aparecem com o mesmo peso visual ou o mesmo nível de confiança de uma política ou procedimento oficial.
5. **Não avançar sobre juízo jurídico ou comercial que a documentação não sustenta** — em temas como sinistro e seguro de carga, enquanto não houver documento oficial, o assistente sinaliza a lacuna e encaminha para validação humana, em vez de formular uma posição própria.

---

## 6. Papéis e SLAs (governança que sustenta o fluxo)

| Fonte / tema | Dono | Acionado quando |
|---|---|---|
| Regras de frete e cálculo (PROC-042) | Operações + TI | Conflito de versão, fallback de baixa confiança |
| Política de devolução e SLA | Comercial | Sinalização de conteúdo desatualizado |
| Seguro de carga e sinistro | Jurídico + Compliance | Conteúdo sem lastro oficial, fallback |
| FAQ do time de atendimento | Atendimento + Compliance | Validação de novos itens, sinalização de erro |
| Estrutura documental e indexação | TI | Ausência de campo de vigência, duplicidade de documentos |

---

## 7. Métricas de sucesso da jornada

- Tempo médio de busca por chamado: de 12 min para o tempo de uma única consulta.
- Taxa de escalonamento: de ~19% (e ~30% em frete) para um patamar a definir após piloto.
- Nota média de atendimento: de 6,1 para a meta que o cliente definir.
- Número de sinalizações abertas vs. resolvidas dentro do SLA, por fonte — indicador de saúde da governança, não só do assistente.

---

## 8. Próximos passos

- Validar os limiares de confiança (Alta / Média / Baixa) com TI e com os donos de cada fonte.
- Confirmar com o comitê de governança quem assume cada linha da tabela da Seção 6.
- Definir o SLA de resposta às sinalizações antes do piloto, para não repetir a lacuna atual.
- Rodar esta jornada com um grupo piloto de atendentes sobre os temas já mapeados (frete, devolução, prazos) antes de estender ao restante do acervo.
