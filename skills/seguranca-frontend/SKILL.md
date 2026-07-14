# Padrão Arquitetural: Segurança e Validação de Fronteira

Esta skill determina a camada de proteção exigida para qualquer interação que realize mutação de dados ou aceite entrada do usuário.

1. Validação Estrita de Schemas (Zod):
- Todo formulário de frontend, payload de API e input de Server Action deve ser previamente validado por um schema tipado utilizando a biblioteca Zod. O parse deve ocorrer antes de qualquer regra de negócio ser instanciada.
- Respostas de falha de validação devem retornar status HTTP 400 acompanhadas da matriz de erros formatada, sem exposição do stack trace da aplicação.

2. Proteção de Cabeçalhos e CSP:
- O arquivo `next.config.mjs` deve conter um bloco obrigatório configurando Content Security Policy (CSP). O agente deve restringir o carregamento de scripts de terceiros, iframes e fontes exclusivamente para origens homologadas no escopo do projeto (ex: domínios do Google Analytics ou Pixel de rastreamento de campanhas).

3. Sanitização contra XSS e CSRF:
- É expressamente proibida a utilização de `dangerouslySetInnerHTML` sem a aplicação prévia de uma biblioteca de sanitização estrita (como DOMPurify). O agente deve auditar todas as Server Actions para garantir proteção contra falsificação de solicitações entre sites.