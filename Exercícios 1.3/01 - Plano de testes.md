# Plano de Testes para Pipeline RAG - NovaTech

## 1. Testes de Ingestão

| Campo | Conteúdo |
|---|---|
| Objetivo | Validar que os documentos da base NovaTech (Anexo A) foram ingeridos corretamente, preservando versão, fonte e metadados mínimos para recuperação. |
| Pré-condições | 1) Índice RAG inicializado. 2) Ingestão configurada para arquivos markdown. 3) Coleção de documentos contendo: POL-001, PROC-042 v1, PROC-042-v2, SLA-2024 e FAQ-Atendimento. |
| Casos de teste | 1) Verificar presença dos 5 documentos esperados no índice. 2) Verificar separação entre PROC-042 v1 e PROC-042-v2 como documentos distintos. 3) Verificar preservação de campos de identificação (nome do documento, seção/versão quando disponível). 4) Verificar ausência de documentos fora do escopo dos Anexos A e B. |
| Critérios de aprovação | 1) 100% dos 5 documentos disponíveis para busca. 2) PROC-042 v1 e v2 recuperáveis separadamente. 3) Metadados mínimos presentes para rastreabilidade do chunk à origem. |
| Riscos identificados | 1) Sobrescrita indevida de versões (v1 e v2). 2) Perda de metadados de origem. 3) Inclusão acidental de base externa ao exercício. |

## 2. Testes de Retrieval

| Campo | Conteúdo |
|---|---|
| Objetivo | Validar a precisão de recuperação de documentos/chunks para perguntas reais do domínio NovaTech. |
| Pré-condições | 1) Pipeline de ingestão concluído. 2) Busca semântica habilitada. 3) Ranking configurado para top-k definido no ambiente de teste. |
| Casos de teste | Executar exatamente os 5 casos da tabela de retrieval desta seção e comparar documento/chunk recuperado com o esperado. |
| Critérios de aprovação | 1) Documento esperado aparece entre os primeiros resultados. 2) Chunk esperado é recuperado para a pergunta correspondente. 3) Resultado final não contradiz conteúdo oficial dos anexos. |
| Riscos identificados | 1) Recuperação de chunk de versão antiga quando há versão revisada. 2) Dependência excessiva de FAQ informal para resposta crítica. 3) Baixa cobertura em perguntas sem documentação formal. |

| Pergunta | Documento esperado | Chunk esperado | Resultado esperado |
|---|---|---|---|
| Qual o prazo de devolução? | POL-001 | POL-001-A | Informar devolução em até 7 dias úteis após recebimento, com contagem excluindo sábados, domingos e feriados nacionais. |
| Posso devolver carga perigosa? | POL-001 | POL-001-B | Informar que não é elegível no processo padrão e orientar tratamento individual com Gestão de Riscos (ramal 4500). |
| Qual o SLA do cliente Platinum? | SLA-2024 | SLA-2024-A | Informar que não existe tier Platinum; tiers válidos são Gold, Silver e Standard. |
| Frete para 600kg para Manaus? | PROC-042-v2 | PROC-042v2-B e PROC-042v2-A | Aplicar regra de frete especial (>500kg) com multiplicador Norte da v2 e fator de peso da faixa 500-1.000kg. |
| Frete para 300kg para Salvador? | Sem documento aplicável na base | Nenhum chunk plenamente aderente | Informar ausência de cobertura para frete padrão (<500kg) nos documentos disponíveis e não inventar valor/regra. |

## 3. Testes de Geração

| Campo | Conteúdo |
|---|---|
| Objetivo | Validar qualidade da resposta do LLM com base apenas no contexto recuperado do RAG, minimizando erros factuais e conflitos de fonte. |
| Pré-condições | 1) Retrieval executado e chunks disponíveis no prompt. 2) Prompt orienta uso exclusivo do contexto retornado. 3) Logs de entrada/saída habilitados para auditoria. |
| Casos de teste | Avaliar os riscos da tabela desta seção em respostas geradas para perguntas de devolução, SLA e frete especial. |
| Critérios de aprovação | 1) Resposta aderente aos chunks recuperados. 2) Inexistência de fatos não suportados. 3) Tratamento explícito de conflitos entre PROC-042 v1 e v2 quando ambos aparecem. |
| Riscos identificados | Alucinação, resposta incompleta, uso incorreto do contexto e conflito entre documentos. |

| Risco | Descrição | Exemplo | Forma de validação |
|---|---|---|---|
| Alucinação | Modelo responde com informação não presente nos chunks recuperados. | Informar SLA para tier Platinum com números inventados. | Conferir saída contra chunks recuperados; reprovar se houver dado sem suporte textual. |
| Resposta incompleta | Modelo omite parte essencial da regra presente no contexto. | Em devolução de carga perigosa, não citar encaminhamento para Gestão de Riscos (ramal 4500). | Checklist de elementos obrigatórios por pergunta; reprovar se item obrigatório ausente. |
| Uso incorreto do contexto | Modelo mistura regra de contexto parcialmente relevante com conclusão errada. | Usar FAQ-32 como regra formal para política operacional sem ressalva de informalidade. | Validar se a resposta distingue fonte formal (POL/PROC/SLA) de FAQ informal quando aplicável. |
| Conflito entre documentos | Modelo combina valores incompatíveis de versões diferentes sem resolver conflito. | Responder multiplicador Sudeste como 1.0 e 1.1 na mesma resposta sem critério temporal. | Verificar se resposta aplica v2 para novos chamados e explica exceção transitória quando pertinente. |

## 4. Testes de Contexto

| Campo | Conteúdo |
|---|---|
| Objetivo | Validar robustez do pipeline e do prompt frente a limitações de janela de contexto e ordenação dos chunks. |
| Pré-condições | 1) Configuração de tamanho máximo de contexto conhecida. 2) Registro de ordem/ranking dos chunks no prompt final. 3) Cenários com conversa curta e longa disponíveis. |
| Casos de teste | Executar checklist de contexto abaixo para o mesmo conjunto de perguntas usadas em retrieval e E2E. |
| Critérios de aprovação | 1) Respostas mantêm consistência factual mesmo com variação de posição dos chunks. 2) Itens críticos não são omitidos por limite de contexto. |
| Riscos identificados | Perda de informação relevante no meio do contexto, degradação em conversas longas e priorização incorreta de chunks. |

Checklist de verificações:

- [ ] Context Window Budget: confirmar que os chunks essenciais (ex.: POL-001-B, PROC-042v2-B, SLA-2024-A/B) cabem no orçamento de tokens junto com instruções do prompt.
- [ ] Lost in the Middle: verificar se chunk crítico no meio do contexto continua sendo utilizado corretamente na resposta.
- [ ] Context Rot: validar consistência após múltiplas iterações de perguntas relacionadas no mesmo atendimento.
- [ ] Conversas longas no Teams: testar manutenção de precisão após histórico extenso e múltiplas retomadas de assunto.
- [ ] Ordem dos chunks recuperados: validar impacto da ordem de ranking na resposta final, especialmente em conflitos PROC-042 v1 vs v2.

## 5. Testes End-to-End (E2E)

| Campo | Conteúdo |
|---|---|
| Objetivo | Validar o fluxo completo (pergunta do atendente -> retrieval -> geração) com critérios de QA funcionais. |
| Pré-condições | 1) Pipeline RAG operacional. 2) Conjunto de perguntas E2E definido. 3) Registro dos chunks efetivamente recuperados por execução. |
| Casos de teste | Executar os 4 cenários E2E abaixo e comparar a resposta do assistente com a resposta esperada. |
| Critérios de aprovação | 1) Chunks críticos recuperados em cada cenário. 2) Resposta final correta, objetiva e sem extrapolação indevida. |
| Riscos identificados | Falhas de encadeamento entre retrieval e geração, além de respostas com confiança alta para lacunas de documentação. |

| Pergunta | Chunks esperados | Resposta esperada |
|---|---|---|
| Qual o prazo de devolução e o que muda se for carga perigosa? | POL-001-A, POL-001-B | Informar prazo geral de 7 dias úteis e que carga perigosa não segue devolução padrão, devendo ser tratada pela Gestão de Riscos (ramal 4500). |
| Qual o SLA do cliente Gold para incidentes críticos? | SLA-2024-C | Informar primeira resposta em até 30 minutos e resolução em até 4 horas. |
| Cliente se diz Platinum. Qual SLA devo aplicar? | SLA-2024-A, FAQ-15 (secundário) | Informar que Platinum não existe; tiers válidos são Gold, Silver e Standard, com orientação para validação contratual. |
| Frete especial para 1.200kg no Sudeste em chamado novo após 01/12/2023 | PROC-042v2-A, PROC-042v2-B, PROC-042v2-E | Aplicar v2: fator de peso da faixa 1.001-3.000kg e multiplicador Sudeste da v2, observando regra transitória já encerrada para chamados novos. |

## 6. Testes de Regressão

| Campo | Conteúdo |
|---|---|
| Objetivo | Garantir que mudanças em documentos, prompt ou componentes do pipeline não degradem comportamento validado anteriormente. |
| Pré-condições | 1) Baseline de QA aprovado. 2) Suite mínima de retrieval, geração, contexto e E2E disponível. 3) Histórico de versão da configuração de pipeline. |
| Casos de teste | Reexecutar os testes definidos na matriz de regressão abaixo após cada mudança relevante. |
| Critérios de aprovação | 1) Sem regressão em perguntas críticas (devolução, SLA e frete especial). 2) Diferenças explicáveis e documentadas quando houver atualização de fonte. |
| Riscos identificados | Mudança silenciosa de comportamento, aumento de conflitos entre versões e piora de precisão após ajustes de modelo. |

| Mudança realizada | Testes que devem ser executados | Documento atualizado | Prompt alterado | Estratégia de chunking alterada | Modelo de embeddings alterado | Modelo LLM alterado |
|---|---|---|---|---|---|---|
| Atualização de conteúdo normativo (POL/PROC/SLA) | Ingestão + Retrieval + Geração + E2E + Regressão focal em perguntas do documento alterado | Sim | Não | Não | Não | Não |
| Revisão de instruções do assistente (prompt) | Geração + Contexto + E2E + Regressão de segurança contra alucinação | Não | Sim | Não | Não | Não |
| Mudança de chunk size/overlap | Ingestão + Retrieval + Contexto + E2E | Não | Não | Sim | Não | Não |
| Troca de embeddings | Retrieval + Contexto + E2E + comparação com baseline de ranking | Não | Não | Não | Sim | Não |
| Troca de LLM | Geração + Contexto + E2E + Regressão completa | Não | Não | Não | Não | Sim |

## 7. Template de Acompanhamento QA

| ID do teste | Categoria | Descrição | Prioridade | Responsável | Status | Resultado esperado | Resultado obtido | Evidências | Observações |
|---|---|---|---|---|---|---|---|---|---|
|  |  |  |  |  |  |  |  |  |  |
