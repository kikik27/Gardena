# Gardena Docs

Minimal docs for quick onboarding.

## 1) What is Gardena
Gardena is AI DeFi farming app on Mantle.
User picks crop strategy. Agent executes with bounded autonomy.

## 2) Core Apps
- `apps/web` — user interface
- `apps/agent` — planning + policy + execution context
- `contracts` — onchain identity/log/policy (Foundry)

## 3) Quick Start
```bash
pnpm install
pnpm dev
```

Build checks:
```bash
pnpm --filter web build
pnpm --filter @gardena/agent build
pnpm --filter @gardena/contracts test
```

## 4) Read Next
- [Architecture](./architecture.md)
- [Agent Flow](./agent-flow.md)
- [Contracts](./contracts.md)
- [Environment](./environment.md)
