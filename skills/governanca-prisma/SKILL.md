---
name: governanca-prisma
description: Prevenção de N+1, uso de paginação, transações e limites estritos para execução de migrations e deleção de dados.
---

# Padrão Arquitetural: Governança de Banco de Dados, Prisma ORM e Supabase

Esta skill dita as regras de interação com o banco de dados (Prisma Client / Supabase CLI) para evitar degradação de performance, perda acidental de dados e alucinações estruturais.

## 1. Limitações de Execução e Mutações de Schema (Regra de Ouro)
O agente tem permissão para manipular a estrutura do banco através do terminal, porém sob regras estritas de segurança:
- **Geração de Migrations:** É terminantemente proibido executar `pnpm dlx prisma db push`, `prisma migrate dev` ou `supabase db push` sem que a nova modelagem (no `schema.prisma` ou no arquivo de migration) tenha sido explicada e aprovada pelo operador humano no chat.
- **Operações Destrutivas:** Comandos como `prisma migrate reset`, `prisma db drop` ou qualquer alteração de schema que remova tabelas/colunas existentes exigem confirmação humana explícita. O agente DEVE parar a execução e perguntar: *"Deseja que eu proceda com a deleção dos dados?"*.
- **Ambiente Isolado (Seeds):** Mutações massivas de dados via scripts automatizados (Seeds) devem ser restritas a no máximo 10 registros de teste, a menos que especificado de outra forma.

## 2. Proibição de Consultas Abertas e Problema N+1
- A execução de métodos como `findMany` sem a cláusula `select` explícita é uma falha crítica. O agente deve extrair apenas as colunas necessárias para a renderização da interface.
- Loops que executam consultas ao banco de dados iterativamente estão estritamente proibidos. O carregamento de relações deve ocorrer em uma única transação utilizando a cláusula `include` de forma controlada.

## 3. Paginação Obrigatória de Coleções
- Qualquer endpoint ou Server Action projetado para retornar listas de registros (como históricos de clientes ou logs) deve implementar paginação nativa (Cursor-based ou Offset-based via `take` e `skip`). O retorno de tabelas inteiras para o frontend está bloqueado.

## 4. Validação de Transações de Alta Concorrência
- Operações de mutação que dependem do estado anterior do banco de dados (como validação de limites de estoque, assinaturas ou dedução de saldo) devem ser obrigatoriamente encapsuladas em transações iterativas (`$transaction` no Prisma ou RPCs no Supabase) para prevenir condições de corrida (*race conditions*).
