# Padrão Arquitetural: Segurança de Backend e APIs

Esta skill impõe diretrizes estritas de blindagem para rotas de servidor (Node.js/Next.js Route Handlers) contra interceptação, vazamento de dados e abusos de tráfego.

1. Controle de Acesso e Autenticação (RBAC):
- O acesso a rotas privadas exige validação obrigatória de sessão ou token (JWT) antes de qualquer operação de I/O.
- A verificação de permissões deve ser baseada em escopos rigorosos (Role-Based Access Control). O agente deve rejeitar a execução de mutações se o nível de privilégio do usuário não for explicitamente validado na camada de roteamento.

2. Prevenção contra Vazamento de Dados (Data Exposure):
- É estritamente proibido retornar objetos de banco de dados inteiros para o cliente. O agente deve criar Data Transfer Objects (DTOs) ou utilizar seleções explícitas (ex: `select` no Prisma) para garantir que hashes de senha, tokens de redefinição e dados PII (Personally Identifiable Information) nunca sejam serializados na resposta HTTP.

3. Limitação de Taxa e Proteção de Recursos (Rate Limiting):
- Endpoints de autenticação, redefinição de senha e processamento de webhooks de plataformas terceiras (ex: n8n, ClickUp) devem incluir estratégias de Rate Limiting e limitação de payload (Body Size Limit) para mitigar ataques de força bruta e exaustão de memória (DDoS).

4. Validação de Variáveis de Ambiente:
- A aplicação não deve inicializar se houver ausência de chaves críticas. O agente deve impor a validação do `process.env` utilizando schemas estritos (Zod) durante o bootstrap do servidor.