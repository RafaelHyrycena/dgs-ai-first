---
name: create-integration-test
description: >
  Use esta skill sempre que for criar, atualizar ou revisar testes de integração para endpoints de
  API, serviços ou fluxos RAG. Ative com frases como "escreva testes de integração para",
  "crie testes para este endpoint", "adicione cobertura de testes a", "teste este serviço" ou
  "atualize os testes de integração". Ative também quando o usuário compartilhar um arquivo de
  serviço ou handler e perguntar sobre testes, mesmo sem usar o termo "teste de integração".
  Sempre consulte esta skill antes de gerar qualquer arquivo de teste Vitest que envolva chamadas
  HTTP, serviços externos ou lógica de múltiplas camadas.
---

# Skill: create-integration-test

## Objetivo

Orientar agentes a gerar testes de integração determinísticos, legíveis e em conformidade com os
padrões de testes do projeto. Os testes produzidos com esta skill utilizam Vitest + MSW, seguem a
estrutura Arrange/Act/Assert e impõem assertions significativas — sem verificações vagas, sem
serviços reais, sem dependência de ordem de execução.

---

## Quando Utilizar

Use esta skill ao criar ou atualizar testes de integração para endpoints de API, serviços ou
fluxos RAG.

**Frases de ativação (o agente deve reconhecer qualquer uma destas):**
- "escreva testes de integração para X"
- "adicione cobertura de testes a este serviço"
- "teste este endpoint / handler / pipeline"
- "crie testes para o fluxo de consulta RAG"
- "atualize / corrija os testes de integração"

---

## Dependências Necessárias

### Skills Fundamentais
- `file-reading` — Leia os arquivos-fonte antes de gerar os testes; nunca infira assinaturas a partir da memória.

### Skills de Domínio
- Nenhuma obrigatória. Se o projeto possuir uma skill `testing-conventions` ou `api-contracts`, leia-a primeiro.

---

## Regras

**Estrutura**
- DEVE usar a nomenclatura `describe('NomeDoModulo', () => { it('deve [comportamento] quando [condição]') })`.
- DEVE estruturar o corpo de cada teste com Arrange / Act / Assert usando comentários inline.
- DEVE testar um único comportamento por bloco `it`. Separe múltiplos comportamentos em testes distintos.

**Assertions**
- DEVE fazer assertions sobre valores específicos, status codes, formatos de resposta ou efeitos colaterais.
- NÃO DEVE usar `toBeDefined()`, `toBeTruthy()` ou `not.toBeNull()` como única assertion.
- PREFERENCIALMENTE usar `toEqual`, `toMatchObject`, `toHaveBeenCalledWith` ou `toStrictEqual`.

**Mocking**
- DEVE usar handlers MSW para interceptar todas as chamadas HTTP externas.
- DEVE usar funções factory para gerar dados de teste — nunca objetos hardcoded inline.
- NÃO DEVE chamar serviços externos reais, bancos de dados ou APIs em nenhum teste.
- PREFERENCIALMENTE definir handlers MSW em um diretório compartilhado `mocks/handlers/` e importá-los por suite.

**Fixtures**
- DEVE extrair entradas reutilizáveis (queries, chunks, payloads) em arquivos de fixture.
- DEVE extrair saídas esperadas reutilizáveis (formatos de resposta) em arquivos de fixture.
- NÃO DEVE duplicar dados de fixture entre arquivos de teste.

**Isolamento**
- NÃO DEVE depender da ordem de execução dos testes. Cada `it` deve ser executável de forma independente.
- DEVE resetar os handlers MSW e qualquer estado compartilhado no `beforeEach` / `afterEach`.
- NÃO DEVE compartilhar estado mutável entre testes por meio de variáveis no escopo do módulo.

**Cobertura**
- DEVE cobrir: caminho feliz, erros de validação, falhas de serviços externos e casos de borda.
- DEVE atingir ≥ 80% de cobertura de branches no módulo sob teste.

---

## Template

```typescript
// tests/integration/[NomeDoModulo].integration.test.ts
import { describe, it, expect, beforeEach, afterEach } from 'vitest'
import { server } from '@/mocks/server'
import { http, HttpResponse } from 'msw'
import { [funcaoFactory] } from '@/tests/factories/[entidade].factory'
import { [fixture] } from '@/tests/fixtures/[dominio].fixtures'

describe('[NomeDoModulo]', () => {
  beforeEach(() => server.resetHandlers())

  describe('[método ou grupo de cenário]', () => {
    it('deve [comportamento esperado] quando [condição]', async () => {
      // Arrange
      const entrada = [funcaoFactory]({ /* sobrescritas */ })
      server.use(
        http.[metodo]('[url]', () => HttpResponse.json([fixture.respostaMock]))
      )

      // Act
      const resultado = await [moduloSobTeste].[metodo](entrada)

      // Assert
      expect(resultado).toMatchObject([fixture.saídaEsperada])
      expect(resultado.status).toBe(200)
    })
  })
})
```

---

## Exemplo Correto (DO)

```typescript
// tests/integration/ragQuery.integration.test.ts
import { describe, it, expect, beforeEach } from 'vitest'
import { server } from '@/mocks/server'
import { http, HttpResponse } from 'msw'
import { queryRagPipeline } from '@/services/ragQuery.service'
import { makeQueryInput } from '@/tests/factories/query.factory'
import { ragFixtures } from '@/tests/fixtures/rag.fixtures'

describe('RagQueryService', () => {
  beforeEach(() => server.resetHandlers())

  describe('queryPipeline', () => {
    it('deve retornar chunks ranqueados quando a API de embeddings responde com sucesso', async () => {
      // Arrange
      const entrada = makeQueryInput({ query: 'O que é recuperação aumentada por geração?' })
      server.use(
        http.post('https://api.embeddings.io/embed', () =>
          HttpResponse.json(ragFixtures.embeddingResponse)
        ),
        http.post('https://api.vectordb.io/search', () =>
          HttpResponse.json(ragFixtures.vectorSearchResponse)
        )
      )

      // Act
      const resultado = await queryRagPipeline(entrada)

      // Assert
      expect(resultado.chunks).toHaveLength(3)
      expect(resultado.chunks[0].score).toBeGreaterThan(0.8)
      expect(resultado.chunks[0]).toMatchObject(ragFixtures.expectedTopChunk)
    })

    it('deve lançar ServiceUnavailableError quando a API de embeddings retorna 503', async () => {
      // Arrange
      const entrada = makeQueryInput({ query: 'teste de fallback' })
      server.use(
        http.post('https://api.embeddings.io/embed', () =>
          HttpResponse.json({ error: 'Serviço Indisponível' }, { status: 503 })
        )
      )

      // Act & Assert
      await expect(queryRagPipeline(entrada)).rejects.toThrow('ServiceUnavailableError')
    })
  })
})
```

---

## Exemplo Incorreto (DON'T)

```typescript
// ❌ RUIM — viola múltiplos padrões
test("funciona", async () => {
  const resultado = await queryRagPipeline({ query: "teste" }) // string hardcoded, sem factory
  expect(resultado).toBeDefined()                              // assertion vaga
  expect(resultado).toBeTruthy()                              // não verifica nada útil
})

// ❌ Sem MSW — chama a API externa real
test("retorna chunks", async () => {
  const resultado = await queryRagPipeline({ query: "o que é RAG?" }) // chamada HTTP real
  expect(resultado).not.toBeNull()                                     // ainda vago
})

// ❌ Sem Arrange/Act/Assert, sem describe, testa dois comportamentos ao mesmo tempo
it("lida com sucesso e erro", async () => {
  const r1 = await queryRagPipeline({ query: "a" })
  expect(r1.chunks.length).toBe(3)
  const r2 = await queryRagPipeline({ query: "" })
  expect(r2).toBeDefined()
})
```

---

## Anti-Padrões

| Anti-Padrão | Por Que Falha |
|---|---|
| `expect(resultado).toBeDefined()` | Passa mesmo quando o resultado é `null`, `{}` ou `[]` — não verifica nada significativo. |
| `expect(resultado).toBeTruthy()` | Qualquer objeto não-vazio é truthy; nunca detecta formatos de dados incorretos. |
| Chamar serviços externos reais | Os testes ficam instáveis, lentos e acoplados à disponibilidade da rede. |
| Strings hardcoded como `"teste"` ou `"usuario1"` | Gera dados irreais; use factories com valores válidos para o domínio. |
| Nenhum handler MSW definido | Passa silenciosamente ou faz uma chamada real acidentalmente; HTTP deve sempre ser interceptado. |
| Múltiplos comportamentos em um único bloco `it` | A primeira falha oculta as subsequentes; viola a responsabilidade única dos testes. |
| Estado mutável compartilhado entre testes | Cria dependência de ordem de execução e causa falhas intermitentes no CI. |
| Dados de fixture duplicados inline | Divergências entre arquivos tornam o refactoring inseguro e as assertions não confiáveis. |
| Ausência de `beforeEach(() => server.resetHandlers())` | Handlers de um teste anterior vazam para o próximo, causando falsos positivos. |

---

## Critérios de Aceitação

Um teste gerado por esta skill está correto quando TODOS os itens abaixo forem verdadeiros:

- [ ] O nome do teste segue o padrão `deve [comportamento] quando [condição]`.
- [ ] O corpo possui comentários explícitos `// Arrange`, `// Act` e `// Assert`.
- [ ] Cada bloco `it` testa exatamente um comportamento.
- [ ] Todas as chamadas HTTP externas são interceptadas por handlers MSW.
- [ ] Os dados de teste são gerados por funções factory, não por literais inline.
- [ ] As assertions utilizam matchers específicos (`toEqual`, `toMatchObject`, `toHaveBeenCalledWith`).
- [ ] Nenhuma assertion usa apenas `toBeDefined()`, `toBeTruthy()` ou `not.toBeNull()`.
- [ ] `server.resetHandlers()` é chamado no `beforeEach`.
- [ ] O teste passa de forma isolada (executável com `vitest run --testPathPattern=<arquivo>`).
- [ ] A cobertura de branches do módulo sob teste é ≥ 80% após a execução da suite.
