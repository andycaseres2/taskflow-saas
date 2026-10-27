# TaskFlow

API SaaS **multi-tenant** para gestionar proyectos y tareas de equipos, con un **asistente de IA** que resume proyectos, sugiere prioridades y crea tareas a partir de texto.

> 🚧 **En desarrollo activo.** El proyecto se construye por etapas siguiendo [`PLAN.md`](./PLAN.md); cada etapa termina con commits pequeños, tests y decisiones de arquitectura documentadas.

![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?logo=typescript&logoColor=white)
![Fastify](https://img.shields.io/badge/Fastify-000000?logo=fastify&logoColor=white)
![Nx](https://img.shields.io/badge/Nx-143055?logo=nx&logoColor=white)
![pnpm](https://img.shields.io/badge/pnpm-F69220?logo=pnpm&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)
![Vitest](https://img.shields.io/badge/Vitest-6E9F18?logo=vitest&logoColor=white)

## Producto

- Cada **organización (tenant)** tiene usuarios, proyectos y tareas.
- Un usuario solo ve los datos de su organización (aislamiento con **Row Level Security** en PostgreSQL).
- Planes `free` / `pro` con límites, modelados como reglas de dominio testeables.
- Asistente de IA para resumir proyectos, priorizar y crear tareas desde lenguaje natural.

## Stack

| Capa | Tecnología |
|---|---|
| Lenguaje | TypeScript estricto |
| API | Fastify + Zod |
| Arquitectura | Hexagonal (puertos y adaptadores) + vertical slices |
| Base de datos | PostgreSQL 16 con RLS · Drizzle ORM |
| Caché y colas | Redis + BullMQ |
| Auth | OAuth 2.0 / OIDC (Google, GitHub) + JWT |
| Tests | Vitest · Testcontainers · Playwright |
| Infra | Docker multi-stage · docker-compose |
| CI/CD | GitHub Actions |
| Monorepo | Nx + pnpm workspaces |

## Estado

- [x] Monorepo con Nx y pnpm workspaces, TypeScript estricto, ESLint y Prettier
- [x] API Fastify con endpoint `GET /health`
- [x] Dockerfile multi-stage (imagen slim, usuario no-root)
- [ ] docker-compose con API, PostgreSQL y Redis
- [ ] Pipeline de CI en GitHub Actions
- [ ] Dominio multi-tenant con RLS
- [ ] Autenticación OAuth 2.0 / OIDC
- [ ] Asistente de IA

## Cómo correrlo

Requisitos: Node.js 20+, pnpm y Docker.

```bash
pnpm install
pnpm nx serve api     # desarrollo
pnpm nx test api      # tests
pnpm nx build api     # build de producción

# o con Docker
docker build -t taskflow-api .
docker run -p 3000:3000 taskflow-api
```

---

Hecho por [Andy Caseres](https://andycaseres.vercel.app) · [LinkedIn](https://www.linkedin.com/in/andy-caseres/)
