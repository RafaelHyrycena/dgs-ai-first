# Checklist de Execução – Pipeline RAG NovaTech

## Resumo Geral

| Categoria    | Total de Testes | Executados | Aprovados | Reprovados | Responsável |
|--------------|-----------------|------------|-----------|------------|-------------|
| Ingestão     | 4               | —          | —         | —          |             |
| Retrieval    | 5               | —          | —         | —          |             |
| Geração      | 4               | —          | —         | —          |             |
| Contexto     | 5               | —          | —         | —          |             |
| E2E          | 4               | —          | —         | —          |             |
| Regressão    | 5               | —          | —         | —          |             |
| **Total**    | **27**          |            |           |            |             |

---

## Testes de Ingestão

| ID     | Descrição                                                                 | Prioridade | Responsável | Critério de aceite                                                                 | Status   | Obs. |
|--------|---------------------------------------------------------------------------|------------|-------------|------------------------------------------------------------------------------------|----------|------|
| ING-01 | Verificar presença dos 5 documentos esperados no índice                   | Alta       |             | 100% dos documentos (POL-001, PROC-042 v1, PROC-042 v2, SLA-2024, FAQ-Atendimento) disponíveis para busca | Pendente |      |
| ING-02 | Verificar separação entre PROC-042 v1 e v2 como documentos distintos      | Alta       |             | PROC-042 v1 e v2 recuperáveis separadamente, sem sobreposição                     | Pendente |      |
| ING-03 | Verificar preservação de metadados de origem                              | Média      |             | Campos de identificação (nome, seção/versão) presentes para rastreabilidade de chunk | Pendente |      |
| ING-04 | Verificar ausência de documentos fora do escopo dos anexos                | Média      |             | Nenhum documento externo ao escopo indexado                                        | Pendente |      |

---

## Testes de Retrieval

| ID     | Pergunta de referência                     | Chunk esperado              | Prioridade | Responsável | Critério de aceite                                                                              | Status   | Obs. |
|--------|--------------------------------------------|-----------------------------|------------|-------------|--------------------------------------------------------------------------------------------------|----------|------|
| RET-01 | Qual o prazo de devolução?                 | POL-001-A                   | Alta       |             | Chunk recuperado entre primeiros resultados; resposta alinhada ao conteúdo oficial              | Pendente |      |
| RET-02 | Posso devolver carga perigosa?             | POL-001-B                   | Alta       |             | Chunk recuperado; orientação para Gestão de Riscos presente                                     | Pendente |      |
| RET-03 | Qual o SLA do cliente Platinum?            | SLA-2024-A                  | Alta       |             | Chunk informa ausência do tier Platinum; tiers válidos listados                                 | Pendente |      |
| RET-04 | Frete para 600 kg para Manaus?             | PROC-042v2-B e PROC-042v2-A | Alta       |             | Chunks da v2 recuperados; regra de frete especial (>500 kg) e multiplicador Norte aplicados     | Pendente |      |
| RET-05 | Frete para 300 kg para Salvador?           | Sem documento aplicável     | Alta       |             | Sistema informa ausência de cobertura sem inventar valor ou regra                               | Pendente |      |

---

## Testes de Geração

> Avaliação qualitativa — resultado não é apenas Pass/Fail. Registrar grau de aderência e evidência textual no campo de observações.

| ID     | Cenário / risco                                                   | Critério de qualidade | Prioridade | Responsável | Critério de aceite                                                                                        | Status   | Obs. |
|--------|-------------------------------------------------------------------|-----------------------|------------|-------------|-----------------------------------------------------------------------------------------------------------|----------|------|
| GER-01 | Alucinação — dados inventados não presentes nos chunks            | Qualitativo           | Alta       |             | Nenhum dado sem suporte textual nos chunks recuperados                                                    | Pendente |      |
| GER-02 | Resposta incompleta — omissão de elemento obrigatório             | Qualitativo           | Alta       |             | Todos os itens obrigatórios do checklist por pergunta presentes na resposta                               | Pendente |      |
| GER-03 | Uso incorreto do contexto — FAQ tratado como fonte formal         | Qualitativo           | Média      |             | Resposta distingue fonte formal (POL/PROC/SLA) de FAQ informal quando aplicável                           | Pendente |      |
| GER-04 | Conflito entre documentos — mistura de versões PROC-042 v1 e v2  | Qualitativo           | Alta       |             | Resposta aplica v2 para chamados novos e explica exceção transitória quando pertinente                    | Pendente |      |

---

## Testes de Contexto

| ID     | Cenário                        | Verificação                                                                                       | Prioridade | Responsável | Critério de aceite                                                                     | Status   | Obs. |
|--------|--------------------------------|---------------------------------------------------------------------------------------------------|------------|-------------|----------------------------------------------------------------------------------------|----------|------|
| CTX-01 | Context window budget          | Chunks críticos (POL-001-B, PROC-042v2-B, SLA-2024-A/B) cabem no orçamento de tokens com o prompt | Alta       |             | Nenhum chunk essencial truncado ou excluído por limite de tokens                       | Pendente |      |
| CTX-02 | Lost in the middle             | Chunk crítico posicionado no meio do contexto continua utilizado corretamente                     | Alta       |             | Consistência factual independente da posição do chunk no prompt                        | Pendente |      |
| CTX-03 | Context rot                    | Consistência após múltiplas iterações de perguntas relacionadas no mesmo atendimento              | Média      |             | Respostas coerentes ao longo de toda a sessão sem degradação factual                   | Pendente |      |
| CTX-04 | Conversas longas               | Precisão mantida após histórico extenso e múltiplas retomadas de assunto                          | Média      |             | Sem queda de qualidade detectável em sessões longas                                    | Pendente |      |
| CTX-05 | Ordem dos chunks recuperados   | Impacto do ranking na resposta, especialmente conflitos PROC-042 v1 vs v2                        | Alta       |             | Ranking não inverte a prioridade de v2 sobre v1 para chamados novos                    | Pendente |      |

---

## Testes E2E

| ID     | Fluxo validado                                                        | Chunks esperados                              | Prioridade | Responsável | Critério de aceite                                                                                                   | Status   | Obs. |
|--------|-----------------------------------------------------------------------|-----------------------------------------------|------------|-------------|----------------------------------------------------------------------------------------------------------------------|----------|------|
| E2E-01 | Prazo de devolução + exceção para carga perigosa                      | POL-001-A, POL-001-B                          | Alta       |             | Prazo de 7 dias úteis informado; carga perigosa encaminhada para Gestão de Riscos (ramal 4500)                      | Pendente |      |
| E2E-02 | SLA do cliente Gold para incidentes críticos                          | SLA-2024-C                                    | Alta       |             | Primeira resposta em até 30 min e resolução em até 4 h informados corretamente                                      | Pendente |      |
| E2E-03 | Cliente se diz Platinum — qual SLA aplicar?                           | SLA-2024-A, FAQ-15 (secundário)               | Alta       |             | Platinum informado como inexistente; tiers válidos listados com orientação para validação contratual                | Pendente |      |
| E2E-04 | Frete especial 1.200 kg no Sudeste — chamado novo pós-01/12/2023      | PROC-042v2-A, PROC-042v2-B, PROC-042v2-E     | Alta       |             | v2 aplicada: fator de peso (1.001–3.000 kg) e multiplicador Sudeste corretos; regra transitória encerrada explicitada | Pendente |      |

---

## Testes de Regressão

| ID     | Evento disparador                                 | Testes a executar                                                                 | Prioridade | Responsável | Critério de aceite                                                                                   | Status   | Obs. |
|--------|---------------------------------------------------|-----------------------------------------------------------------------------------|------------|-------------|------------------------------------------------------------------------------------------------------|----------|------|
| REG-01 | Atualização de conteúdo normativo (POL/PROC/SLA)  | Ingestão + Retrieval + Geração + E2E + Regressão focal no documento alterado      | Alta       |             | Sem regressão em perguntas críticas; diferenças documentadas                                         | Pendente |      |
| REG-02 | Revisão de instruções do assistente (prompt)      | Geração + Contexto + E2E + Regressão de segurança contra alucinação               | Alta       |             | Qualidade de geração mantida ou melhorada; sem novas alucinações                                     | Pendente |      |
| REG-03 | Mudança de chunk size / overlap                   | Ingestão + Retrieval + Contexto + E2E                                             | Média      |             | Precisão de retrieval igual ou superior ao baseline; chunks críticos ainda recuperados               | Pendente |      |
| REG-04 | Troca de modelo de embeddings                     | Retrieval + Contexto + E2E + comparação com baseline de ranking                   | Alta       |             | Ranking não piora vs baseline; top-k mantém chunks esperados nas perguntas críticas                  | Pendente |      |
| REG-05 | Troca de LLM                                      | Geração + Contexto + E2E + Regressão completa                                     | Alta       |             | Regressão completa aprovada; comportamento validado anterior preservado                              | Pendente |      |
