# Test Review Checklist

> Revisão rápida de teste automatizado — tempo estimado: < 2 minutos.
> Responda cada item com **SIM** ou **NÃO**. Substitua ⬜ por ✅ (SIM) ou ❌ (NÃO).

---

## Checklist de Revisão

### Nomenclatura

| Item   | Verificação                                                             | Status |
|--------|-------------------------------------------------------------------------|--------|
| REV-01 | O teste está dentro de um bloco `describe` nomeado com o módulo/unidade? | ⬜     |
| REV-02 | O bloco `it` utiliza o formato `deve [comportamento] quando [condição]`? | ⬜     |
| REV-03 | A descrição do `it` identifica o comportamento sem ambiguidade?          | ⬜     |

### Estrutura

| Item   | Verificação                                                                 | Status |
|--------|-----------------------------------------------------------------------------|--------|
| REV-04 | O corpo do teste possui os comentários `// Arrange`, `// Act` e `// Assert`? | ⬜     |
| REV-05 | A seção Arrange está separada da seção Act?                                  | ⬜     |
| REV-06 | O bloco `it` testa exatamente um comportamento?                              | ⬜     |

### Assertions ⚠️ crítico

| Item   | Verificação                                                                    | Status |
|--------|--------------------------------------------------------------------------------|--------|
| REV-07 | Todas as assertions validam valores, campos ou status codes específicos? ⚠️    | ⬜     |
| REV-08 | Nenhuma assertion usa `toBeDefined()` como única validação? ⚠️                 | ⬜     |
| REV-09 | Nenhuma assertion usa `toBeTruthy()` como única validação? ⚠️                  | ⬜     |
| REV-10 | Nenhuma assertion usa `not.toBeNull()` como única validação?                   | ⬜     |

### Mocking ⚠️ crítico

| Item   | Verificação                                                                   | Status |
|--------|-------------------------------------------------------------------------------|--------|
| REV-11 | Todas as chamadas HTTP externas são interceptadas por handlers MSW? ⚠️        | ⬜     |
| REV-12 | Nenhum serviço externo real é acessado durante a execução do teste? ⚠️        | ⬜     |
| REV-13 | Os handlers MSW são resetados no `beforeEach`?                                | ⬜     |

### Dados de Teste

| Item   | Verificação                                                                       | Status |
|--------|-----------------------------------------------------------------------------------|--------|
| REV-14 | Os dados de entrada são gerados por funções factory (não objetos literais inline)? | ⬜     |
| REV-15 | Os dados esperados são importados de arquivos de fixture?                          | ⬜     |
| REV-16 | Nenhum dado de teste usa strings genéricas como `"test"`, `"foo"` ou `"hello"`?   | ⬜     |

### Qualidade Geral

| Item   | Verificação                                                                 | Status |
|--------|-----------------------------------------------------------------------------|--------|
| REV-17 | O teste passa de forma isolada, sem depender de outros testes?              | ⬜     |
| REV-18 | O teste não depende da ordem de execução da suite?                          | ⬜     |
| REV-19 | O teste cobre o comportamento declarado no `it` de ponta a ponta?           | ⬜     |

---

## Resultado

| Situação                                  | Decisão       |
|-------------------------------------------|---------------|
| 100% dos itens marcados como ✅           | ✅ **Aprovado**   |
| Qualquer item ⚠️ marcado como ❌          | ❌ **Reprovado**  |
| Itens não-críticos marcados como ❌       | ⚠️ **Revisar**   |

**Itens críticos** (⚠️): REV-07, REV-08, REV-09, REV-11, REV-12.
A falha em qualquer um deles reprova o teste imediatamente, independentemente dos demais.
