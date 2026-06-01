# Agent Flow

`apps/agent/src/graph.ts` orchestrates starter flow.

## Nodes
- `plan.ts` — pick strategy from crop map
- `policy.ts` — guardrails (max tx, max risk)
- `log.ts` — decision hash helper

## Config
- `config/crops.ts` — crop to strategy mapping
- `config/contracts.ts` — contract address/env mapping

## Output
Agent returns:
- selected plan
- policy decision (allow/block)
- decision hash
