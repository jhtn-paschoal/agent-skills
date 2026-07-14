---
name: deploy-vercel-edge
description: Estratégias de cache (ISR) e restrições de bibliotecas incompatíveis com ambiente Edge.
---

# Padrão Arquitetural: Otimização de Deploy e Edge Computing

Esta skill governa as regras de infraestrutura para implantações focadas em máxima performance e redução do Custo Por Aquisição (CPA) através da diminuição da latência.

1. Caching e Revalidação (ISR):
- Páginas que dependem de dados remotos, mas não exigem tempo real absoluto, devem implementar Incremental Static Regeneration (ISR). O agente deve configurar a opção `revalidate` nos `fetch` requests para balancear atualização de conteúdo e tempo de carregamento estático.
- Renderização no lado do servidor (SSR / `force-dynamic`) deve ser utilizada como último recurso, exclusivamente para rotas que exigem dados do usuário logado na sessão atual.

2. Restrições de Execução Edge:
- O uso de APIs nativas do Node.js (como `fs`, `path`, `crypto` ou `child_process`) dentro de Middleware ou Edge Route Handlers é proibido. O agente deve assegurar que o código executado nestas camadas utiliza os padrões Web API suportados pelo Vercel Edge Runtime.

3. Roteamento de Domínios Customizados:
- Aplicações projetadas para suportar roteamento multi-tenant ou links customizados de franquias devem ter as regras de reescrita (rewrites) definidas unicamente no arquivo `next.config.mjs` ou no `middleware.ts`, minimizando o impacto no tempo de resolução da requisição.