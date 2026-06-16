###  Versão antiga:

  test('query endpoint works', async () => {
    const result = await handler({ body: '{"question": "test"}' });
    expect(result).toBeDefined();
  });


###  Nova versão:

describe('queryHandler', () => {
  it('should return an answer and source document when a valid question is provided', async () => {
    // Arrange
    const request = {
      body: JSON.stringify({
        question: 'What is the return deadline for damaged goods?'
      })
    };

    const expectedResponse = {
      answer: 'Damaged goods must be returned within 7 business days.',
      source_document: 'POL-001'
    };

    vi.spyOn(service, 'query').mockResolvedValue(expectedResponse);

    // Act
    const result = await handler(request);

    // Assert
    expect(result.statusCode).toBe(200);
    expect(result.body.answer).toContain('7 business days');
    expect(result.body.source_document).toBe('POL-001');
  });
});


### Melhorias aplicadas

| Melhoria                         | Explicação                                                                     |
| -------------------------------- | ------------------------------------------------------------------------------ |
| Uso de `describe` e `it`         | Segue o padrão de nomenclatura definido no AGENTS.md.                          |
| Nome descritivo                  | O teste descreve claramente o comportamento esperado.                          |
| Estrutura Arrange / Act / Assert | Facilita leitura e manutenção.                                                 |
| Dados representativos            | Utiliza uma pergunta relacionada ao domínio do sistema.                        |
| Assertions específicas           | Valida status, conteúdo da resposta e documento de origem.                     |
| Mocking explícito                | Elimina dependência de serviços externos e torna o teste determinístico.       |
| Validação do comportamento       | O teste verifica o resultado esperado, e não apenas a existência de um objeto. |
