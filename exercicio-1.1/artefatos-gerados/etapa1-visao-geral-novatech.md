# Análise de Artefatos — NovaTech
## Etapa 1: Visão Geral (Mapa de Temas e Hipóteses de Gaps)

**Data:** 2026-08-09
**Insumo:** Metadados de 5 documentos fornecidos pelo cliente

---

## 1. Mapa de temas cobertos

| # | Documento | Tema / Área | Observação |
|---|---|---|---|
| 1 | POL-001 — Política de Devolução de Mercadorias | Devolução / Pós-venda | Regras claras (prazo 7 dias), com exceção para carga perigosa — já indica interseção com o tema "segurança de carga" |
| 2 | PROC-042 — Cálculo de Frete Especial | Frete / Precificação | Fórmula para fretes >500kg, multiplicadores por região |
| 3 | SLA-2024 — Tabela de SLA por Tipo de Cliente | Atendimento / Prazos contratuais | Segmentação Gold / Silver / Standard |
| 4 | PROC-042-v2 — Cálculo de Frete (Revisado) | Frete / Precificação | Mesma numeração do item 2, multiplicadores diferentes, **sem indicação de vigência** |
| 5 | FAQ-Atendimento | Conhecimento tácito de suporte | 47 perguntas informais, sem validação formal — provavelmente perpassa vários temas (frete, devolução, prazos etc.) |

Cruzando com os quatro temas de dúvida citados no cenário original (prazos, regras de frete, devolução, reclamação):

- **Prazos** → coberto por SLA-2024
- **Regras de frete** → coberto, mas em **duas versões conflitantes** (PROC-042 e PROC-042-v2)
- **Devolução** → coberto por POL-001
- **Reclamação** → **sem documento formal identificado** nesta amostra
- **Segurança de carga** → aparece apenas de forma indireta (exceção dentro de POL-001), sem documento dedicado nesta amostra
- **Compliance** → não representado nesta amostra

---

## 2. Hipóteses de gaps

### Gaps de conteúdo (o que pode estar faltando)

1. **Procedimento de reclamação de clientes** — é um dos quatro tipos de dúvida citados como motivador do projeto, mas não aparece em nenhum dos 5 metadados. Hipótese: existe em outro lugar (SharePoint/Confluence) e não foi incluído nesta amostra, ou é tratado apenas informalmente (possivelmente dentro do FAQ). Precisa confirmação com o cliente.
2. **Normas de segurança de carga** como documento próprio — hoje só aparece como exceção dentro da política de devolução. Se for um tema recorrente de dúvida, pode não ter documentação own dedicada e formal o suficiente para fundamentar respostas do assistente.
3. **Políticas de compliance** — citadas no cenário geral do projeto, mas ausentes nesta amostra. Pode ser só um recorte da entrega do cliente, mas vale confirmar se compliance está no escopo do MVP do assistente.

### Gaps de governança documental (riscos para a base de conhecimento do assistente)

4. **Conflito de versionamento crítico (PROC-042 vs. PROC-042-v2)** — mesma numeração, conteúdo divergente (multiplicadores diferentes), sem indicação de qual está vigente. Se ambos forem indexados sem resolução, o assistente pode responder de forma contraditória sobre cálculo de frete — o próprio problema que o projeto busca eliminar. **Isso precisa virar uma pergunta obrigatória ao cliente antes de qualquer indexação.**
5. **Ausência de metadados de governança** — nenhum dos documentos traz campos como responsável/dono, data de vigência, status (ativo/obsoleto) ou última revisão. Sem isso, não há como o assistente (ou a equipe da DB1) determinar automaticamente qual fonte é a autoritativa em casos de conflito, nem programar expurgo de conteúdo desatualizado.
6. **Documento informal sem validação (FAQ-Atendimento)** — tem valor prático (conhecimento tácito de atendentes experientes) mas risco de autoridade: se indexado com o mesmo peso que uma política oficial, pode fazer o assistente "confirmar" respostas não oficiais como se fossem regra da empresa. Duas hipóteses de tratamento a validar com o cliente: (a) usar como fonte de apoio com peso/prioridade menor e citação explícita de "fonte não oficial"; (b) promover trechos validados para virarem documentação oficial antes de entrar na base.

---

## 3. Perguntas sugeridas para a próxima rodada com o cliente

- Qual das duas versões de PROC-042 está vigente hoje? Existe um processo de controle de versão nos repositórios de origem (SharePoint/Confluence)?
- Existe documentação formal de procedimento de reclamação? Se sim, por que não apareceu nesta amostra?
- Há um documento (ou deveria haver) dedicado às normas de segurança de carga, além da menção em POL-001?
- Como o cliente deseja tratar o FAQ informal: como fonte válida, fonte secundária, ou insumo para formalizar novos documentos oficiais?
- Existe algum inventário/mapa de metadados (dono, data de vigência, status) para os ~1.200 documentos totais (800 SharePoint + 400 Confluence)? Essa amostra de 5 é representativa do padrão geral?

---

*Próxima etapa: aguardando novos artefatos do cliente para aprofundar a análise de intenção do projeto.*
