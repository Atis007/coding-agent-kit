# UI Context

## Theme

[Describe the visual language — e.g. Dark-only technical
workspace, or light minimal with accent colors.]

## Colors

All components must use these tokens — no hardcoded hex values.

| Role            | CSS Variable / Constant | Value    |
| --------------- | ----------------------- | -------- |
| Background      | `--bg-base`             | `#[hex]` |
| Surface         | `--bg-surface`          | `#[hex]` |
| Primary text    | `--text-primary`        | `#[hex]` |
| Muted text      | `--text-muted`          | `#[hex]` |
| Primary accent  | `--accent-primary`      | `#[hex]` |
| Border          | `--border-default`      | `#[hex]` |
| Error           | `--state-error`         | `#[hex]` |
| Success         | `--state-success`       | `#[hex]` |

For React Native: define these as a theme constants object
instead of CSS custom properties.

## Typography

| Role      | Font                          |
| --------- | ----------------------------- |
| UI text   | [e.g. System default / Inter] |
| Code/mono | [e.g. JetBrains Mono]         |

## Spacing and Radius

[Define your scale — e.g. 4px base unit, or Tailwind defaults.
Specify border radius for small UI, cards, modals.]

## Component Library

[Base components — e.g. shadcn/ui, custom primitives,
React Native Paper. How to add new ones.]

## Layout Patterns

- [Primary layout — e.g. Tab navigation with 4 tabs]
- [Screen structure — e.g. Header + scrollable content + fixed bottom action]
- [List patterns — e.g. Cards in a vertical list with pull-to-refresh]
- [Modal patterns — e.g. Bottom sheet for forms, alert dialog for confirmations]

## Icons

[Icon library and sizes — e.g. Lucide React, 16px inline,
20px buttons, 24px navigation.]
