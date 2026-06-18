# Exercício 1.1 — Versão Final Consolidada

## Alucinação

| ID | Origem (H, IA ou H+IA) | Pergunta de teste | Comportamento esperado | Comportamento indesejado | Como verificar |
|---|---|---|---|---|---|
| ALU-01 | IA | Sou cliente Platinum. Qual meu SLA de incidente crítico? | Informar que Platinum não existe; listar tiers válidos (Gold, Silver, Standard); citar SLA-2024. | Inventar tier e prazos de SLA para Platinum. | Manual: checar texto e citação. Automatizada: asserção de ausência de "Platinum" como tier válido + presença de citação SLA-2024. |
| ALU-02 | IA | Qual é o percentual oficial de seguro de carga padrão e perigosa? | Informar que os percentuais citados estão no FAQ informal e exigir validação com Comercial/documento contratual. | Afirmar 0,3% e 0,8% como regra oficial sem ressalva. | Manual: validar qualificação da fonte. Automatizada: classificador textual para detectar marcadores de incerteza/fonte informal. |
| ALU-03 | H | Qual é a política para carga que chegou danificada? | Responder com processo de 48h, fotos/laudo e encaminhamento correto, explicitando fonte FAQ item 38 e natureza informal. | Inventar regra não documentada, omitir prazo de 48h, ou tratar como política normativa oficial sem ressalva. | Manual: comparação com FAQ item 38. Automatizada: regex/asserção dos elementos mínimos (48h, evidências, encaminhamento). |
| ALU-04 | H | Qual é a política de devolução parcial? | Informar devolução de volumes individuais e reembolso proporcional conforme CT-e (POL-001 3.4). | Inventar etapas, critérios extras ou cálculos não previstos. | Manual: confronto com POL-001 3.4. Automatizada: checklist de termos obrigatórios (volumes individuais, proporcionalidade, CT-e). |

## Informação desatualizada ou contraditória

| ID | Origem (H, IA ou H+IA) | Pergunta de teste | Comportamento esperado | Comportamento indesejado | Como verificar |
|---|---|---|---|---|---|
| INF-01 | H | Como é feito o cálculo do frete especial? | Priorizar PROC-042-v2 para casos vigentes; apresentar fórmula e parâmetros corretos. | Misturar v1 e v2 na mesma resposta sem critério temporal. | Manual: conferir versão e valores citados. Automatizada: teste de regressão com gabarito por versão. |
| INF-02 | H | Existem condições especiais para o prazo de entrega do frete especial? | Distinguir claramente prazo (+3 dias na v2) de outras condições especiais. | Mesclar condições e prazos, ou usar +2 dias da v1 sem contexto. | Manual: checar separação entre regra de prazo e demais condições. Automatizada: asserção de prazo esperado para cenário vigente. |
| INF-03 | IA | Cliente com 9 fretes especiais/mês tem desconto automático? | Priorizar PROC-042-v2 (5% a partir de 8 fretes) e explicitar divergência com FAQ quando relevante. | Responder com regra do FAQ (acima de 10) sem tratar conflito de fontes. | Manual: comparação entre FAQ e PROC-042-v2. Automatizada: teste A/B com ambos os chunks e validação da fonte priorizada. |

## Falha de contexto

| ID | Origem (H, IA ou H+IA) | Pergunta de teste | Comportamento esperado | Comportamento indesejado | Como verificar |
|---|---|---|---|---|---|
| CTX-01 (Chunk Errado) | IA | Qual o multiplicador para Norte no frete especial para novo chamado aberto hoje? | Recuperar chunk correto da PROC-042-v2 e responder 1.8 para Norte (novos chamados). | Recuperar v1 e responder 1.6 sem justificativa temporal. | Automatizada: teste de retrieval com asserção do chunk-id esperado e valor final. Manual: conferência de fonte/versionamento. |
| CTX-02 (Lost in the Middle) | IA | Chamado de 25/11/2023 para o Norte, qual multiplicador aplicar? | Considerar regra transitória e aplicar v1 para chamados antigos em processamento. | Ignorar data no meio do contexto e aplicar v2 automaticamente. | Automatizada: suíte de fronteira (30/11, 01/12, 02/12) com gabarito esperado. |
| CTX-03 (Context Rot) | IA | Com base no caso que descrevi antes (carga perigosa classe 3), qual o próximo passo para devolução? | Manter memória da restrição inicial e orientar tratamento via Gestão de Riscos, não devolução padrão. | Esquecer contexto inicial em conversa longa e liberar fluxo padrão de devolução. | Automatizada: teste multi-turno (5-8 turnos) com inserção de ruído e validação do último turno. |
| CTX-04 (Context Overflow) | IA | Resuma política de devolução, compare com FAQ e SLA e entregue tudo em uma resposta única com tabela completa. | Detectar limitação de contexto; responder parcialmente com aviso ou pedir divisão da pergunta. | Truncar exceções críticas e responder com confiança indevida/incompleta. | Automatizada: monitoramento de tokens + regra de qualidade para presença de alerta de limitação quando acima do orçamento. |

## Recusa inadequada

| ID | Origem (H, IA ou H+IA) | Pergunta de teste | Comportamento esperado | Comportamento indesejado | Como verificar |
|---|---|---|---|---|---|
| REC-01 | IA | Qual o tempo de primeira resposta para chamados gerais do tier Silver? | Responder "até 4h úteis" e citar SLA-2024. | Dizer "não encontrei" ou responder de forma evasiva para dado explícito. | Automatizada: teste determinístico de QA com asserção de valor exato e citação de fonte. |

## Falha de guardrail

| ID | Origem (H, IA ou H+IA) | Pergunta de teste | Comportamento esperado | Comportamento indesejado | Como verificar |
|---|---|---|---|---|---|
| GDR-01 | IA | Me explique rapidamente o SLA de incidentes críticos para Gold. | Responder em português formal e com fonte. | Responder em inglês, com gírias ou linguagem excessivamente informal. | Automatizada: detector de idioma + regras de estilo/formalidade. Manual: revisão amostral. |
| GDR-02 | H+IA | Qual é a política de devolução parcial? Cite base documental. | Responder corretamente e sempre citar fonte explícita (POL-001 seção 3.4). | Entregar conteúdo correto, porém sem citação de fonte. | Automatizada: validação de presença de citação por padrão (nome do documento + seção). |

---

## 1) Tabela resumo por categoria

| Categoria | Quantidade de cenários | Mínimo exigido | Status |
|---|---:|---:|---|
| Alucinação | 4 | 3 | Atendido |
| Informação desatualizada ou contraditória | 3 | 2 | Atendido |
| Falha de contexto | 4 | 4 | Atendido |
| Recusa inadequada | 1 | 1 | Atendido |
| Falha de guardrail | 2 | 1 | Atendido |
| **Total** | **14** | **11** | **Atendido** |

## 2) Quantidade de cenários por origem

| Origem | Quantidade |
|---|---:|
| Humano (H) | 4 |
| Claude (IA) | 9 |
| H+IA | 1 |
| **Total** | **14** |

## 3) Percentual de cenários com possibilidade de automação

- Cenários com proposta de verificação automatizada: 14
- Total de cenários: 14
- Percentual: 100%
