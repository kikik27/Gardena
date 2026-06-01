# Contracts

Contracts live in `contracts/contracts`.

## Modules
- `AgentIdentity.sol` — register agent identity + reputation
- `DecisionLog.sol` — log decision hash and outcomes
- `RiskPolicy.sol` — user policy + emergency pause

## Foundry Commands
```bash
cd contracts
forge build
forge test -vv
forge script script/Deploy.s.sol:DeployScript --rpc-url $MANTLE_RPC_URL --broadcast
```
