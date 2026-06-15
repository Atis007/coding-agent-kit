# Code Standards — PHP

PHP 8.4+ REST API conventions. No framework — custom routing,
controllers, and services.

## General

- `declare(strict_types=1)` in every file
- Use PHP 8.x features: typed properties, union types, named
  arguments, enums, match, readonly properties
- PSR-12 coding style
- PSR-4 autoloading via Composer

## Architecture Pattern

- **Controllers** handle HTTP concerns: parse request, validate
  input, call service, return response. No business logic.
- **Services** contain business logic. Receive typed data, return
  results. No HTTP awareness — should work from CLI too.
- **Repositories** handle database access. Raw SQL or thin query
  builder — no ORM unless specified.
- One controller per resource, one service per domain concept.

## Request/Response

- Validate and parse all request input at the controller level
- Consistent JSON response shapes:
  ```php
  // Success
  { "data": { ... } }
  { "data": [ ... ], "meta": { "total": 42, "page": 1 } }

  // Error
  { "error": { "code": "VALIDATION_ERROR", "message": "..." } }
  ```
- Appropriate HTTP status codes — don't return 200 for errors
- `Content-Type: application/json` on all API responses

## Routing

- Centralized route definitions — one file or class mapping
  method + path to controller + action
- RESTful: GET /items, GET /items/{id}, POST /items,
  PUT /items/{id}, DELETE /items/{id}
- Route parameters validated before reaching the controller

## Database

- Prepared statements for every query — no string concatenation
  of user input into SQL
- Transactions for multi-statement writes
- Typed return values from repository methods
- Migrations: versioned SQL files, tracked in a migrations table

## Auth and Access

- Auth check in middleware or controller start — before logic
- Ownership checks in the service layer
- Never trust client-side identity claims

## Error Handling

- Exceptions for exceptional conditions, not control flow
- Custom exception classes: `NotFoundException`,
  `ValidationException`, `UnauthorizedException`
- Global exception handler → HTTP response conversion
- Never expose stack traces or SQL to the client

## Types and Structure

- Type hints on all parameters and return types
- Enums for fixed value sets (roles, statuses)
- Readonly classes or DTOs for data transfer between layers
- No magic arrays passed between layers — define the shape

## Naming

- `PascalCase` for classes
- `camelCase` for methods and variables
- `UPPER_SNAKE_CASE` for constants
- Controller methods: `index`, `show`, `store`, `update`, `destroy`
- Service methods: verb-noun (`createUser`, `validateTicket`)

## File Organization

- `src/Controllers/` — HTTP controllers
- `src/Services/` — business logic
- `src/Models/` or `src/Repositories/` — database access
- `src/Middleware/` — request middleware (auth, CORS)
- `src/Exceptions/` — custom exception classes
- `src/Routes/` — route definitions
- `src/Utils/` — shared utilities
- `config/` — configuration files
- `migrations/` — SQL migration files
- `public/` — entry point (index.php)

## Security

- CORS: explicit allowed origins, not `*` in production
- Rate limiting on auth endpoints
- Input sanitization for rendered data
- `password_hash()` with `PASSWORD_BCRYPT` or `PASSWORD_ARGON2ID`
- Secrets in environment variables, not in code
