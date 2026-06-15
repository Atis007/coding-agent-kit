# claude-code-kit

Modular configuration files for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Two operational modes, stack-specific standards, and project scaffolding templates.

Ships with React, React Native, PHP, and Express. Designed to grow — add any stack by dropping in two files.

Works alongside the [Karpathy behavioral guidelines](https://github.com/multica-ai/andrej-karpathy-skills) plugin.

## What This Is

A kit of files you assemble per project to control how Claude Code works with your codebase. Not a plugin — a scaffolding system you copy from and customize.

**Mode 1 — Advisor:** You write code. Claude reads it, answers questions, doesn't touch files.

**Mode 2 — Agentic Build:** You lead. Claude implements against spec files that define the project, architecture, and coding standards.

## Structure

```
├── advisor-CLAUDE.md              ← Mode 1: drop in as CLAUDE.md
├── build-CLAUDE.md                ← Mode 2: drop in as CLAUDE.md
├── templates/                     ← Universal, fill per project
│   ├── project-overview.md
│   ├── progress-tracker.md
│   ├── ai-workflow-rules.md
│   └── ui-context.md
├── standards/                     ← Stack-specific, reusable as-is
│   ├── code-standards-typescript.md
│   ├── code-standards-react.md
│   ├── code-standards-rn.md
│   ├── code-standards-php.md
│   └── code-standards-express.md
└── architecture/                  ← Stack starters, pick + customize
    ├── architecture-react.md
    ├── architecture-rn.md
    ├── architecture-php.md
    └── architecture-express.md
```

**Templates** are language-agnostic — same files for every project, filled in with your specifics.

**Standards and architecture** are stack-specific — pick the ones matching your project, or create new ones for any stack.

## Usage

### Mode 1 — Advisor

Copy `advisor-CLAUDE.md` to your project root as `CLAUDE.md`. Done.

### Mode 2 — Agentic Build

1. Copy `build-CLAUDE.md` to your project root as `CLAUDE.md`
2. Create a `context/` folder in your project
3. Copy templates from `templates/` into `context/`
4. Pick your architecture starter, copy to `context/architecture.md`
5. Pick your code standards, copy to `context/`
6. Edit file references in `CLAUDE.md` to match your stack
7. Fill in the template placeholders

### Example Stack Combos

**React + Tailwind (web frontend)**
```
context/
├── project-overview.md
├── architecture.md              ← from architecture-react.md
├── ui-context.md
├── code-standards-typescript.md
├── code-standards-react.md
├── ai-workflow-rules.md
└── progress-tracker.md
```

**React Native + Expo (mobile)**
```
context/
├── project-overview.md
├── architecture.md              ← from architecture-rn.md
├── ui-context.md
├── code-standards-typescript.md
├── code-standards-rn.md
├── ai-workflow-rules.md
└── progress-tracker.md
```

**PHP REST API (backend, no framework)**
```
context/
├── project-overview.md
├── architecture.md              ← from architecture-php.md
├── code-standards-php.md
├── ai-workflow-rules.md
└── progress-tracker.md
```

**Express.js + TypeScript (backend)**
```
context/
├── project-overview.md
├── architecture.md              ← from architecture-express.md
├── code-standards-typescript.md
├── code-standards-express.md
├── ai-workflow-rules.md
└── progress-tracker.md
```

**Full-stack:** Combine frontend and backend files. Use one `architecture.md` covering both layers, or split into two and reference both in `CLAUDE.md`.

## Adding a New Stack

The kit grows with you. To add Go, Java, Python, Rust, or anything else:

1. Create `standards/code-standards-{stack}.md` — your coding conventions for that stack
2. Create `architecture/architecture-{stack}.md` — typical system structure and boundaries
3. Add the combo to this README

That's it. The templates, workflow rules, and CLAUDE.md files work with any stack unchanged.

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed
- [Karpathy guidelines](https://github.com/multica-ai/andrej-karpathy-skills) plugin installed (recommended, not required)

## License

MIT
