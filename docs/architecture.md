# Architecture

## Stack
- Web: Next.js 16, React 19, Tailwind v4, TanStack Query
- Agent: TypeScript, LangGraph context pattern
- Contracts: Solidity 0.8.24 + Foundry
- Chain: Mantle (testnet/mainnet)

## Runtime Flow
1. User picks crop + capital
2. Agent maps crop -> strategy plan
3. Policy check validates risk and amount
4. Execution context prepares tx action
5. Decision hash logged for transparency
