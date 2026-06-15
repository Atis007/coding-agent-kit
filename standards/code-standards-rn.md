# Code Standards — React Native (Expo)

React Native + Expo conventions. Use with `code-standards-typescript.md`.

## Components

- Functional components only
- One component per file, file name matches component name
- Props interface named `{Component}Props`
- Co-locate component-specific types, helpers, and constants —
  extract only when shared

## Component Structure

Order within a component:
1. Props destructuring
2. Hooks (state, Redux selectors, refs, effects)
3. Derived values and handlers
4. Early returns (loading, error, empty states)
5. Return JSX

## State Management

- Local state (`useState`) for UI-only state (modals, input
  values, animation triggers)
- Redux for application state that persists across screens or
  needs to survive navigation (user data, cached entities,
  app settings)
- Keep Redux slices focused — one slice per domain concept
- Selectors for reading Redux state — do not access store
  shape directly in components
- Dispatch actions from event handlers, not from effects
  when avoidable

## Local Database (expo-sqlite)

- All database access through a dedicated data layer —
  components never run SQL directly
- Typed query results — define interfaces for each table row
- Migrations: versioned, forward-only, run on app start
- Use transactions for multi-statement writes
- Async operations: handle loading and error states in the UI

## Navigation

- Expo Router for file-based routing or React Navigation —
  document the choice in `architecture.md`
- Type route params — no untyped `params.id` access
- Keep screen components thin — delegate logic to hooks or
  services, screen is a layout shell

## Styling

- `StyleSheet.create` for static styles — defined at the
  bottom of the component file
- Dynamic styles via style arrays:
  ```tsx
  <View style={[styles.card, isActive && styles.cardActive]} />
  ```
- No inline style objects for static values — they create
  new objects on every render
- Project design tokens (in `ui-context.md`) via a theme
  module — no hardcoded hex values
- Platform-specific styles via `Platform.select` when needed

## Performance

- `FlatList` for any list that can exceed ~20 items — never
  `ScrollView` with `.map()` for dynamic lists
- `keyExtractor` with stable, unique keys — not array index
- `getItemLayout` when items have fixed height
- Minimize re-renders in lists: keep item components pure,
  avoid passing new objects/functions as props every render
- Images: use appropriate resolution, `expo-image` when available
- Do not memoize by default — memoize when measured

## Hooks

- Custom hooks for reusable stateful logic
- Go in `hooks/` or co-located with the feature
- No `useEffect` for derived state — compute inline
- `useEffect` cleanup: always clean up subscriptions,
  listeners, and timers

## File Organization

- `app/` — route screens (Expo Router) or `screens/`
  (React Navigation)
- `components/` — shared components
- `components/ui/` — base UI primitives
- `features/` — feature-specific components by domain
- `hooks/` — shared custom hooks
- `store/` — Redux store, slices, selectors
- `db/` — database schema, migrations, data access layer
- `lib/` or `utils/` — pure utility functions
- `types/` — shared type definitions
- `constants/` — app-wide constants (colors, config, enums)

## Platform Considerations

- Test on both iOS and Android unless the project targets one
- Use `Platform.OS` checks sparingly — prefer cross-platform
  components
- Safe area: `SafeAreaView` or `useSafeAreaInsets` for
  edge-to-edge screens
- Keyboard: `KeyboardAvoidingView` with platform-specific
  behavior prop

## Error Handling

- Global error boundary at app root for crash recovery
- Per-screen or per-feature error boundaries for isolated failures
- User-facing error messages: clear, actionable, no stack traces
- Network errors: distinguish offline from server errors
