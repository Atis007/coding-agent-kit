# Code Standards — C

C systems programming conventions. C11 or later.

## General

- C11 standard minimum (`-std=c11`) — specify in `architecture.md`
- Compile with warnings on: `-Wall -Wextra -Werror` (treat
  warnings as errors)
- No undefined behavior — if you're not sure, it's UB
- Build system: Make or CMake — document in `architecture.md`

## Naming

- `snake_case` for functions, variables, typedefs, file names
- `UPPER_SNAKE_CASE` for macros and constants (`#define`, `enum` values)
- `PascalCase` or `snake_case_t` for struct/typedef types —
  pick one, be consistent
- Prefix public API with module name to avoid collisions:
  `user_create()`, `user_destroy()`, `db_connect()`
- No single-letter names except `i`, `j`, `k` for loop indices
  and `n` for count in short scopes

## Memory Management

- Every `malloc` has a matching `free` — no exceptions
- Check every allocation: `malloc` can return `NULL`
- Establish clear ownership: document who allocates and who frees
- Use the create/destroy pattern for structs with resources:
  ```c
  User *user_create(const char *name);
  void user_destroy(User *user);
  ```
- Initialize all variables at declaration — no uninitialized reads
- Set freed pointers to `NULL` to catch use-after-free
- Prefer stack allocation for small, short-lived data
- No `realloc` on the same pointer — use a temporary:
  ```c
  void *tmp = realloc(buf, new_size);
  if (!tmp) { /* handle error, buf is still valid */ }
  buf = tmp;
  ```

## Error Handling

- Return error codes from functions — `0` for success,
  negative values for errors
- Define error codes as an enum:
  ```c
  enum {
      ERR_OK = 0,
      ERR_NOMEM = -1,
      ERR_INVALID = -2,
      ERR_IO = -3,
  };
  ```
- Check every return value — no ignoring errors
- Use `errno` only for standard library calls
- Clean up resources on error paths (goto-cleanup pattern
  is acceptable in C):
  ```c
  int result = ERR_OK;
  char *buf = malloc(size);
  if (!buf) { result = ERR_NOMEM; goto cleanup; }
  // ... work ...
  cleanup:
      free(buf);
      return result;
  ```

## Functions

- Keep functions short — one screen, one purpose
- Parameters: inputs first, outputs last
- Const-correct: use `const` on pointers you don't modify
- Maximum ~5 parameters — if you need more, pass a struct
- Document preconditions and postconditions for public functions
- Use `static` for file-local functions — minimize public surface

## Structs and Data

- Opaque types for encapsulation: forward-declare in headers,
  define in the `.c` file
- Use `typedef` for struct types to avoid `struct` keyword everywhere
- Prefer fixed-width types for data with specific size
  requirements: `int32_t`, `uint8_t`
- Use `size_t` for sizes and counts, `ptrdiff_t` for pointer differences
- No bitfields unless interfacing with hardware

## Headers

- Every `.c` file has a corresponding `.h` file for its public API
- Include guards in every header:
  ```c
  #ifndef MODULE_NAME_H
  #define MODULE_NAME_H
  // ...
  #endif
  ```
- Minimal includes in headers — forward-declare where possible,
  include in the `.c` file
- Self-contained headers: a header must compile on its own

## Strings

- Null-terminated strings (`char *`) for compatibility
- Always track buffer sizes — never assume enough space
- Use `snprintf`, never `sprintf`
- Use `strncpy` or `strlcpy` where available, never `strcpy`
  with unbounded input
- For binary data, use explicit `size` parameter alongside
  the pointer

## File Organization

- `src/` — source files (`.c`)
- `include/` — public headers (`.h`)
- `tests/` — test files
- `build/` — build output (gitignored)
- `Makefile` or `CMakeLists.txt` at the root

## Security

- Validate all external input — buffer sizes, string lengths,
  numeric ranges
- No buffer overflows — bounds-check every array access
  with external data
- Use `calloc` over `malloc` when zero-initialization matters
- No format string vulnerabilities: never `printf(user_input)`,
  always `printf("%s", user_input)`
