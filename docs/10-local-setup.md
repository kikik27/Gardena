# 10 — Local Setup

## Requirements
- Node.js 22+
- pnpm
- Foundry (`forge`, `cast`)

## Install
```bash
pnpm install
```

## Run
```bash
pnpm dev
```

## Validate
```bash
pnpm --filter web build
pnpm --filter @gardena/agent build
pnpm --filter @gardena/contracts test
```
