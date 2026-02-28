# Capibara

Capibara is an opinionated, boundary-driven capability framework for
standardizing structural evolution across frontend and backend systems.

It is designed to bootstrap applications around:

- Feature-first architecture
- Versioned feature modules
- Capability-driven evolution
- Strict architectural boundaries
- Deterministic scaffolding
- AI-compatible conventions
- Capability visibility tooling

## Supported Targets

Capibara is intended to support:

- React Native with Expo only
- React
- Next.js

## Core Idea

Capibara treats capabilities as explicit structural contracts. Instead of hiding
breaking changes behind internal branching, features evolve through versioned
modules and declared capabilities that can be negotiated at system boundaries.

This enables:

- Safer feature evolution
- Clear compatibility contracts
- Deterministic capability registries
- Runtime capability negotiation between clients and backends

## Planned Tooling

The project is centered on a CLI-first workflow:

```bash
npx capibara init
```

The intended CLI surface includes commands for:

- Project initialization
- Feature creation and evolution
- Capability creation and validation
- Architecture linting and diagnostics

## Project Direction

The technical blueprint currently defines these primary subsystems:

- CLI core
- Feature and version module system
- Capability system
- Negotiation middleware
- Capability viewer
- Monorepo generator
- Architecture enforcement layer

## Reference Docs

The detailed scope and architecture live in:

- [Project Scope](./ai-files/CONSTITUTION.md)
- [Technical Blueprint](./ai-files/TECHNICAL_BLUEPRINT.md)

Read those files before implementing new features or changing the project
direction.
