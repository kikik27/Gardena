# 05 — Agent Design

## Agent Responsibility
Agent translates user intent into constrained DeFi action.

## Node Graph (Starter)
- `plan.ts`: crop -> strategy plan
- `policy.ts`: risk/amount validation
- `log.ts`: decision hash generation
- `graph.ts`: orchestrates flow and output bundle

## Inputs
- user address
- crop selection
- amount
- risk policy

## Outputs
- selected plan metadata
- allow/block decision
- decision hash
