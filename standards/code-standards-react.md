# Code Standards — React

React web frontend conventions. Use with `code-standards-typescript.md`.

## Components

- Functional components only — no class components
- One component per file, file name matches component name
  (`UserCard.tsx` exports `UserCard`)
- Co-locate component-specific types, helpers, and constants
  in the same file — extract only when shared
- Props interface named `{Component}Props`:
  ```tsx
  interface UserCardProps {
    user: User;
    onSelect: (id: string) => void;
  }
  ```

## Component Structure

Order within a component:
1. Props destructuring
2. Hooks (state, context, refs, effects)
3. Derived values and handlers
4. Early returns (loading, error, empty states)
5. Return JSX

## State

- Local state (`useState`) for UI-only state (open/closed,
  input values, hover)
- Lift state up only when a sibling needs it — not preemptively
- Context for cross-cutting concerns (theme, auth, locale) —
  not for all shared state
- External state management (Redux, Zustand) for server-synced
  or complex shared state — define when to use in `architecture.md`

## Hooks

- Custom hooks for reusable stateful logic — not for extracting
  a function that could be plain
- Custom hooks start with `use`, go in `hooks/` or co-located
  with the feature
- `useEffect` must have a clear purpose comment if the
  dependency array is non-obvious
- No `useEffect` for derived state — compute inline:
  ```tsx
  // yes
  const fullName = `${first} ${last}`;

  // no
  const [fullName, setFullName] = useState('');
  useEffect(() => setFullName(`${first} ${last}`), [first, last]);
  ```

## Performance

- Do not memoize by default — memoize when you measure a problem
- `React.memo` on components that re-render with same props
  due to parent re-renders, and the render is expensive
- `useMemo` for expensive computations — not simple derivations
- `useCallback` only when passing callbacks to memoized children
  or dependency arrays

## Styling (Tailwind)

- Tailwind utility classes for all styling — no inline style
  objects unless dynamic values require it
- Extract repeated class combinations into component
  abstractions, not string constants
- Project design tokens (in `ui-context.md`) via CSS custom
  properties — no hardcoded hex values
- Responsive: mobile-first (`sm:`, `md:`, `lg:`)
- Conditional classes with `clsx`/`cn`:
  ```tsx
  <div className={cn('p-4 rounded-lg', isActive && 'bg-blue-500')} />
  ```

## File Organization

- `components/` — shared/reusable components
- `components/ui/` — base UI primitives (button, input, modal)
- `features/` or page-level folders — feature-specific components
- `hooks/` — shared custom hooks
- `lib/` or `utils/` — pure utility functions
- `types/` — shared type definitions

## Forms

- Controlled inputs with explicit state
- Validate on submit, show errors after first submit attempt
- Disable submit during async operations
- Clear error state when the user edits the relevant field

## API Communication

- All API calls through a single client/wrapper — not raw
  `fetch` scattered across components
- Handle loading, error, and success states explicitly
- Type API responses at the boundary
