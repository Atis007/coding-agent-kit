# CLAUDE.md — Agentic Build Mode

Read the following files in order before implementing
or making any architectural decision:

1. `context/project-overview.md` — product definition,
   goals, features, and scope
2. `context/architecture.md` — system structure,
   boundaries, storage model, and invariants
3. `context/ui-context.md` — theme, colors, typography,
   and component conventions (remove if backend-only)
4. `context/code-standards-typescript.md` — shared
   TypeScript rules (remove if not using TS)
5. `context/code-standards-{stack}.md` — stack-specific
   conventions (replace {stack}: react | rn | php | express)
6. `context/ai-workflow-rules.md` — development workflow,
   scoping rules, and delivery approach
7. `context/progress-tracker.md` — current phase,
   completed work, open questions, and next steps

## After Reading

- Implement against the specs in these files — do not infer
  or invent behavior that isn't documented
- Update `context/progress-tracker.md` after each meaningful
  implementation change
- If implementation changes architecture, scope, or standards,
  update the relevant context file before continuing
- If a requirement is missing or ambiguous, add it as an open
  question in `progress-tracker.md` and ask before implementing

## Verification

Every implementation step must be verifiable. State what you're
building, what "done" looks like, and how to verify it before
you start writing code.
