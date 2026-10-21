# CLAUDE.md — Proyecto portafolio: SaaS multi-tenant AI-first

## Quién soy y para qué es este repo

Soy Andy Caseres, Software Engineer (web, mobile y backend) que trabaja AI-first.
Este repo es mi **proyecto de portafolio** para postular a roles **Senior Full-Stack (SaaS & AI)**.
Objetivo doble:

1. **Aprender de verdad** cada tecnología (no solo que la IA la escriba).
2. Tener un repo público que pueda **defender línea por línea** en una entrevista técnica.

## Reglas para Claude Code (IMPORTANTE)

- **Modo tutor primero**: antes de implementar algo nuevo, explícame en 3–6 líneas el concepto y por qué se hace así. Luego implementamos.
- **Yo escribo, tú guías** en los temas marcados `[A MANO]` en `PLAN.md`: dame pistas, revisa mi código, no lo escribas por mí salvo que lo pida.
- En el resto, puedes implementar, pero **al final de cada tarea** dame:
  - Resumen de qué se hizo y por qué.
  - 2–3 preguntas tipo entrevista sobre lo que acabamos de hacer.
- **TDD**: para lógica de dominio, primero el test (rojo), luego el código (verde), luego refactor. No saltar este orden.
- **ADRs**: toda decisión de arquitectura relevante se documenta en `docs/adr/NNNN-titulo.md` (formato abajo).
- **Nunca** subir secretos. `.env` en `.gitignore`, siempre mantener `.env.example` actualizado.
- Commits pequeños con Conventional Commits (`feat:`, `fix:`, `test:`, `docs:`, `refactor:`, `chore:`).
- Explicaciones en **español**; código, nombres y commits en **inglés**.
- Si algo no lo sé o lo estoy diciendo mal, **corrígeme** sin rodeos.

## Producto (dominio)

**"TaskFlow"**: API SaaS multi-tenant para gestionar proyectos y tareas de equipos, con un **asistente IA** que resume proyectos, sugiere prioridades y crea tareas a partir de texto.

- Cada **organización (tenant)** tiene usuarios, proyectos y tareas.
- Un usuario solo ve datos de su tenant (aislamiento con **Postgres RLS**).
- Planes `free` / `pro` con límites (ej. nº de proyectos) → regla de dominio testeable.

## Stack objetivo

| Capa           | Tecnología                                                                                     |
| -------------- | ---------------------------------------------------------------------------------------------- |
| Lenguaje       | TypeScript estricto (`strict: true`, sin `any`)                                                |
| API            | Fastify + Zod (validación runtime)                                                             |
| Arquitectura   | Hexagonal (puertos y adaptadores) + Vertical Slices                                            |
| DB             | PostgreSQL 16 con Row Level Security; ORM: Drizzle                                             |
| Caché/colas    | Redis + BullMQ                                                                                 |
| Auth           | OAuth 2.0 / OIDC (Google, GitHub) + JWT                                                        |
| Tests          | Vitest (unit), Testcontainers (integración con Postgres/Redis reales), Playwright (E2E básico) |
| Infra local    | Docker multi-stage + docker-compose                                                            |
| CI/CD          | GitHub Actions (lint, typecheck, test, build imagen)                                           |
| IA             | Vercel AI SDK o SDK de Anthropic + servidor MCP propio                                         |
| Extra (fase 2) | Microservicio en **Go** comunicado por **gRPC**                                                |
| Monorepo       | pnpm workspaces (o Turborepo)                                                                  |

## Estructura de carpetas (hexagonal)

```
apps/
  api/
    src/
      modules/
        projects/
          domain/          # entidades, value objects, reglas de negocio (SIN dependencias externas)
          application/     # casos de uso (use cases), puertos (interfaces)
          infrastructure/  # adaptadores: repos Drizzle, http (rutas Fastify), colas
        tasks/
        tenants/
        ai-assistant/
      shared/              # errores, Result type, branded types, config
    test/
  go-notifier/             # fase 2: servicio Go + gRPC
packages/
  contracts/               # schemas Zod / .proto compartidos
docs/
  adr/
docker/
.github/workflows/
```

Regla de oro: `domain/` **no importa** nada de `infrastructure/` ni de librerías externas.

## Formato de ADR

```md
# NNNN — Título de la decisión

Fecha: AAAA-MM-DD · Estado: Propuesto | Aceptado | Reemplazado

## Contexto

¿Qué problema hay y qué restricciones?

## Decisión

Qué elegimos.

## Alternativas consideradas

Opción B, opción C y por qué no.

## Consecuencias

Lo bueno, lo malo, qué deuda dejamos.
```

## Comandos (se irán completando)

- `pnpm dev` — levantar API en local
- `docker compose up -d` — Postgres + Redis
- `pnpm test` — tests unitarios
- `pnpm test:int` — tests de integración (Testcontainers)
- `pnpm lint && pnpm typecheck`

## Progreso

Ver `PLAN.md`. Al terminar una tarea, marca el checkbox y anota la fecha.
