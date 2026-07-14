# Padrão Arquitetural: API Node.js (TypeScript + Prisma)

1. Topologia de Diretórios (Camadas Estritas):
- A arquitetura MVC monolítica está proibida. O design deve seguir separação por responsabilidade (Controller-Service-Repository).
- Camada de Roteamento (`src/routes/`): Define endpoints e injeta dependências. Nenhuma regra de negócio deve existir aqui.
- Camada de Serviço (`src/services/`): Contém estritamente as regras de negócio, regras de locação de infraestrutura e validações.
- Camada de Dados (`src/repositories/` ou chamadas diretas ao Prisma): Isolamento das consultas ao banco de dados.

2. Manipulação de Erros e Tipagem:
- O uso do tipo `any` causará falha de auditoria. Todos os retornos de API e payloads de webhooks externos devem possuir interfaces TypeScript explícitas.
- O tratamento de erros deve ocorrer em um middleware global (`src/middlewares/errorHandler.ts`). O vazamento de stack traces para a resposta HTTP é uma falha crítica de segurança.

3. Integrações Externas e Automação:
- Rotas destinadas a receber webhooks de plataformas de automação devem implementar validação de payload estrita e retornar status HTTP 200 imediatamente, delegando o processamento cruzado de dados para processos assíncronos ou filas, evitando timeout na plataforma de origem.
