# PLAN.md — Ruta a Senior Full-Stack (SaaS & AI)

> Ritmo sugerido: **1–1,5 h diarias entre semana + 3–4 h el fin de semana**.
> `[A MANO]` = lo escribo yo; Claude Code solo guía y revisa.
> Cada semana termina con: commit, ADR si aplica, y 3 preguntas de entrevista respondidas en `docs/entrevista-notas.md`.

---

## Semana 0 — Hoy / esta semana (urgente: respalda el perfil de Torre)
- [x] Crear repo público `taskflow-saas` en GitHub con este `CLAUDE.md` y `PLAN.md` (2026-10-20)
- [ ] Monorepo con pnpm workspaces + TypeScript estricto + ESLint + Prettier
- [ ] API Fastify mínima: `GET /health`
- [ ] `[A MANO]` **Dockerfile multi-stage** (deps → build → runtime con imagen slim, usuario no-root)
- [ ] `[A MANO]` **docker-compose**: api + postgres + redis, con healthchecks y volúmenes
- [ ] `[A MANO]` **GitHub Actions**: lint → typecheck → test → build de imagen Docker
- [ ] `.env.example` + `.gitignore` correctos (gestión de secretos)
- [ ] ADR 0001: "Monorepo con pnpm y arquitectura hexagonal"
- **Entrevista**: ¿Qué gana un Dockerfile multi-stage? ¿Por qué no correr como root? ¿Qué pasa en tu pipeline si falla un test?

## Semana 1 — Arquitectura hexagonal + TDD
- [ ] Leer/ver: Hexagonal Architecture (Alistair Cockburn), resumen de DDD (Bounded Contexts, Entities, Value Objects)
- [ ] `[A MANO]` Módulo `projects`: entidad `Project`, value object `ProjectName` (validación), regla "plan free máx. 3 proyectos"
- [ ] `[A MANO]` TDD estricto: test rojo → verde → refactor para cada regla de dominio
- [ ] Puerto `ProjectRepository` + adaptador en memoria (para tests)
- [ ] Caso de uso `CreateProject` y `ListProjects`
- [ ] Rutas Fastify con validación Zod (adaptador HTTP)
- [ ] ADR 0002: "Por qué hexagonal y no MVC tradicional"
- **Entrevista**: ¿Qué es un puerto y qué un adaptador? ¿Cómo pruebas un caso de uso sin base de datos? ¿Diferencia entre Clean, Onion y Hexagonal?

## Semana 2 — PostgreSQL avanzado + multi-tenancy
- [ ] Drizzle + migraciones
- [ ] Modelo: `tenants`, `users`, `memberships`, `projects`, `tasks`
- [ ] `[A MANO]` **Row Level Security**: políticas por `tenant_id` usando `SET app.tenant_id` por request
- [ ] Índices compuestos (`tenant_id, created_at`), entender `EXPLAIN ANALYZE`
- [ ] Transacciones: crear proyecto + tareas iniciales atómicamente
- [ ] Tests de integración con **Testcontainers** (Postgres real)
- [ ] ADR 0003: "Aislamiento de tenants: RLS vs schema-per-tenant vs DB-per-tenant"
- **Entrevista**: ¿Cómo garantizas que un tenant no vea datos de otro? ¿Qué es MVCC? ¿Cuándo un índice no se usa?

## Semana 3 — Auth (OAuth 2.0 / OIDC) + TypeScript avanzado
- [ ] Login con Google y GitHub (flujo Authorization Code + PKCE), emitir JWT propio
- [ ] Refresh tokens, expiración, rotación
- [ ] Middleware que resuelve usuario + tenant desde el token
- [ ] `[A MANO]` TypeScript: **branded types** (`TenantId`, `UserId`), genéricos, `infer`, tipo `Result<T, E>`
- [ ] Revisar OWASP Top 10 y aplicar: rate limit, helmet/headers, validación de inputs
- **Entrevista**: Diferencia OAuth 2.0 vs OIDC. ¿Por qué PKCE? ¿Dónde guardas el JWT en el front y por qué? (Menciona tu experiencia con SSO + Active Directory)

## Semana 4 — Redis, colas y eventos
- [ ] Caché cache-aside para `GET /projects/:id` con invalidación
- [ ] `[A MANO]` Lock distribuido simple con Redis (`SET NX PX`)
- [ ] BullMQ: cola `notifications` (job al asignar una tarea), reintentos y backoff
- [ ] Eventos de dominio (`TaskAssigned`) publicados desde el caso de uso
- [ ] ADR 0004: "Comunicación asíncrona con colas"
- **Entrevista**: cache-aside vs write-through. ¿Qué pasa si Redis se cae? ¿Qué es idempotencia en un consumer?

## Semana 5 — Ingeniería de agentes de IA (tu diferencial)
- [ ] Módulo `ai-assistant`: endpoint que resume un proyecto con LLM (streaming)
- [ ] "Tool calling": el agente puede crear tareas usando los casos de uso (no la DB directa)
- [ ] **Servidor MCP** propio que expone `list_projects`, `create_task` (con auth por tenant)
- [ ] RAG básico con **pgvector**: embeddings de descripciones de tareas → "buscar tareas similares"
- [ ] Límites: max tokens, timeouts, costo por tenant, logs (Langfuse opcional)
- [ ] Documentar **cuándo NO usar IA** en este producto (ADR 0005)
- [ ] Mostrar tus **skills de generación de tests** en `docs/ai-workflow.md`
- **Entrevista**: ¿Cómo evitas que el agente filtre datos de otro tenant? ¿Cómo evalúas la calidad del agente? ¿Context engineering vs prompt engineering?

## Semana 6–7 — Go + gRPC (fase 2)
- [ ] `[A MANO]` Tour of Go completo (go.dev/tour): structs, interfaces, goroutines, channels
- [ ] `[A MANO]` Servicio `go-notifier` que consume la cola o recibe llamadas
- [ ] Definir `notifier.proto` + generar código (buf o protoc)
- [ ] API Node llama a `go-notifier` por **gRPC**
- [ ] Dockerizar el servicio Go y sumarlo al compose + CI
- **Entrevista**: REST vs gRPC, ¿cuándo cada uno? ¿Qué es una goroutine y en qué se diferencia de un thread?

## Semana 8 — Cierre de portafolio
- [ ] Tests E2E básicos con Playwright (o Supertest) del flujo principal
- [ ] README profesional: diagrama de arquitectura, cómo correrlo, decisiones (link a ADRs)
- [ ] Deploy (Railway / Fly.io / AWS) con URL pública
- [ ] Actualizar Torre y LinkedIn con el proyecto **real** (Docker, CI, RLS, Go, gRPC, MCP)
- [ ] Video corto explicando la arquitectura (sirve también como contenido para tus redes)

---

## En paralelo (sin semana fija)
- [ ] **Presentar AWS Cloud Practitioner** (meta: antes de que acabe noviembre)
- [ ] Python: FastAPI + Pydantic mini-API (1 fin de semana) → quita el "no experience"
- [ ] GitLab: crear cuenta y replicar el pipeline en GitLab CI (1 tarde)
- [ ] **Inglés técnico**: 20 min diarios (explicar en voz alta lo que hiciste ese día)
