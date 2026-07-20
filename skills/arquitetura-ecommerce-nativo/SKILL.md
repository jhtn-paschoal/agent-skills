---
name: arquitetura-ecommerce-nativo
description: Desativa o motor Next.js/React e força o uso de linguagens de template (Liquid/Twig) respeitando a topologia da plataforma.
---

# Padrão Arquitetural: E-commerce Nativo (Templates)

## 1. Sobrescrita de Ecossistema (Desativação React)
Quando esta skill for acionada, o agente está proibido de propor o uso de Node.js, Next.js, React ou TypeScript. O ecossistema é restrito à linguagem de template exigida pela plataforma (ex: `.liquid` para Shopify ou `.tpl` para Nuvemshop).

## 2. Topologia de Arquivos Restrita
O agente não pode criar diretórios arbitrários. O desenvolvimento deve seguir estritamente o layout e a nomenclatura exigidos pelo tema da plataforma (ex: pastas de `layout/`, `templates/`, `sections/`, `snippets/`, `assets/`).

## 3. Otimização de Assets e LCP
- Estilos e scripts devem ser isolados por seção do e-commerce. É proibido o carregamento de um arquivo CSS/JS global monolítico que trave a thread principal.
- Imagens críticas (acima da dobra) devem utilizar os filtros nativos da plataforma de e-commerce que injetam atributos de `preload`, além de recortes de dimensão automáticos para preservar as métricas de tempo de carregamento.
