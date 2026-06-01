# 04 — Technology A-Z

## A. Frontend
- **Next.js 16**: App Router, server/client boundaries, route handlers
- **React 19**: UI runtime
- **Tailwind CSS v4**: utility-first styling
- **shadcn/ui structure**: reusable component conventions via `components.json`
- **TanStack Query v5**: API state management, caching, retries
- **wagmi v2 + viem v2 + RainbowKit**: wallet connectivity and EVM interaction

## B. Agent Layer
- **TypeScript**: strict typed agent modules
- **LangGraph-compatible node structure**: plan/policy/log node pattern
- **Policy Engine (deterministic)**: max amount, max risk, active policy gating
- **Decision Hashing**: compact verifiable decision fingerprint

## C. Smart Contracts
- **Solidity 0.8.24**
- **Foundry** (`forge build`, `forge test`, `forge script`)
- **Modules**:
  - AgentIdentity
  - DecisionLog
  - RiskPolicy

## D. Data & Integrations
- **Supabase (planned)** for offchain decision detail, analytics cache, benchmark rows
- **Mantle RPC** for chain access (testnet/mainnet)

## E. Tooling & Monorepo
- **pnpm workspace** package management
- **Turborepo** task orchestration
- **TypeScript project boundaries** per app/package

## F. Security & Secrets
- `.env` isolation per app
- `.dev-wallet` local-only key storage
- gitignore hardening to prevent key leakage
