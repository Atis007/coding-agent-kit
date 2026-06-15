# Code Standards — Java

Java backend conventions. Framework-agnostic — specify
Spring Boot, Quarkus, or plain Java in `architecture.md`.

## General

- Java 17+ (LTS) or current LTS — specify in `architecture.md`
- Maven or Gradle for build — pick one, document in `architecture.md`
- Compiler warnings treated as errors in CI
- No wildcard imports (`import java.util.*`) — explicit imports only

## Naming

- `PascalCase` for classes and interfaces
- `camelCase` for methods, variables, parameters
- `UPPER_SNAKE_CASE` for constants (`static final`)
- Packages: lowercase, reverse domain (`com.project.module`)
- Interfaces: noun or adjective (`Serializable`, `UserRepository`)
  — no `I` prefix
- Implementations: descriptive name (`JpaUserRepository`,
  `InMemoryCache`) — not `UserRepositoryImpl`
- Boolean methods: `is`, `has`, `can` prefix

## Architecture Pattern

- **Controllers** handle HTTP — parse request, validate input,
  call service, return response. No business logic.
- **Services** contain business logic. Receive typed data,
  return typed results. No HTTP awareness.
- **Repositories** handle data access. Services call repositories,
  not the DB directly.
- One controller per resource, one service per domain concept.
- DTOs for data crossing layer boundaries — entities don't
  leak into API responses.

## Types and Structure

- Use `record` for immutable data carriers (DTOs, value objects)
- Use `sealed` interfaces/classes for closed type hierarchies
- Use `enum` for fixed value sets — with behavior when appropriate
- Use `Optional` for return types that may be absent — never
  for parameters or fields
- Prefer composition over inheritance
- Keep classes focused — single responsibility

## Error Handling

- Checked exceptions for recoverable conditions the caller
  must handle
- Unchecked exceptions (`RuntimeException` subclasses) for
  programming errors and unrecoverable failures
- Custom exception classes per domain error:
  ```java
  public class ResourceNotFoundException extends RuntimeException {
      public ResourceNotFoundException(String resource, String id) {
          super(resource + " not found: " + id);
      }
  }
  ```
- Global exception handler that maps exceptions to HTTP responses
- Never catch `Exception` or `Throwable` broadly — catch specific types
- Never swallow exceptions (`catch (e) {}`)
- Log at the catch site with context, not at every rethrow

## Null Safety

- Minimize null usage — use `Optional`, empty collections, or
  sentinel values instead
- `@Nullable` / `@NonNull` annotations on public API boundaries
- Fail fast on unexpected null — `Objects.requireNonNull` at
  method entry when a parameter must not be null
- Never return null from a collection-returning method — return
  empty collection

## Testing

- JUnit 5 for tests
- Test names: `methodName_scenario_expectedResult`
- `@ParameterizedTest` for table-driven test patterns
- Mockito for mocking dependencies — mock interfaces, not classes
- Test the behavior, not the implementation
- One assertion concept per test (multiple asserts are fine if
  they verify the same behavior)

## Dependencies

- Minimize dependencies — every dependency is maintenance and
  security surface
- Use the framework's built-in features before adding libraries
- Pin dependency versions — no dynamic version ranges in production

## File Organization

- One public class per file, file name matches class name
- Package by feature, not by layer (when the project is large enough):
  ```
  com.project.user/
  ├── UserController.java
  ├── UserService.java
  ├── UserRepository.java
  └── UserDto.java
  ```
- For smaller projects, package by layer is fine:
  ```
  controllers/
  services/
  repositories/
  dto/
  ```
- Document the choice in `architecture.md`

## API (if building an HTTP service)

- Consistent JSON response shapes
- Proper HTTP status codes
- Input validation at the controller level (Bean Validation
  annotations or explicit checks)
- Auth check before any mutation
- Pagination for list endpoints
