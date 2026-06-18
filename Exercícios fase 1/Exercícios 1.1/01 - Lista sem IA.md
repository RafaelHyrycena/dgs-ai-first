## Pergunta 1

 Pergunta: "Existem condições especiais para o prazo de entrega do frete especial?"

Comportamento esperado: 
A IA deve identificar as condições especiais relacionadas ao frete especial com base na documentação revisada, organizando cada regra de forma clara e indicando a qual situação ela se aplica. A resposta deve deixar explícito que, na versão mais recente do procedimento, o prazo de entrega do frete especial é o prazo padrão da rota  + 3 dias úteis  adicionais para manuseio e roteirização de carga pesada. Se citar outras condições especiais do frete especial, a IA deve separá-las do prazo de entrega para evitar ambiguidade.

Comportamento indesejado: 
A IA mistura regras diferentes em uma única explicação, sem deixar claro a qual condição cada prazo ou restrição se refere, o que pode gerar interpretações incorretas para o atendente. Também é indesejado usar informações da versão antiga sem sinalizar que existe uma versão revisada mais recente.

## Pergunta 2

 Pergunta: "Qual é a política para carga que chegou danificada?"

Comportamento esperado: 
A IA deve informar que, para cargas danificadas em trânsito, o cliente precisa registrar a ocorrência em até  48 horas  após o recebimento, com fotos e, se possível, laudo. Depois disso, a NovaTech deve investigar o caso e, se for comprovada responsabilidade da empresa, o reembolso será integral. A resposta também deve indicar claramente que essa informação está no  FAQ, item 38 .

Comportamento indesejado: 
A IA não informa nada, apresenta regras que não se referem a cargas danificadas ou afirma algo que não esteja presente no FAQ. Também é inadequado tratar essa informação como se viesse de uma política formal, sem mencionar que a fonte disponível está no FAQ.

## Pergunta 3

Pergunta: "Qual é a política de devolução parcial?"

Comportamento esperado: 
A IA deve informar que, quando a entrega envolver múltiplos volumes, o cliente pode devolver volumes individuais. Também deve explicar que cada volume devolvido segue o mesmo procedimento da devolução padrão e que o cálculo do reembolso é proporcional ao peso ou valor do volume devolvido, conforme o CT-e.

Comportamento indesejado: 
A IA confunde a regra de devolução parcial, inventa dados sobre o processo de devolução ou deixa de explicar o procedimento e a forma de cálculo do reembolso.

## Pergunta 4

Pergunta: "Como é feito o cálculo do frete especial?"

Comportamento esperado: 
A IA deve validar os documentos e responder com base no arquivo revisado  PROC-042-v2 , sem misturar regras antigas com as novas. A resposta deve informar que o cálculo do frete especial segue a fórmula  Valor do frete = Valor base × Multiplicador regional × Fator de peso , utilizando os multiplicadores e fatores de peso mais recentes disponibilizados pela NovaTech.

Comportamento indesejado: 
A IA confunde qual documento deve usar, mistura regras das duas versões e apresenta dados combinados do documento antigo com o revisado, comprometendo a precisão da resposta ao cliente.