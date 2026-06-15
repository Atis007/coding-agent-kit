# Code Standards — C#

C# / .NET conventions. Framework-agnostic — specify ASP.NET Core,
console app, or other in `architecture.md`.

## General

- .NET 8+ (LTS) or current LTS — specify in `architecture.md`
- Nullable reference types enabled (`<Nullable>enable</Nullable>`)
- Treat warnings as errors in CI
- Use latest C# language features available for your target

## Naming

- `PascalCase` for classes, interfaces, methods, properties,
  events, enums, namespaces, public fields
- `camelCase` for parameters, local variables
- `_camelCase` for private fields (underscore prefix)
- `UPPER_SNAKE_CASE` is NOT used — constants are `PascalCase`
- Interfaces: `I` prefix (`IUserRepository`, `ILogger`)
- Async methods: `Async` suffix (`GetUserAsync`, `SaveAsync`)
- Boolean properties/methods: `Is`, `Has`, `Can` prefix

## Architecture Pattern

- **Controllers** handle HTTP — parse request, call service,
  return response. No business logic.
- **Services** contain business logic. Typed input/output.
  No HTTP awareness.
- **Repositories** handle data access via EF Core or Dapper —
  document in `architecture.md`.
- DTOs for API boundaries — entities don't leak into responses.
- Dependency injection for all service dependencies —
  constructor injection, no service locator.

## Types and Structure

- `record` for immutable data (DTOs, value objects):
  ```csharp
  public record UserDto(string Name, string Email);
  ```
- `sealed` on classes that aren't designed for inheritance
- `enum` for fixed value sets
- Use `readonly struct` for small value types
- Prefer interfaces over abstract classes
- One class per file, file name matches class name

## Null Safety

- Nullable reference types enabled everywhere
- Use `?` for types that can be null, not-null by default
- No `null` returns from collection-returning methods — return
  empty collection
- Use null-conditional (`?.`) and null-coalescing (`??`) operators
- Pattern matching for null checks:
  ```csharp
  if (user is not null) { ... }
  ```
- `ArgumentNullException.ThrowIfNull(param)` at public API boundaries

## Error Handling

- Exceptions for exceptional conditions, not control flow
- Custom exception classes for domain errors:
  ```csharp
  public sealed class NotFoundException : Exception
  {
      public NotFoundException(string entity, object id)
          : base($"{entity} not found: {id}") { }
  }
  ```
- Global exception handler middleware for HTTP APIs
- Never catch `Exception` broadly unless at the top-level handler
- Never swallow exceptions
- Use `ExceptionFilter` or middleware, not try-catch in every controller

## Async

- `async`/`await` for all I/O operations — no `.Result` or `.Wait()`
- Return `Task` or `Task<T>` from async methods, not `void`
  (except event handlers)
- Use `CancellationToken` for operations that can be cancelled
- `ConfigureAwait(false)` in library code, not needed in ASP.NET Core

## Dependency Injection

- Register services in `Program.cs` or a dedicated extension method
- Constructor injection — no property injection, no service locator
- Use appropriate lifetimes: `Scoped` for request-bound,
  `Singleton` for stateless, `Transient` when cheap and stateless
- Depend on interfaces, not implementations

## Testing

- xUnit or NUnit — pick one, document in `architecture.md`
- Test names: `MethodName_Scenario_ExpectedResult`
- Moq or NSubstitute for mocking — mock interfaces, not classes
- `[Theory]` with `[InlineData]` for parameterized tests
- Test the behavior, not the implementation
- Use `WebApplicationFactory` for integration tests (ASP.NET Core)

## LINQ

- Use LINQ for collection operations — it's idiomatic C#
- Prefer method syntax over query syntax for simple operations
- Don't chain more than 3-4 operations — extract intermediate
  variables for readability
- Be aware of deferred execution — materialize with `ToList()`
  or `ToArray()` when you need a concrete result

## File Organization

- One public class per file
- Namespace matches folder structure
- Group by feature (preferred for larger projects) or by layer:
  ```
  Features/
  ├── Users/
  │   ├── UserController.cs
  │   ├── UserService.cs
  │   ├── IUserRepository.cs
  │   └── UserDto.cs
  ```
- `Program.cs` — entry point and service registration
- `appsettings.json` — configuration (no secrets here)

## Configuration

- Options pattern for typed configuration sections
- Secrets in environment variables or user secrets — never
  in `appsettings.json`
- Validate configuration at startup — fail fast on missing values

## API (if building an HTTP service)

- Consistent JSON response shapes (camelCase by default in
  ASP.NET Core)
- Proper HTTP status codes
- `[FromBody]`, `[FromRoute]`, `[FromQuery]` explicit on parameters
- FluentValidation or DataAnnotations for input validation
- Auth via `[Authorize]` attribute and policies
