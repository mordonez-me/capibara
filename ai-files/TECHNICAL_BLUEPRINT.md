# 🦫 Capibara — Technical Blueprint (Reference Level)

> This document defines the high-level technical components of Capibara. It
> serves as orientation material for detailed OpenSpec documents. Implementation
> details may evolve during formal specification.

---

# 1. Architectural Components Overview

Capibara is composed of five primary technical subsystems:

1. CLI Core
2. Feature & Version Module System
3. Capability System
4. Negotiation Middleware
5. Capability Viewer (Client + Backend)
6. Monorepo Generator
7. Architecture Enforcement Layer

Each subsystem is described below at reference level.

---

# 2. CLI Core

The CLI is the primary interaction surface.

It is responsible for:

- Bootstrapping projects
- Enforcing architectural rules
- Managing features
- Managing versions
- Managing capabilities
- Validating structural integrity

---

## 2.1 Initialization Command

```bash
npx capibara init
```

Interactive prompts:

- Platform:
  - React Native (Expo)
  - React
  - Next.js

- Structure mode:
  - Monolithic
  - Monorepo

- Package manager (auto-enforced):
  - pnpm (mandatory in monorepo mode)

---

## 2.2 Feature Lifecycle Commands

```bash
npx capibara create feature <name>
npx capibara evolve feature <name> --breaking
```

Expected output structure:

```plaintext
features/
  <name>/
    contract.ts
    index.ts
    v1/
      component.tsx
      hook.ts
      mapper.ts
      dto.ts
```

When evolving:

```plaintext
features/
  <name>/
    v2/
```

A new capability is optionally scaffolded automatically.

---

## 2.3 Capability Lifecycle Commands

```bash
npx capibara create capability <name>
npx capibara validate capabilities
npx capibara capabilities graph
```

Responsibilities:

- Enforce naming conventions
- Validate `replaces` graph
- Detect unused capabilities
- Detect undeclared usage
- Generate compatibility matrix

---

## 2.4 Architecture Validation Commands

```bash
npx capibara lint architecture
npx capibara doctor
```

Must detect:

- Cross-feature imports
- Cross-version imports
- Capability misuse
- Monorepo boundary violations
- Expo version mismatch

---

# 3. Feature & Version Module System

Capibara enforces versioned feature modules.

Example:

```plaintext
src/
  features/
    feed/
      contract.ts
      index.ts
      v1/
      v2/
```

Rules:

- `index.ts` is the only decision boundary.
- No internal conditional logic based on capability.
- Each version folder must be internally coherent.
- Version folders cannot import each other.

---

# 4. Capability System

Capabilities represent structural contracts.

They must:

- Be explicitly declared.
- Include metadata.
- Form a directed acyclic graph via `replaces`.

---

## 4.1 Declaration Reference

```ts
declareCapability({
  name: "feed.pagination.cursor.v2",
  owner: "feed-team",
  introducedIn: "1.4.0",
  replaces: "feed.pagination.page.v1",
});
```

---

## 4.2 Registry Reference

```ts
export const capabilityRegistry = [
  "feed.pagination.cursor.v2",
  "auth.session.refresh.v1",
];
```

---

## 4.3 Deterministic Hash

```ts
export const getCapabilityHash = () =>
  hash(capabilityRegistry.sort().join("|"));
```

The hashing strategy is implementation-defined and may evolve.

---

# 5. Negotiation Middleware

Negotiation must occur at system boundaries.

---

## 5.1 Client-Side Injection (Reference)

```ts
fetch("/api/feed", {
  headers: {
    "x-capability-hash": getCapabilityHash(),
  },
});
```

Implementation may use:

- Fetch interceptors
- Axios interceptors
- Framework-specific middleware

---

## 5.2 Backend Middleware (Reference)

```ts
function capabilityMiddleware(req, res, next) {
  const hash = req.headers["x-capability-hash"];
  req.capabilities = resolveCapabilities(hash);
  next();
}
```

Routing example:

```ts
if (req.hasCapability("feed.pagination.cursor.v2")) {
  return handleFeedV2(req, res);
}
return handleFeedV1(req, res);
```

No external network dependency allowed in request path.

---

# 6. Capability Viewer

The viewer provides runtime introspection in development environments.

---

## 6.1 Client Viewer

Accessible in development mode only.

Capabilities exposed:

- Registered capabilities
- Capability hash
- Replacement lineage
- Feature mapping

Example output:

```json
{
  "capabilities": [
    {
      "name": "feed.pagination.cursor.v2",
      "replaces": "feed.pagination.page.v1"
    }
  ],
  "hash": "a84f99d..."
}
```

Viewer access methods may include:

- Dev overlay
- Debug screen
- CLI command
- JSON export

---

## 6.2 Backend Viewer

Protected endpoint:

```
GET /__capabilities
```

Returns:

- Supported backend capabilities
- Deprecated capabilities
- Compatibility matrix

Must be disabled or protected in production.

---

# 7. Monorepo Generator

When selected, monorepo mode must:

- Enforce pnpm
- Configure Turborepo
- Create workspace layout
- Configure TS project references
- Share lint configuration
- Enforce cross-package boundaries

Reference structure:

```plaintext
project/
  apps/
    mobile/
    web/
  packages/
    features/
    shared/
    capabilities/
```

CLI must generate all configuration files.

---

# 8. Architecture Enforcement Layer

Lint-based enforcement:

- No cross-feature imports
- No cross-version imports
- No internal capability checks
- Shared cannot import features
- No feature flag drift
- Strict boundary validation

Must integrate with ESLint and CI.

---

# 9. Interrupt / Boundary Enforcement Concept

Capibara introduces a structural interrupt model:

- Capability checks are only allowed at feature boundary.
- Internal modules are pure.
- Version selection happens before execution.

Example boundary interrupt:

```ts
export const Feed = () =>
  hasCapability("feed.pagination.cursor.v2")
    ? <FeedV2 />
    : <FeedV1 />
```

This pattern is mandatory and machine-enforced.

---

# 10. React Native / Expo Enforcement

In React Native mode:

- Expo is mandatory.
- Only latest stable SDK supported.
- CLI validates version.
- Fails if outdated.

No bare React Native support.

---

# 11. React / Next Support

React:

- Feature-first enforced.
- No SSR assumptions.

Next:

- App Router support.
- API routes capability-aware.
- SSR compatibility preserved.

---

# 12. AI-Operable Structure

Capibara must expose:

- Deterministic folder patterns
- Capability metadata graph
- Version lineage
- Structured CLI output
- JSON exportable architecture map

AI must be able to:

- Discover features
- Discover versions
- Discover capabilities
- Propose migrations
- Detect violations

---

# 13. Reference-Only Notice

This document defines:

- Component boundaries
- Responsibilities
- High-level structure
- Intended CLI surface

Detailed behavior, validation logic, edge cases, and data formats will be
defined in OpenSpec.

This document is directional, not final specification.

---
