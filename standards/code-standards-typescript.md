# Code Standards — TypeScript

Shared TypeScript conventions. Used alongside a stack-specific
standards file (React, RN, Express).

## Strict Mode

- `strict: true` in tsconfig — no exceptions
- No `// @ts-ignore` or `// @ts-expect-error` without a
  comment explaining why and a TODO to remove it

## Types

- No `any` — use `unknown` at system boundaries, then narrow
- Interfaces for object shapes that describe data (props, API
  responses, DB rows)
- Type aliases for unions, intersections, and computed types
- Discriminated unions for state with multiple shapes:
  ```ts
  type Result<T> =
    | { status: 'success'; data: T }
    | { status: 'error'; error: string }
  ```
- Export types from the file that defines them — do not
  re-declare the same shape in multiple files

## Boundaries

- Validate all external input at system boundaries (API
  responses, user input, URL params, storage reads)
- After validation, trust the type — do not re-check deep
  in the call stack
- Parse, don't validate: transform unknown data into typed
  data at the boundary, then pass typed data forward

## Naming

- `camelCase` for variables, functions, parameters
- `PascalCase` for types, interfaces, classes, components
- `UPPER_SNAKE_CASE` for true constants (config values,
  enum-like objects)
- Boolean variables: `is`, `has`, `should`, `can` prefix
- Functions that return boolean: same prefixes or verb form
  (`isValid`, `hasPermission`)
- Event handlers: `handle` prefix (`handlePress`, `handleSubmit`)
- No abbreviations except universally understood ones
  (`id`, `url`, `db`, `config`, `params`, `props`)

## Functions

- Prefer pure functions — same input, same output, no side effects
- One function, one job — if you need "and" to describe it, split
- Explicit return types on exported functions and functions
  longer than 5 lines
- Early returns over nested conditionals:
  ```ts
  // yes
  if (!user) return null;
  if (!user.active) return null;
  return user.name;

  // no
  if (user) {
    if (user.active) {
      return user.name;
    }
  }
  return null;
  ```

## Imports

- Group in this order, separated by blank lines:
  1. External packages
  2. Internal modules (absolute paths / aliases)
  3. Relative imports (parent, then sibling, then child)
  4. Types (if using `import type`)
- No circular imports — if two files import each other,
  extract the shared dependency

## Error Handling

- Throw at the point of failure, catch at the point of recovery
- Do not catch errors just to log and re-throw
- Typed error classes or error codes for errors callers need
  to distinguish
- Never swallow errors silently (`catch (e) {}`)

## General

- No magic numbers — extract to named constants
- No dead code in commits — delete it, git has history
- Comments explain "why," not "what" — the code explains what
- TODO comments include context: `// TODO(attila): reason`
