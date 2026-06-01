# Mantle Garden — Tech Blueprint

**Track:** AI Trading & Strategy  
**Hackathon:** Mantle Turing Test  
**Date:** 31 May 2026  
**Version:** 1.0

---

## 1. Tech Stack

### Frontend
- **Next.js 15** (App Router)
- **TypeScript**
- **Tailwind CSS** + **shadcn/ui**
- **wagmi v2** — wallet connection + hooks
- **viem** — low-level EVM interaction
- **@rainbow-me/rainbowkit** or **Privy** — wallet UI (beginner-friendly)
- **framer-motion** — crop growth animations

### Agent Layer
- **LangGraph.js** — agent workflow orchestration
- **OpenAI API** (GPT-4o-mini) — strategy reasoning + explanation
- Custom **Policy Engine** (TypeScript) — deterministic guardrails

### Smart Contracts (Mantle)
- **Solidity ^0.8.24**
- **Hardhat** or **Foundry** — dev + test + deploy
- Deployment target: **Mantle Sepolia Testnet**

### Backend / Data
- **Supabase** — Postgres + Auth + Realtime
- **Mantle RPC** — testnet + mainnet endpoint

### Optional
- **ElizaOS** — AI farmer persona/chat layer
- **Coinbase AgentKit** — wallet action abstraction (if Mantle compatible)

---

## 2. Package List

### Frontend
```json
{
  "next": "^15",
  "react": "^19",
  "typescript": "^5",
  "tailwindcss": "^4",
  "@shadcn/ui": "latest",
  "wagmi": "^2",
  "viem": "^2",
  "@rainbow-me/rainbowkit": "^2",
  "framer-motion": "^11",
  "@tanstack/react-query": "^5",
  "lucide-react": "latest"
}
```

### Agent Layer
```json
{
  "@langchain/langgraph": "latest",
  "@langchain/core": "latest",
  "@langchain/openai": "latest",
  "openai": "^4"
}
```

### Smart Contracts
```json
{
  "hardhat": "^2",
  "@nomicfoundation/hardhat-toolbox": "^5",
  "@openzeppelin/contracts": "^5"
}
```

### Backend
```
supabase (hosted)
```

---

## 3. Smart Contract Interfaces (Minimal)

### AgentIdentity.sol
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract AgentIdentity {
    struct Agent {
        address owner;
        string name;
        string metadataURI; // strategy config, persona
        uint256 createdAt;
        bool active;
    }

    mapping(uint256 => Agent) public agents;
    mapping(uint256 => uint256) public reputationScore;
    uint256 public agentCount;

    event AgentRegistered(uint256 indexed agentId, address owner, string name);
    event ReputationUpdated(uint256 indexed agentId, uint256 score);

    function registerAgent(string calldata _name, string calldata _metadataURI) 
        external returns (uint256) 
    {
        uint256 id = ++agentCount;
        agents[id] = Agent(msg.sender, _name, _metadataURI, block.timestamp, true);
        emit AgentRegistered(id, msg.sender, _name);
        return id;
    }

    function updateReputation(uint256 _agentId, uint256 _score) external {
        reputationScore[_agentId] = _score;
        emit ReputationUpdated(_agentId, _score);
    }
}
```

### DecisionLog.sol
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract DecisionLog {
    struct Decision {
        uint256 agentId;
        bytes32 decisionHash;   // hash of off-chain detail
        bytes32 strategyId;
        address targetProtocol;
        uint256 amount;
        uint8 riskLevel;        // 1=low, 2=medium, 3=high
        uint8 outcome;          // 0=pending, 1=success, 2=partial, 3=failed
        uint256 timestamp;
    }

    mapping(uint256 => Decision) public decisions;
    uint256 public decisionCount;

    event DecisionLogged(
        uint256 indexed decisionId, 
        uint256 indexed agentId, 
        bytes32 decisionHash,
        uint8 riskLevel
    );
    event OutcomeUpdated(uint256 indexed decisionId, uint8 outcome);

    function logDecision(
        uint256 _agentId,
        bytes32 _decisionHash,
        bytes32 _strategyId,
        address _targetProtocol,
        uint256 _amount,
        uint8 _riskLevel
    ) external returns (uint256) {
        uint256 id = ++decisionCount;
        decisions[id] = Decision(
            _agentId, _decisionHash, _strategyId,
            _targetProtocol, _amount, _riskLevel,
            0, block.timestamp
        );
        emit DecisionLogged(id, _agentId, _decisionHash, _riskLevel);
        return id;
    }

    function updateOutcome(uint256 _decisionId, uint8 _outcome) external {
        decisions[_decisionId].outcome = _outcome;
        emit OutcomeUpdated(_decisionId, _outcome);
    }
}
```

### RiskPolicy.sol
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract RiskPolicy {
    struct Policy {
        address user;
        uint256 maxTxAmount;
        uint256 maxDailyLoss;
        uint8 maxRiskLevel;        // 1=low only, 2=medium max, 3=high allowed
        bool active;
        uint256 createdAt;
    }

    mapping(address => Policy) public userPolicies;

    event PolicySet(address indexed user, uint256 maxTxAmount, uint8 maxRiskLevel);
    event EmergencyPause(address indexed user);

    function setPolicy(
        uint256 _maxTxAmount,
        uint256 _maxDailyLoss,
        uint8 _maxRiskLevel
    ) external {
        userPolicies[msg.sender] = Policy(
            msg.sender, _maxTxAmount, _maxDailyLoss,
            _maxRiskLevel, true, block.timestamp
        );
        emit PolicySet(msg.sender, _maxTxAmount, _maxRiskLevel);
    }

    function emergencyPause() external {
        userPolicies[msg.sender].active = false;
        emit EmergencyPause(msg.sender);
    }

    function checkPolicy(
        address _user, 
        uint256 _amount, 
        uint8 _riskLevel
    ) external view returns (bool) {
        Policy memory p = userPolicies[_user];
        return p.active 
            && _amount <= p.maxTxAmount 
            && _riskLevel <= p.maxRiskLevel;
    }
}
```

---

## 4. Folder Structure

```
mantle-garden/
├── apps/
│   ├── web/                      # Next.js frontend
│   │   ├── app/
│   │   │   ├── page.tsx          # Landing / farm view
│   │   │   ├── garden/           # Main game UI
│   │   │   ├── diary/            # Agent diary / transparency
│   │   │   ├── policy/           # Risk settings
│   │   │   └── benchmark/        # AI vs Human dashboard
│   │   ├── components/
│   │   │   ├── farm/             # CropCard, PlotGrid, GrowthAnimation
│   │   │   ├── agent/            # AgentDiary, ReputationBadge
│   │   │   ├── wallet/           # ConnectButton, BalanceDisplay
│   │   │   └── policy/           # RiskSlider, EmergencyPause
│   │   ├── lib/
│   │   │   ├── agent/            # LangGraph client
│   │   │   ├── policy/           # Policy engine (client-side check)
│   │   │   ├── crops/            # Crop config (name, risk, strategy map)
│   │   │   └── mantled/         # Mantle-specific helpers
│   │   └── hooks/                # wagmi hooks, agent hooks
│   │
│   └── agent/                    # LangGraph agent service
│       ├── src/
│       │   ├── graph.ts          # Main workflow graph
│       │   ├── nodes/
│       │   │   ├── observe.ts    # Read market/state
│       │   │   ├── plan.ts       # Strategy selection
│       │   │   ├── policy.ts     # Deterministic risk check
│       │   │   ├── execute.ts    # viem tx execution
│       │   │   └── log.ts        # Record decision on-chain
│       │   ├── tools/
│       │   │   ├── mantledfi.ts  # Mantle DeFi protocol tools
│       │   │   └── simulate.ts   # Pre-execution simulation
│       │   └── config/
│       │       ├── crops.ts      # Crop → strategy mapping
│       │       └── prompts.ts    # LLM prompt templates
│       └── package.json
│
├── contracts/
│   ├── contracts/
│   │   ├── AgentIdentity.sol
│   │   ├── DecisionLog.sol
│   │   └── RiskPolicy.sol
│   ├── test/
│   ├── scripts/
│   │   └── deploy.ts
│   └── hardhat.config.ts
│
├── supabase/
│   ├── migrations/
│   └── seed.sql
│
├── docs/
│   ├── 2026-05-31-validation-plan.md
│   ├── 2026-05-31-tech-blueprint.md
│   └── 2026-05-31-48h-build-order.md
│
├── package.json
├── turbo.json                    # Turborepo monorepo
└── README.md
```

---

## 5. Supabase Schema (Minimal)

```sql
-- Agent decisions (off-chain detail, hashed on-chain)
CREATE TABLE agent_decisions (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    agent_id INTEGER NOT NULL,
    user_address TEXT NOT NULL,
    crop TEXT NOT NULL,              -- rice | corn | chili
    strategy_id TEXT NOT NULL,
    decision_text TEXT NOT NULL,      -- natural language reason
    decision_hash TEXT NOT NULL,      -- matches on-chain hash
    tx_hash TEXT,
    amount TEXT NOT NULL,
    risk_level SMALLINT NOT NULL,     -- 1=low, 2=medium, 3=high
    outcome SMALLINT DEFAULT 0,       -- 0=pending, 1=success, 2=partial, 3=failed
    pnl TEXT,                         -- profit/loss
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Agent reputation snapshot
CREATE TABLE agent_reputation (
    agent_id INTEGER PRIMARY KEY,
    total_strategies INTEGER DEFAULT 0,
    success_count INTEGER DEFAULT 0,
    failed_count INTEGER DEFAULT 0,
    avg_return NUMERIC DEFAULT 0,
    reputation_score INTEGER DEFAULT 50,
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- User policies (mirror of on-chain, for fast read)
CREATE TABLE user_policies (
    user_address TEXT PRIMARY KEY,
    max_tx_amount TEXT NOT NULL,
    max_daily_loss TEXT NOT NULL,
    max_risk_level SMALLINT NOT NULL,
    active BOOLEAN DEFAULT TRUE,
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Benchmark data (AI vs Human baseline)
CREATE TABLE benchmarks (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    strategy_type TEXT NOT NULL,      -- rice | corn | chili
    participant_type TEXT NOT NULL,    -- ai_agent | human_baseline
    capital_amount TEXT NOT NULL,
    return_pct NUMERIC,
    gas_used TEXT,
    execution_time_ms INTEGER,
    risk_events INTEGER DEFAULT 0,
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

---

## 6. LangGraph Workflow (Simplified)

```
User selects crop + capital
         │
         ▼
    ┌─────────┐
    │ INTENT  │  Parse crop → strategy intent
    └────┬────┘
         │
         ▼
    ┌─────────┐
    │ OBSERVE │  Read market data, protocol states
    └────┬────┘
         │
         ▼
    ┌──────────┐
    │   PLAN   │  LLM selects best strategy option
    └────┬─────┘
         │
         ▼
    ┌──────────┐
    │  POLICY  │  Deterministic check: amount, risk, protocol, daily loss
    │  CHECK   │  ❌ BLOCK if violation → notify user
    └────┬─────┘
         │ ✅ PASS
         ▼
    ┌──────────┐
    │ SIMULATE │  Dry-run tx, estimate gas, preview outcome
    └────┬─────┘
         │
         ▼
    ┌──────────┐
    │ EXECUTE  │  Send tx via viem to Mantle
    └────┬─────┘
         │
         ▼
    ┌──────────┐
    │   LOG    │  Write decision hash + outcome on-chain
    │          │  Write full detail to Supabase
    └────┬─────┘
         │
         ▼
    ┌──────────┐
    │ UPDATE   │  Update reputation score
    │ REPUTATION│  Update benchmark data
    └─────────┘
```

---

## 7. Crop → Strategy Mapping Config

```typescript
// apps/agent/src/config/crops.ts

export const CROPS = {
  rice: {
    name: "Rice Field",
    emoji: "🌾",
    riskLevel: 1,        // low
    expectedReturn: "3–5%",
    description: "Safe and steady. Like planting rice.",
    strategy: {
      type: "lending",
      protocols: ["Aave", "Compound"],  // or Mantle-native equivalents
      action: "supply",
      asset: "USDC",
    },
    color: "#4ade80",
    growthTime: 30000,   // ms for animation (30s demo)
  },
  corn: {
    name: "Corn Field",
    emoji: "🌽",
    riskLevel: 2,        // medium
    expectedReturn: "8–12%",
    description: "Moderate risk, solid harvest.",
    strategy: {
      type: "liquidity_pool",
      protocols: ["Uniswap V3"],
      action: "add_liquidity",
      asset: "USDC/ETH",
    },
    color: "#facc15",
    growthTime: 45000,
  },
  chili: {
    name: "Chili Field",
    emoji: "🌶️",
    riskLevel: 3,        // high
    expectedReturn: "15–25%",
    description: "High heat, high reward. Not for the faint-hearted.",
    strategy: {
      type: "yield_vault",
      protocols: ["GMX", "Pendle"],
      action: "deposit",
      asset: "USDC",
    },
    color: "#ef4444",
    growthTime: 60000,
  },
} as const;

export type CropKey = keyof typeof CROPS;
```

---

## 8. Key Integration Points with Mantle

| What | How | Why |
|------|-----|-----|
| Wallet connect | wagmi + RainbowKit with Mantle chain config | Beginner-friendly onboarding |
| TX execution | viem with Mantle RPC | Fast, cheap EVM tx |
| Agent identity | AgentIdentity contract on Mantle | ERC-8004 direction |
| Decision logs | DecisionLog contract on Mantle | Radical transparency |
| Risk policy | RiskPolicy contract on Mantle | Bounded autonomy |
| Reputation | On-chain score + Supabase cache | Agent trust signal |
| Benchmark | Compare AI agent vs hardcoded human baseline | Human vs AI narrative |
| Testnet deploy | Mantle Sepolia | Demo without real funds |

---

*Document prepared for Mantle Turing Test Hackathon Phase 2.*  
*Team: Butuh Uwang*
