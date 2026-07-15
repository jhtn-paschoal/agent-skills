---
name: otimizacao-performance
description: Otimiza a performance da aplicação (Next.js 15, Core Web Vitals) e bloqueia consultas N+1 (Prisma e Supabase).
---

# Padrão Arquitetural: Otimização de Performance Estrita

## Visão Geral
Otimizações sem medição são suposições. Suposições geram complexidade sem ganho de velocidade. O fluxo obrigatório é: Medir (Baseline) → Identificar o Gargalo → Corrigir → Verificar.

## 1. Core Web Vitals e Orçamentos (Performance Budgets)
Qualquer alteração de código que degrade essas métricas será rejeitada.

| Métrica | Bom | Precisa Melhorar | Ruim |
| :--- | :--- | :--- | :--- |
| **LCP (Largest Contentful Paint)** | ≤ 2.5s | ≤ 4.0s | > 4.0s |
| **INP (Interaction to Next Paint)** | ≤ 200ms | ≤ 500ms | > 500ms |
| **CLS (Cumulative Layout Shift)** | ≤ 0.1 | ≤ 0.25 | > 0.25 |

**Limites de Infraestrutura:**
- Bundle JavaScript Inicial: < 200KB (Gzipped).
- Tempo de Resposta da API (TTFB): < 200ms (p95).

## 2. Anti-Patterns de Frontend (Next.js App Router) e Correções

### Imagens Não Otimizadas (Impacto Direto no LCP e CLS)
**Proibido:** Utilizar tags HTML nativas (`<img>` ou `<picture>`) ou não definir `priority` na imagem "Hero" (acima da dobra).
**Correção:** Uso estrito de `next/image` com preenchimento de dimensões ou `fill`.

```tsx
// GOOD: Imagem Hero (Acima da dobra) - Previne LCP alto
import Image from 'next/image';

<Image alt="Descrição" height="{600}" priority sizes="(max-width: 768px) 100vw, 1200px" src="/hero-banner.webp" width="{1200}"/>

// GOOD: Imagens secundárias (Abaixo da dobra)
<Image alt="Descrição" height="{400}" loading="lazy" src="/content.webp" width="{800}"/>
```

### Bundle Size e Hydration (Impacto Direto no INP)
**Proibido:** Usar `React.lazy` em SPAs legadas ou enviar bibliotecas pesadas de gráficos e mapas no bundle inicial do servidor.
**Correção:** Utilizar `next/dynamic` para Client Components não essenciais.

```tsx
// GOOD: Adia a hidratação e o download do JS até que seja necessário
import dynamic from 'next/dynamic';

const HeavyChart = dynamic(() => import('@/components/HeavyChart'), {
  ssr: false,
  loading: () => <SkeletonChart/>
});
```

## 3. Anti-Patterns de Backend e Banco de Dados

### Consultas N+1 (Prisma ORM e Supabase Client)
**Proibido:** Iterar sobre arrays para realizar requisições individuais ao banco de dados.

```typescript
// BAD (Prisma): N+1 Queries
const users = await prisma.user.findMany();
for (const user of users) {
  user.posts = await prisma.post.findMany({ where: { userId: user.id } }); // Falha
}

// GOOD (Prisma): Transação Única via Join
const users = await prisma.user.findMany({
  include: { posts: true }
});

// GOOD (Supabase): Transação Única via Foreign Key
const { data: users } = await supabase
  .from('users')
  .select('*, posts(*)');
```

### Falta de Caching no Servidor (Next.js 15)
**Proibido:** Refazer consultas custosas repetidamente em páginas estáticas ou compartilhadas. Cabeçalhos Express (`res.set('Cache-Control')`) são inúteis aqui.
**Correção:** Utilizar a API estendida do `fetch` do Next.js ou o `unstable_cache` para chamadas a ORMs.

```typescript
// GOOD: Cache de dados de terceiros
const res = await fetch('[https://api.exemplo.com/dados](https://api.exemplo.com/dados)', {
  next: { revalidate: 3600 } // Cacheia por 1 hora
});

// GOOD: Cache de chamadas de ORM (Prisma/Supabase) em Server Components
import { unstable_cache } from 'next/cache';

const getCachedConfig = unstable_cache(
  async () => {
    return await prisma.config.findFirst();
  },
  ['global-config'],
  { revalidate: 3600 }
);
```

## 4. Red Flags Arquiteturais (Ação Exigida)
1. Ausência de paginação em listagens (`take` / `skip` no Prisma, `range()` no Supabase).
2. Retorno completo de objetos de banco de dados para o client-side (Causa vazamento de memória e falha de segurança).
3. Uso de `use client` no layout raiz ou em componentes de encapsulamento que quebram o fluxo de Server Components.
