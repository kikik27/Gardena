# Gardena Codex Instructions

## Design Direction
- Build modern web3 interface with clean premium feel.
- Theme: light base, white + soft green energy accents.
- Avoid flashy/alay effects. Motion subtle and classy.
- Keep UX simple for beginners.

## Product Context
- Gardena = AI DeFi farming app on Mantle.
- Crop metaphors map to strategy risk tiers.
- Agent decisions must feel transparent.

## Code Constraints
- Keep component structure simple: `components/base`, `components/sections`, `components/ui`.
- Do NOT introduce atoms/molecules/organisms folder naming.
- Reuse existing components where possible.
- Use Next.js App Router patterns.

## Quality Gates
- Must pass: `pnpm --filter web build`
- Keep TypeScript strict-safe.
- Preserve existing Foundry/agent packages.
