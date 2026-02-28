# 🦫 Capibara — Project Scope

## 1. Project Overview

Capibara is an opinionated, boundary-driven capability framework designed to
standardize structural evolution across frontend and backend systems.

Capibara bootstraps applications with:

- Feature-First architecture
- Versioned feature modules
- Capability-driven structural evolution
- Strict architectural boundaries enforced via lint
- Deterministic scaffolding
- AI-compatible conventions
- Integrated capability visibility tooling

Capibara supports:

- React Native (Expo-only)
- React
- Next.js

---

## 2. Installation & Bootstrap Model

Capibara must be initialized via CLI:

```bash
npx capibara init
```

The CLI must:

- Prompt platform selection (React Native, React, Next.js)
- Enforce Expo-only for React Native
- Validate latest Expo SDK
- Allow monolithic or monorepo setup
- Configure pnpm + Turborepo if monorepo
- Generate base feature-first structure
- Generate capability registry
- Configure lint rules
- Scaffold initial feature
- Enable capability viewer in dev mode

---

## 3. Supported Platforms

### 3.1 React Native (Expo-Only)

- Expo is mandatory.
- Only latest stable Expo SDK is supported.
- CLI must validate Expo version.
- No support for bare React Native.

---

### 3.2 React

- Feature-first structure enforced.
- No SSR assumptions.
- Capability negotiation compatible.

---

### 3.3 Next.js

- App Router–based structure.
- Feature-first inside `src/`.
- SSR compatible.
- API routes capability-aware.
- Monorepo compatible.

---

## 4. Project Structure Modes

### 4.1 Monolithic Mode

```plaintext
project/
  src/
    app/
    features/
    shared/
    capabilities/
  package.json
  tsconfig.json
  .eslintrc
```

---

### 4.2 Monorepo Mode (pnpm + Turborepo)

```plaintext
project/
  apps/
    mobile/
    web/
  packages/
    features/
    shared/
    capabilities/
    config/
  turbo.json
  pnpm-workspace.yaml
```

Monorepo mode must:

- Configure pnpm workspaces
- Configure Turbo pipelines
- Enforce cross-package boundaries
- Share lint rules
- Share TypeScript references

---

## 5. Feature-First Architecture

Example:

```plaintext
src/
  features/
    feed/
      contract.ts
      index.ts
      v1/
      v2/
  shared/
  capabilities/
```

Rules:

- No cross-feature imports.
- No cross-version imports.
- Version selection only at feature boundary.
- No internal conditionals for capabilities.

---

## 6. Capability System

Capabilities represent structural contracts.

Examples:

- `feed.pagination.cursor.v2`
- `pricing.price_object.v2`
- `sdui.hero.v3`

Capabilities:

- Must be declared explicitly.
- Must include metadata.
- Represent contract-level changes only.
- Must not represent internal refactors.

---

### 6.1 Capability Declaration (Reference)

```ts
declareCapability({
  name: "feed.pagination.cursor.v2",
  owner: "feed-team",
  introducedIn: "1.4.0",
  replaces: "feed.pagination.page.v1",
});
```

---

## 7. Capability Registry

Capibara must generate a deterministic capability registry.

Example:

```ts
// capabilities/registry.ts

export const capabilityRegistry = [
  "feed.pagination.cursor.v2",
  "auth.session.refresh.v1",
];

export const getCapabilityHash = () => {
  return hash(capabilityRegistry.sort().join("|"));
};
```

The registry must:

- Be statically discoverable
- Be deterministic
- Be used to compute capability hash
- Support metadata introspection

---

## 8. Runtime Negotiation

### Client

- Registers supported capabilities
- Computes capability hash
- Sends hash via header

Example:

```ts
fetch("/api/feed", {
  headers: {
    "x-capability-hash": getCapabilityHash(),
  },
});
```

---

### Backend

- Parses capability header
- Selects versioned handler
- Returns compatible response

Example:

```ts
if (req.hasCapability("feed.pagination.cursor.v2")) {
  return handleFeedV2(req, res);
}

return handleFeedV1(req, res);
```

No external runtime service dependency allowed.

---

# 9. Capability Viewer (Client + Backend)

Capibara must include built-in capability inspection tooling.

---

## 9.1 Client Capability Viewer

In development mode, the client must expose:

- Registered capabilities
- Active capability hash
- Capability metadata
- Version lineage (`replaces` graph)
- Feature-to-capability mapping

Viewer must:

- Be disabled in production builds
- Be optionally enabled via dev flag
- Be accessible via dev overlay or debug screen
- Provide structured JSON export

Example dev output:

```json
{
  "capabilities": [
    {
      "name": "feed.pagination.cursor.v2",
      "introducedIn": "1.4.0",
      "replaces": "feed.pagination.page.v1"
    }
  ],
  "hash": "a84f99d..."
}
```

---

## 9.2 Backend Capability Viewer

Backend must expose a protected dev endpoint:

```
GET /__capabilities
```

Returns:

- Supported backend capabilities
- Active handler versions
- Deprecation status
- Compatibility matrix

Example response:

```json
{
  "supportedCapabilities": [
    "feed.pagination.cursor.v2",
    "auth.session.refresh.v1"
  ],
  "deprecated": ["feed.pagination.page.v1"]
}
```

Must be:

- Disabled or protected in production
- Accessible in staging/dev
- Usable for debugging negotiation mismatches

---

## 10. Lint-Enforced Architecture

Capibara must include strict lint rules:

- No cross-feature imports
- No version cross-imports
- No capability checks inside internal modules
- Shared cannot import features
- No scattered feature flags
- Strict boundary enforcement

Violations must fail CI.

---

## 11. CLI Responsibilities

CLI must support:

```bash
npx capibara init
npx capibara create feature feed
npx capibara evolve feature feed --breaking
npx capibara create capability
npx capibara lint architecture
npx capibara capabilities view
```

CLI must:

- Validate structure
- Validate capability lifecycle
- Validate Expo version
- Validate monorepo configuration
- Generate scaffolds deterministically

---

## 12. AI Compatibility

Capibara must be AI-operable:

- Deterministic structure
- Machine-readable capability metadata
- Discoverable feature boundaries
- Static capability graph
- Exportable capability registry

AI must be able to:

- Generate features
- Evolve versions
- Add capabilities
- Suggest deprecations
- Detect architectural violations

---

## 13. Architectural Constraints

- No runtime SaaS dependency
- No performance overhead
- Strict enforcement
- Incremental adoption possible
- Platform-agnostic core logic
- Expo-only for mobile

---

## 14. Success Criteria

Capibara succeeds when:

- Breaking changes are isolated into version folders.
- Capabilities govern structural evolution.
- Capability negotiation is observable.
- Architectural violations are machine-detected.
- Capability drift is visible.
- AI can operate deterministically.
- Legacy removal becomes predictable.
- Client and backend capability states are introspectable.

---
