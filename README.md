# Gardena

AI DeFi farming app on Mantle.

## Stack
- Web: Next.js 16, React 19, Tailwind v4
- Wallet: wagmi, viem, RainbowKit
- Agent: LangGraph, OpenAI
- Contracts: Solidity 0.8.24, Foundry
- Data: Supabase (planned)

## Start
```bash
pnpm install
pnpm dev
```

## Build
```bash
pnpm build
```

## Apps
- `apps/web` frontend
- `apps/agent` agent service
- `contracts` smart contracts

## Env (minimal)
```bash
# apps/web/.env.local
NEXT_PUBLIC_WALLETCONNECT_PROJECT_ID=
NEXT_PUBLIC_MANTLE_RPC_URL=

# apps/agent/.env
OPENAI_API_KEY=
MANTLE_RPC_URL=
MANTLE_CHAIN_ID=5000
MANTLE_NETWORK=mantle
AGENT_IDENTITY_ADDRESS=
DECISION_LOG_ADDRESS=
RISK_POLICY_ADDRESS=
PRIVATE_KEY=
```
