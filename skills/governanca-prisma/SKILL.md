# Padrão Arquitetural: Governança de Banco de Dados e Prisma ORM

Esta skill dita as regras de interação com o banco de dados via Prisma Client para evitar estrangulamento de memória e degradação de tempo de resposta.

1. Proibição de Consultas Abertas e Problema N+1:
- A execução de métodos como `findMany` sem a cláusula `select` explícita é uma falha crítica. O agente deve extrair apenas as colunas necessárias para a renderização da interface ou processamento da lógica.
- Loops que executam consultas ao banco de dados iterativamente estão estritamente proibidos. O carregamento de relações deve ocorrer em uma única transação utilizando a cláusula `include` de forma controlada.

2. Paginação Obrigatória de Coleções:
- Qualquer endpoint ou Server Action projetado para retornar listas de registros (como históricos de clientes ou logs de estoque) deve implementar paginação nativa (Cursor-based ou Offset-based via `take` e `skip`).

3. Validação de Transações de Alta Concorrência:
- Operações de mutação que dependem do estado anterior do banco de dados (como validação de limites de volume de inventário ou dedução de saldo) devem ser obrigatoriamente encapsuladas em transações iterativas (`$transaction`) para prevenir condições de corrida (race conditions).