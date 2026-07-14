# Padrão Arquitetural: Governança de Versionamento (Conventional Commits)

Esta skill obriga o agente e o operador a manterem uma árvore de commits rastreável, semântica e automatizável, impedindo mensagens de versão ambíguas.

1. Estrutura Obrigatória:
- Todo commit deve seguir a sintaxe estrita: `tipo(escopo): descrição da alteração`.
- O uso de letras maiúsculas no início da descrição ou pontos finais no término da linha está proibido.

2. Tipologia Restrita (Tipos Permitidos):
- `feat`: Adição de nova funcionalidade.
- `fix`: Correção de um bug ou falha de sistema.
- `refactor`: Alteração de código que não corrige bugs nem adiciona funcionalidades (ex: renomeação de variáveis, extração de componentes).
- `perf`: Alteração focada exclusivamente em melhoria de desempenho (ex: implementação de Code Splitting, remoção de N+1 no Prisma).
- `chore`: Atualização de dependências, configurações de build ou modificações no ecossistema (ex: injeção de novas skills).
- `docs`: Modificações restritas a arquivos Markdown ou comentários de documentação.

3. Escopo e Verbo Imperativo:
- O `(escopo)` deve indicar o domínio ou componente afetado (ex: `auth`, `hero-section`, `webhook-n8n`).
- A descrição deve utilizar o tempo verbal imperativo, descrevendo o que o commit fará se aplicado. Exemplo correto: `feat(pricing): adiciona grid de planos e precos`. Exemplo incorreto: `adicionado planos`.

4. Limite e Granularidade:
- Commits monolíticos ("cria projeto inteiro") são falhas de governança. O agente deve agrupar alterações logicamente e sugerir commits granulares durante fluxos de refatoração extensos.