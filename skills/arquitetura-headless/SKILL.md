---
name: arquitetura-headless
description: Padroniza o consumo de Storefront APIs (GraphQL/REST) e o gerenciamento de estado global (carrinho) no Next.js.
---

# Padrão Arquitetural: Headless Commerce

## 1. Consumo de Storefront APIs (Shopify / Nuvemshop)
O agente deve isolar todas as chamadas de API de e-commerce em uma camada de serviço dedicada (`src/services/commerce/`).
- É proibido realizar chamadas diretas ao banco de dados (Prisma/Supabase) para buscar dados de catálogo ou preços caso o projeto dependa de uma API externa.
- O agente deve configurar cache agressivo (`next: { revalidate: 3600 }`) para consultas de catálogo (produtos, categorias) e `no-store` estrito para rotas de checkout, inventário e precificação dinâmica.

## 2. Gerenciamento de Estado Global (Carrinho)
O uso da Context API do React para gerenciamento do carrinho está bloqueado devido ao risco de estrangulamento por re-renderizações.
- O agente deve implementar bibliotecas baseadas em mutação atômica (como Zustand ou Jotai) para persistência do estado do carrinho no `localStorage`.
- O acesso ao armazenamento do navegador deve ocorrer estritamente no ciclo de vida do cliente (após a hidratação) para evitar erros de divergência de UI (Hydration Mismatch).

## 3. Webhooks e Sincronização
Webhooks disparados pela plataforma de e-commerce (ex: atualização de estoque, mudança de preço) devem atualizar as tags de cache do Next.js via `revalidateTag`. O agente deve projetar essa infraestrutura para invalidar páginas estáticas instantaneamente sem exigir rebuild completo.
