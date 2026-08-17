# Revisão de QA — Testabilidade da Especificação de Requisitos de RAG
## Projeto NovaTech

**Autor:** QA, DB1
**Objeto revisado:** `spec-requisitos-rag-novatech.md` (REQ-01 a REQ-33)
**Pergunta que orienta esta revisão:** para cada requisito, é possível observar e validar objetivamente, no produto entregue, se ele foi atendido ou não — sem depender de interpretação pessoal do avaliador?

---

## 1. Metodologia da revisão

Um requisito é considerado **testável** quando atende aos 4 critérios abaixo simultaneamente:

1. **Tem critério de teste explícito** — não basta a intenção estar clara no texto; precisa existir uma frase objetiva de "como verificar".
2. **Tem um resultado binário ou mensurável** — pass/fail, ou uma métrica com limiar numérico definido (não "resposta adequada", "compatível", "razoável").
3. **Não depende de dado ainda não definido** — se o critério de teste cita um limiar, uma lista ou um mapeamento que ainda não existe (ex.: "SLA de revisão" sem número), o requisito só é testável condicionalmente, após esse dado ser fechado.
4. **Não depende de um artefato não especificado** — se o critério menciona algo visual ("selo", "aviso", "rótulo") sem uma referência de design/formato, o QA não tem como decidir objetivamente se o critério foi cumprido.

Classificação usada abaixo:
- ✅ **Testável** — atende aos 4 critérios.
- ⚠️ **Parcialmente testável** — tem intenção clara, mas falta 1+ critério (geralmente: falta a frase de critério de teste, ou o critério existe mas depende de algo ainda não definido).
- ❌ **Não testável como está** — não há como um avaliador (ou dois avaliadores independentes) chegar ao mesmo veredito com o texto atual.

---

## 2. Achado principal (transversal)

**17 dos 33 requisitos (~52%) não têm uma frase explícita de "Critério de teste".** Eles descrevem bem a intenção de produto, mas terminam na explicação de racional, sem uma linha objetiva de "como o QA verifica isso no produto entregue". Isso é o achado mais importante desta revisão — é um padrão sistemático, não um problema pontual em 1 ou 2 requisitos.

Requisitos afetados: **REQ-06, REQ-09, REQ-11, REQ-14, REQ-16, REQ-17, REQ-18, REQ-19, REQ-20, REQ-23, REQ-24, REQ-27, REQ-28, REQ-29, REQ-30, REQ-32, REQ-33.**

Isso é especialmente crítico em REQ-17 (citação de fonte) e REQ-19 (log de auditoria) — são dois dos requisitos mais centrais do documento (rastreabilidade, o pilar que o Jurídico e o Compliance pediram no discovery) e nenhum dos dois tem uma frase de verificação objetiva.

---

## 3. Tabela de veredito por requisito

| REQ | Veredito | Motivo |
|---|---|---|
| 01 | ✅ Testável | Critério objetivo (campo preenchido, relatório de cobertura). |
| 02 | ✅ Testável | Critério objetivo, com exemplo concreto (PROC-042/v2). |
| 03 | ✅ Testável | Critério objetivo, mas ver nota 4.1 sobre "aviso explícito" não estar definido em formato. |
| 04 | ⚠️ Parcial | Critério existe, mas cita "selo visual" — não há referência de design/formato (nota 4.2). |
| 05 | ✅ Testável | Critério objetivo e específico ao caso do seguro de carga. |
| 06 | ❌ Não testável | Não tem critério de teste próprio — só o texto de definição da hierarquia (nota 4.3). |
| 07 | ✅ Testável | Critério objetivo, com pergunta de teste concreta. |
| 08 | ⚠️ Parcial | Critério existe e é objetivo, mas depende de resolver a sobreposição com REQ-03 (nota 4.4). |
| 09 | ❌ Não testável | "Deve ser detectável" não define frequência, método nem responsável (nota 4.5). |
| 10 | ⚠️ Parcial | Critério existe para o caso (a); o caso (b) tem fronteira subjetiva não definida (nota 4.6). |
| 11 | ❌ Não testável | Não tem critério de teste — e o mecanismo de detecção ("citação indexada") não está definido em formato verificável (nota 4.7). |
| 12 | ⚠️ Parcial | Critério objetivo só para o exemplo "Platinum"; falta lista de referência para generalizar (nota 4.8). |
| 13 | ✅ Testável | Critério objetivo; recomendo só amarrar a definição de "hora útil" já usada no POL-001 (nota 4.9). |
| 14 | ❌ Não testável | Não tem critério de teste explícito. |
| 15 | ⚠️ Parcial | Critério existe, mas depende do "SLA de revisão" — hoje sem número definido (é ponto em aberto da Seção 8). |
| 16 | ❌ Não testável | Não tem critério de teste explícito. |
| 17 | ❌ Não testável | Requisito central de rastreabilidade sem critério de teste — prioridade máxima de correção. |
| 18 | ❌ Não testável | Não tem critério de teste explícito nem métrica de usabilidade. |
| 19 | ❌ Não testável | Requisito central de auditoria sem critério de teste — prioridade máxima de correção. |
| 20 | ❌ Não testável | Não tem critério de teste explícito. |
| 21 | ✅ Testável | Critério objetivo (verificação documental do ADR). |
| 22 | ✅ Testável | Critério objetivo (existência de estimativa e processo de acompanhamento). |
| 23 | ❌ Não testável | Sem critério, e "compatível com os compromissos do SLA-2024" não tem número (nota 4.10). |
| 24 | ⚠️ Parcial | Menciona "testado antes do go-live" mas sem RTO/RPO definidos nem critério isolado (nota 4.11). |
| 25 | ✅ Testável | Critério objetivo (cobertura de classificação). |
| 26 | ⚠️ Parcial | Critério objetivo, mas cobertura de teste depende de uma matriz perfil × classificação ainda não definida (nota 4.12). |
| 27 | ❌ Não testável | Não tem critério de teste — e o comportamento exige verificação sutil (nota 4.13). |
| 28 | ❌ Não testável | Não tem critério de teste explícito. |
| 29 | ❌ Não testável | Não tem critério de teste explícito. |
| 30 | ❌ Não testável | Não tem critério de teste explícito. |
| 31 | ✅ Testável | Critério objetivo e verificável por log. |
| 32 | ❌ Não testável | Sem critério, e sem limiar definido para "restringir" vs. "abrir para a base inteira" (nota 4.14). |
| 33 | ❌ Não testável | Não tem critério de teste explícito. |

**Resumo:** 10 ✅ testáveis como estão, 6 ⚠️ parcialmente testáveis, **17 ❌ não testáveis como estão.**

---

## 4. Notas detalhadas (ambiguidades que vão além de "falta a frase")

**4.1 (REQ-03)** — "aviso explícito de revisão pendente" precisa de um texto de referência mínimo (ex.: um template de mensagem), senão dois QAs podem divergir sobre o que conta como "explícito o suficiente".

**4.2 (REQ-04)** — O documento usa "selo visual" em vários requisitos (REQ-04, REQ-08, REQ-12 indiretamente) sem nenhuma referência de como isso aparece na interface do Teams. Sem uma definição de UI (mesmo que seja só textual, ex.: prefixo `[NÃO OFICIAL]` antes da resposta), não há como testar objetivamente "o mesmo peso visual".

**4.3 (REQ-06)** — A hierarquia está bem definida como regra, mas não virou requisito testável isoladamente. Sugestão: adicionar um critério do tipo "dado um tema com fontes nos 4 níveis simultaneamente disponíveis, a resposta usa a fonte do nível mais alto disponível, e cita explicitamente por que descartou os níveis inferiores quando há conflito relevante".

**4.4 (REQ-08 x REQ-03)** — Fica ambíguo se um documento `sujeito_a_revisao` (REQ-03) é **sempre** nível Baixa (REQ-08) mesmo quando não há conflito de versão nem problema de vigência — ou se o aviso de revisão pendente e o nível de confiança são dimensões independentes. Como está, dois QAs podem classificar esse caso de forma diferente. Recomendo decidir explicitamente: revisão pendente rebaixa a confiança sempre, ou é um aviso paralelo que não necessariamente muda o nível.

**4.5 (REQ-09)** — Não há: (a) frequência definida da auditoria periódica, (b) método (é uma revisão humana amostral? um comparador automático entre versões de um mesmo tema?), (c) quem executa. Sem isso, não dá para verificar se o requisito "está sendo cumprido" em um dado mês — só dá para constatar depois que um erro já aconteceu (como o item 45 do FAQ).

**4.6 (REQ-10, caso b)** — A linha entre "conhecimento geral de mercado" (permitido, rotulado) e "regra específica da NovaTech" (proibido sem fonte) é subjetiva no texto atual. Um exemplo como "explicar o que é CT-e" é claro, mas perguntas de fronteira (ex.: "qual o prazo médio de entrega no mercado de logística?") podem ser classificadas de forma diferente por dois avaliadores. Recomendo decisão explícita da Seção 8 (ponto 4) antes de considerar este requisito testável — hoje ele description descreve intenção, não um limite operacional.

**4.7 (REQ-11)** — O requisito diz que o filtro verifica se a resposta "contém pelo menos uma citação de fonte indexada", mas não define **o que conta estruturalmente como citação** (é um campo separado retornado pelo sistema, ou é texto livre que precisa ser reconhecido por regex/heurística dentro da resposta?). Se for texto livre, o próprio filtro determinístico terá uma taxa de erro (falso positivo/negativo) que precisa ser testada e reportada — hoje o requisito não prevê isso.

**4.8 (REQ-12)** — O teste cobre bem o caso "Platinum", mas não existe, em lugar nenhum da especificação, uma lista fechada de valores válidos (tiers, regiões, classes de carga perigosa, categorias de FAQ) que o QA possa usar como gabarito para gerar outros casos de teste equivalentes. Sem esse "dicionário de domínio", a cobertura de teste depende de o QA lembrar de cada caso manualmente.

**4.9 (REQ-13)** — "24 horas úteis" precisa herdar a mesma definição de dia/hora útil já usada no POL-001 (exclui fins de semana e feriados) e, se possível, a janela de expediente (08h–18h) usada no SLA-2024 seção 5 — hoje o requisito não faz esse vínculo explícito, abrindo espaço para interpretações diferentes de "quando o relógio conta".

**4.10 (REQ-23)** — "SLA de disponibilidade compatível com os compromissos contratuais" não tem número. O próprio SLA-2024 já define disponibilidade de portal (99,5% Gold, 99,0% Silver, 98,0% Standard) — sugiro usar esse padrão como piso mínimo explícito para a disponibilidade do banco vetorial, em vez de "compatível".

**4.11 (REQ-24)** — Falta definir RTO (tempo máximo aceitável de indisponibilidade durante um restore) e RPO (perda máxima de dados aceitável) como números. "Testado antes do go-live" é necessário mas não suficiente — sem RTO/RPO, não há como o QA aprovar ou reprovar o resultado do teste de restore.

**4.12 (REQ-26)** — O critério de teste dá um exemplo (atendente padrão vs. supervisor), mas não define a matriz completa de perfis × níveis de confidencialidade que precisa ser testada (quantos perfis existem, além de "atendente padrão" e "supervisor"? Jurídico, Comercial sênior, outros?). Isso está ligado ao ponto em aberto 7 da Seção 8 — enquanto os perfis não forem mapeados, a cobertura de teste deste requisito é necessariamente incompleta.

**4.13 (REQ-27)** — Este é um caso de teste sutil e vale registrar como risco de qualidade, não só como "falta a frase": para provar que o assistente "não revela a existência" do conteúdo restrito, o QA precisa comparar a resposta de um caso de "sem permissão" com a de um caso real de "sem informação na base" e confirmar que são indistinguíveis — inclusive em tempo de resposta (uma latência sistematicamente diferente entre os dois casos já seria um vazamento indireto por canal lateral). O requisito, como está, não pede essa verificação de latência.

**4.14 (REQ-32)** — Não há limiar definido para quando a busca "abre para a base inteira" depois de restringir a um ramo. Sem um número (ex.: "se os top-5 resultados dentro do ramo tiverem similaridade abaixo de X, expande a busca"), o comportamento não é verificável de forma determinística — cada execução poderia "decidir" isso de um jeito.

---

## 5. Achados de tipo de teste (não é sobre estar certo ou errado, é sobre qual tipo de verificação cabe)

Vale separar, para o plano de testes, quais requisitos são:

- **Teste de sistema/automatizável** (a maioria de REQ-01 a REQ-12, REQ-17, REQ-25 a REQ-27, REQ-30 a REQ-33): dá para rodar contra um conjunto de perguntas de teste (o mapa de cobertura do Anexo B é a base natural para isso) e obter pass/fail.
- **Teste de processo/auditoria organizacional** (REQ-15, REQ-21, REQ-22, REQ-23, REQ-28, REQ-29, Seção 6 como um todo): não se testa rodando uma pergunta no assistente — se testa verificando se um comitê existe, se um relatório foi gerado, se um documento de responsabilidade foi assinado. É testável, mas com auditoria de processo, não com caso de teste funcional.
- **Teste de infraestrutura/operação** (REQ-22, REQ-23, REQ-24): precisa de um ambiente de homologação para simular indisponibilidade e restore — não é verificável só lendo a especificação ou olhando uma resposta do assistente.

Recomendo que o plano de testes do projeto (a ser feito em exercício futuro) já separe os requisitos por esse eixo, porque a estratégia de verificação é diferente para cada grupo — isso também é coerente com o entendimento de que testes de IA são não-determinísticos e variam em grau de qualidade, enquanto testes de processo/infra são binários.

---

## 6. Recomendação priorizada

1. **Prioridade alta** — fechar o critério de teste ausente em REQ-17 e REQ-19: são os dois requisitos que sustentam toda a exigência de rastreabilidade e auditoria pedida por Jurídico/Compliance no discovery; hoje nenhum dos dois tem uma frase objetiva de verificação.
2. **Prioridade alta** — decidir a fronteira de "resposta genérica" (nota 4.6) antes do piloto; sem isso, REQ-10 e REQ-11 (o requisito que você pediu para adicionar nesta rodada) não são verificáveis de forma consistente por dois avaliadores diferentes.
3. **Prioridade média** — quantificar os limiares hoje descritos como "compatível"/"detectável"/qualitativos: SLA de revisão (REQ-15), disponibilidade do banco vetorial (REQ-23), RTO/RPO do restore (REQ-24), limiar de expansão de busca por ramo (REQ-32).
4. **Prioridade média** — criar dois artefatos de apoio que faltam hoje e que vários requisitos pressupõem: um **dicionário de domínio** (valores válidos de tier, região, classe de carga — nota 4.8) e uma **referência mínima de UI/rótulos** (nota 4.2), mesmo que só textual.
5. **Prioridade baixa, mas recomendável** — completar a frase de "Critério de teste" nos demais 12 requisitos da lista da Seção 2, mesmo quando a correção é simples (a maioria só precisa de uma frase adicional, a intenção já está clara no texto).

Posso aplicar essas correções diretamente na especificação (adicionando a linha de critério de teste em cada um dos 17 requisitos e ajustando as notas de ambiguidade), se você quiser — ou podemos primeiro fechar as decisões de negócio pendentes (fronteira da resposta genérica, limiares de SLA) e só depois consolidar a versão testável final.
