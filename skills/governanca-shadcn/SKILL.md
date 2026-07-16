---
name: governanca-shadcn
description: Padroniza a criação de interfaces utilizando exclusivamente o ecossistema Shadcn UI e Radix, bloqueando a recriação manual de primitivos visuais.
---

# Padrão Arquitetural: Componentes Visuais e Shadcn UI

## 1. Regra de Ouro (YAGNI + Reutilização)
É terminantemente proibido construir componentes primitivos (botões, modais, inputs, tabelas, menus de navegação) utilizando HTML nativo e Tailwind puro do zero. A aplicação utiliza o ecossistema **Shadcn UI** como base de design system.

## 2. Fluxo de Trabalho Obrigatório para UI
Antes de iniciar a codificação de qualquer tela, painel ou formulário, o agente deve executar o seguinte fluxo lógico:

1. **Verificação de Necessidade:** Quais primitivos visuais esta tela exige? (Ex: `card`, `button`, `input`, `dialog`, `toast`).
2. **Auditoria Local:** Verifique se esses componentes já estão instalados no diretório `components/ui/`. Se estiverem, importe-os e utilize-os imediatamente.
3. **Instalação via CLI:** Se o componente exigido não estiver na pasta local, o agente DEVE instalá-lo utilizando a CLI antes de prosseguir com o código da página:
   ```bash
   pnpm dlx shadcn@latest add <nome-do-componente>
