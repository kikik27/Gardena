# 03 — System Architecture

## Components
- `apps/web`: user interface, wallet actions, query layer
- `apps/agent`: strategy planning, policy checks, decision hash
- `contracts`: identity/log/policy modules onchain (Foundry)

## Logical Pipeline
1. Web receives user intent
2. Agent maps intent to strategy
3. Policy module validates constraints
4. Execution layer prepares tx action
5. Logging layer stores verifiable decision footprint

## Monorepo Layout
- `apps/web`
- `apps/agent`
- `contracts`
- `docs`
