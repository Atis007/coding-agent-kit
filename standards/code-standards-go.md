# Code Standards — Go

Go backend/systems conventions.

## General

- `gofmt` on every file — non-negotiable, no style debates
- `go vet` and `staticcheck` pass with no warnings
- Go 1.21+ (or current stable) — use the version in `go.mod`
- Modules for dependency management (`go.mod` / `go.sum`)

## Naming

- `PascalCase` for exported identifiers
- `camelCase` for unexported identifiers
- Short, clear names — `r` not `reader` in a 3-line scope,
  `userService` not `us` in a wide scope
- Packages: short, lowercase, singular (`user`, `auth`, `db`)
  — no `utils`, `helpers`, `common` (put things where they belong)
- Interfaces: verb or role name (`Reader`, `Validator`,
  `UserStore`) — not `IReader`
- No getters/setters prefix: `user.Name()` not `user.GetName()`

## Error Handling

- Return errors, don't panic — `panic` is for truly
  unrecoverable situations only
- Check every error — no `_` for error returns unless
  you have a documented reason
- Wrap errors with context using `fmt.Errorf("doing X: %w", err)`
- Define sentinel errors or custom error types for errors
  callers need to distinguish:
  ```go
  var ErrNotFound = errors.New("not found")
  ```
- Errors are values — use `errors.Is` and `errors.As`, not
  string matching

## Functions

- Accept interfaces, return structs
- Small functions — if it scrolls past one screen, split it
- Early returns for error/edge cases:
  ```go
  if err != nil {
      return nil, fmt.Errorf("loading user: %w", err)
  }
  ```
- Context (`context.Context`) as the first parameter when
  the function does I/O or may be cancelled

## Structs and Interfaces

- Keep interfaces small — one or two methods is ideal
- Define interfaces where they're used (consumer side),
  not where they're implemented
- Use struct embedding for composition, not inheritance
- Zero values should be useful — design structs so the
  zero value is valid or obviously invalid

## Concurrency

- Don't start goroutines without a plan to stop them
- Use `context.Context` for cancellation
- Channels for communication, mutexes for shared state
- Don't share memory — pass data through channels when possible
- `sync.WaitGroup` for fan-out/fan-in patterns
- Never leak goroutines — every goroutine must have an exit path

## Testing

- Table-driven tests as the default pattern
- Test file next to the code: `user.go` → `user_test.go`
- `testify` for assertions if needed, but stdlib is fine
- Test names: `TestFunctionName_Scenario_ExpectedResult`
- Use `t.Helper()` in test helper functions
- Interfaces make mocking easy — no mocking frameworks needed

## Project Structure

- `cmd/` — entry points (one subdirectory per binary)
- `internal/` — private application code (not importable
  by other modules)
- `pkg/` — public library code (only if building a reusable
  library — most projects don't need this)
- Keep it flat until complexity demands folders — don't
  pre-organize into 10 empty directories

## Dependencies

- Standard library first — `net/http`, `encoding/json`,
  `database/sql` cover most needs
- Minimize external dependencies — every dependency is
  maintenance debt
- Vet dependencies before adding: is it maintained, does it
  have a reasonable API, could you write this in 50 lines

## API (if building an HTTP service)

- `net/http` or a thin router (`chi`, `gorilla/mux`) —
  document the choice in `architecture.md`
- Middleware for cross-cutting concerns (logging, auth, CORS)
- Consistent JSON response shapes
- Typed request/response structs — `json.Decoder` with
  `DisallowUnknownFields`
- Graceful shutdown with `context` and `os.Signal`
