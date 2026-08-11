# Simulação de Discovery com Stakeholders — Governança de Dados (Projeto NovaTech)

**Uso:** material de preparação interna da DB1. Simula como uma sessão de discovery facilitada por um Product Specialist, com as áreas donas das fontes de documentação, tende a se desenrolar — com base nos achados reais das Etapas 1-3 (conflito PROC-042, erro do item 45 do FAQ, gap do processo de sinistro) e no padrão de problema já quantificado pelo cliente (12 min/chamado, 45 atendentes, 3 fontes). Os números abaixo são hipóteses de trabalho para orientar a condução da sessão real — precisam ser validados com o cliente, não são dados coletados.

**Facilitação:** Product Specialist, DB1
**Duração simulada:** 90 minutos
**Participantes simulados:**

- Fernanda Lima — Supervisora de Atendimento ao Cliente
- Marcos Andrade — Gerente de TI / Infraestrutura de Conteúdo
- Juliana Prado — Analista de Compliance
- Rafael Souza — Gerente Comercial
- Débora Nunes — Coordenadora de Operações
- Thiago Bezerra — Consultor Jurídico

---

## 1. Abertura

O Product Specialist abre a sessão retomando o objetivo: entender, com quem convive com a documentação todo dia, por que a informação que chega ao atendente tantas vezes está incompleta, desatualizada ou contraditória — e o que isso custa em governança antes de qualquer linha de código do assistente ser escrita.

Pergunta de abertura: *"Quando um atendente não tem certeza da resposta, o que ele faz hoje?"*

---

## 2. Painel consolidado — o que o discovery revela

> Os atendentes hoje abrem em média **4,2 fontes diferentes** por chamado (SharePoint, Confluence, a pasta de rede e, na prática, o FAQ informal dos colegas, mesmo sem validação oficial). As dúvidas mais comuns são sobre **prazos de entrega (34%)**, **regras de frete (27%)**, **política de devolução (21%)** e **outros temas — reclamação e sinistro (18%)**. Em **19% dos casos**, o atendente não encontra uma resposta com confiança suficiente e escalona para o supervisor — número que sobe para **perto de 30%** especificamente nos chamados sobre frete, por causa da divergência entre PROC-042 e PROC-042-v2. A nota média de atendimento de 0 a 10 fica em **6,1 (61% de satisfação)**. Os atendentes reclamam da falta de padronização entre fontes e da ausência de um jeito simples de avisar quando percebem algo errado na documentação — hoje isso vira mensagem avulsa no grupo do Teams, sem dono e sem rastro.

Esse painel é a versão NovaTech do padrão que costuma aparecer em discoveries desse tipo: o problema não é falta de conteúdo, é falta de uma fonte única, atualizada e com dono claro — e de um canal formal para reportar quando o conteúdo falha.

---

## 3. O que cada área trouxe (falas simuladas)

**Fernanda Lima (Atendimento):** *"A gente já aprendeu a desconfiar do PROC-042. Tem atendente que decorou 'a v2 é a que vale', mas isso não devia depender da memória de ninguém. E quando eu escalono pro meu supervisor, muitas vezes ele também não sabe — ele pergunta pra outro supervisor, e aí já são três pessoas gastando tempo em algo que devia estar resolvido num documento só."*

**Marcos Andrade (TI):** *"O SharePoint não tem um campo obrigatório de 'versão vigente'. Qualquer pessoa sobe um arquivo novo com o mesmo prefixo e ele fica lá, ativo, ao lado do antigo. A gente tem quase 800 documentos e nenhum processo de arquivamento formal — então esse problema do frete provavelmente não é o único caso, é só o único que a análise pegou porque alguém perguntou."*

**Juliana Prado (Compliance):** *"O que me preocupa mais é o FAQ. Foi escrito pelos próprios atendentes, o que é ótimo pra pegar a linguagem real do cliente, mas ele está circulando com o mesmo peso de uma política oficial. Se um auditor perguntar de onde veio a regra do seguro de carga de 0,3% a 0,8%, hoje eu não tenho uma resposta — não existe isso em nenhum documento formal que eu conheça. E soube que a PROC-043, de cargas perigosas, está em revisão aqui dentro; se ela mudar antes do assistente ir ao ar, o conteúdo já nasce desatualizado."*

**Rafael Souza (Comercial):** *"Isso já é dinheiro na mesa. Vocês mostraram o caso do cliente com 8 a 9 fretes por mês sendo informado de que não tinha direito a desconto, quando pela v2 ele teria. Eu não sei há quanto tempo isso está acontecendo nem quantos clientes passaram pela mesma situação — e isso é relação com cliente, não é só um erro de sistema."*

**Débora Nunes (Operações):** *"O processo de sinistro que vocês encontraram existe, sim — janela de 48h, e-mail para sinistros@novatech.com.br, aciona o Jurídico. Mas isso nunca foi formalizado como procedimento, é uma prática que a operação foi construindo. Se o assistente for treinado só com o que está documentado oficialmente, ele vai dizer pro cliente que esse processo não existe — o que é pior do que não responder."*

**Thiago Bezerra (Jurídico):** *"Do ponto de vista jurídico, o risco maior é o assistente citar como oficial algo que nunca passou por validação — o seguro de carga é o exemplo mais claro. Se isso virar uma resposta 'com fonte', formalizada, put entre aspas para o cliente, e depois a gente disser que aquilo nunca foi política da empresa, o problema deixa de ser de atendimento e passa a ser de responsabilidade contratual."*

---

## 4. Conexão com os achados técnicos das Etapas 1-3

A sessão simulada não introduz problemas novos — ela dá nome de dono a cada achado já levantado:

| Achado técnico (Etapas 1-3) | Área responsável | O que falta |
|---|---|---|
| PROC-042 vs PROC-042-v2, sem versão vigente definida | TI + Operações | Campo obrigatório de status/vigência e processo de arquivamento |
| Item 45 do FAQ com desconto errado (mistura v1 e v2) | Comercial + Atendimento | Validação formal do FAQ contra a fonte oficial vigente |
| Processo de sinistro não documentado em POL-001 | Operações + Jurídico | Formalização como procedimento oficial |
| Seguro de carga (0,3%/0,8%) só no FAQ, sem lastro oficial | Compliance + Jurídico | Confirmação da regra e documento de origem |
| PROC-043 em revisão pelo Compliance | Compliance | Prazo de conclusão antes da indexação |
| Ausência de canal para reportar inconsistência | Atendimento + TI | Mecanismo formal de sinalização com dono e rastreabilidade |

---

## 5. Mapa de risco de governança (se nada for endereçado)

| Risco | Probabilidade | Impacto | Evidência já observada |
|---|---|---|---|
| Assistente reproduz uma resposta desatualizada como se fosse oficial | Alta | Alto | Conflito PROC-042 / erro item 45 do FAQ |
| Conteúdo sem dono nem ciclo de revisão vira "verdade" citada com fonte | Alta | Alto | FAQ informal tratado como política |
| Prática operacional real (sinistro) fica invisível para o assistente | Média | Alto | Processo de sinistro fora do POL-001 |
| Exposição contratual/jurídica por informação não validada | Média | Alto | Seguro de carga sem lastro oficial |
| Efeito de escala com o acervo completo (~1.200 documentos, atualização mensal) — o problema piora, não dilui, conforme mais fontes entram (context rot) | Alta | Alto | Extrapolação da amostra de 5 documentos |

*(Esse mapa é a versão textual do gráfico de matriz de risco já gerado — `mapa_risco_novatech.png` — que pode ser anexado como apoio visual na apresentação ao cliente.)*

---

## 6. O que isso vira em requisito para o assistente de IA

1. **Fonte única de verdade por tema**, com campo de vigência obrigatório — elimina o cenário PROC-042.
2. **Hierarquia de autoridade documental** (política/procedimento oficial > FAQ validado > FAQ não validado), com o assistente indicando o nível de confiança e a origem de cada resposta.
3. **Canal de sinalização de inconsistência** dentro do próprio fluxo do atendente no Teams — com dono definido por área e trilha de auditoria, para que o problema encontrado hoje na análise não dependa de uma revisão manual esporádica para ser pego de novo.
4. **Comitê de governança recorrente**, com um dono por fonte (Comercial, Operações, Compliance, Jurídico, TI, Atendimento) e SLA de revisão — pré-requisito para lidar com a escala do acervo completo sem repetir o padrão de context rot.
5. **Métricas de acompanhamento pós-implantação**: redução do tempo médio de busca (hoje 12 min/chamado), redução da taxa de escalonamento (hoje ~19%) e evolução da nota de satisfação (hoje 6,1).

---

## 7. Próximos passos sugeridos

- Validar esta simulação com o cliente antes da sessão real — os números aqui são hipóteses de trabalho, não dados coletados.
- Confirmar a lista de documentos pendentes já levantada nas Etapas 1-3: PROC-043, PROC-088, política de seguro de carga, política de sinistros e o restante do FAQ.
- Usar a tabela da Seção 4 como roteiro de perguntas por área na sessão real de discovery.
- Definir, ainda na sessão real, quem assume cada linha do comitê de governança proposto na Seção 6.
