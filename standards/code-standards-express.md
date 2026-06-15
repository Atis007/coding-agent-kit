# Code Standards — Express.js

Express.js + TypeScript backend conventions. Use with
`code-standards-typescript.md`.

## Architecture Pattern

- **Routes** define endpoints and wire middleware. No logic.
- **Controllers** handle HTTP: parse request, call service,
  format response. No business logic.
- **Services** contain business logic. Typed input/output.
  No `req`/`res` awareness.
- **Repositories** handle database queries. Services call
  repositories, not the DB client directly.
- One router per resource, one service per domain concept.

## Request/Response

- Validate input at controller or middleware level with a
  schema validator (Zod, Joi) — define in `architecture.md`
- Consistent JSON response shapes:
  ```ts
  // Success
  { data: { ... } }
  { data: [...], meta: { total: 42, page: 1 } }

  // Error
  { error: { code: 'VALIDATION_ERROR', message: '...' } }
  ```
- Appropriate HTTP status codes
- Type request handlers with explicit param, body, response types

## Middleware

- Auth middleware verifies identity and attaches user to `req`
- Validation middleware parses request body/params
- Error handling middleware is last — catches all thrown errors
- Order: CORS → body parser → auth → route validation →
  handler → error handler
- No business logic in middleware

## Error Handling

- Throw typed errors from services, catch in error middleware
- Custom error class with status code and error code:
  ```ts
  class AppError extends Error {
    constructor(
      public statusCode: number,
      public code: string,
      message: string
    ) { super(message); }
  }
  ```
- No stack traces to client in production
- Log errors with context (request ID, user ID, endpoint)

## Database

- DB client choice in `architecture.md` (Prisma, Knex, raw pg)
- Typed query results — no `any` from DB
- Parameterized queries — no string concatenation
- Transactions for multi-statement writes
- Migrations: versioned, forward-only

## Naming

- `camelCase` for variables, functions, handlers
- `PascalCase` for types, interfaces, classes
- Route paths: lowercase, kebab-case, plural nouns
  (`/api/users`, `/api/order-items`)
- Controllers: `getAll`, `getById`, `create`, `update`, `remove`
- Services: verb-noun (`createUser`, `validateOrder`)

## File Organization

- `src/routes/` — route definitions per resource
- `src/controllers/` — request handlers
- `src/services/` — business logic
- `src/repositories/` — database access
- `src/middleware/` — auth, validation, error handling
- `src/errors/` — custom error classes
- `src/types/` — shared type definitions
- `src/utils/` — pure utilities
- `src/config/` — env parsing, configuration
- `src/index.ts` — entry point

## Environment and Config

- All config from environment variables — not hardcoded
- Parse and validate env vars at startup — fail fast if missing
- No `.env` in git — use `.env.example`

## Security

- Helmet for security headers
- CORS: explicit origins in production
- Rate limiting on auth and public endpoints
- Input validation on every endpoint accepting data
- Auth check before any mutation
