# Quai Vault

**Decentralized multisig wallet infrastructure for the Quai Network.**

Quai Vault is an open-source, modular multisig wallet system designed for secure, multi-owner treasury management across Quai Network's sharded architecture. It brings Zodiac-compatible smart account infrastructure to Quai, enabling DAOs, teams, and institutions to manage assets with configurable approval thresholds, extensible security modules, and full on-chain transparency.

---

## Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                         Users & DAOs                             │
├──────────────┬───────────────────────────────────┬───────────────┤
│  quaivault-  │       quaivault-frontend          │  quaivault-   │
│  www         │       Wallet Management dApp      │  www          │
│  Marketing   │  Create · Propose · Approve ·     │  Docs &       │
│  & Landing   │  Execute · Modules · Recovery     │  Guides       │
├──────────────┴──────────────┬────────────────────┴───────────────┤
│                             │                                    │
│                    quaivault-indexer                              │
│          Real-time event indexing · Supabase · Health checks     │
│                             │                                    │
├─────────────────────────────┴────────────────────────────────────┤
│                    quaivault-contracts                            │
│   QuaiVault · Factory · Proxy · Modules · MultiSend · Zodiac    │
├──────────────────────────────────────────────────────────────────┤
│                        Quai Network                              │
│             Cyprus · Paxos · Hydra (9 shards)                    │
└──────────────────────────────────────────────────────────────────┘
```

---

## Repositories

| Repository | Description | Stack |
|---|---|---|
| [**quaivault-contracts**](https://github.com/Quai-Vault/quaivault-contracts) | Smart contracts — multisig wallet, factory, proxy, and security modules | Solidity 0.8.22 · Hardhat · OpenZeppelin v5 |
| [**quaivault-frontend**](https://github.com/Quai-Vault/quaivault-frontend) | Wallet management dApp — create, propose, approve, execute transactions | React 18 · TypeScript · quais.js · Zustand |
| [**quaivault-indexer**](https://github.com/Quai-Vault/quaivault-indexer) | Blockchain event indexer — real-time tracking of all vault activity | TypeScript · Supabase · Docker |
| [**quaivault-www**](https://github.com/Quai-Vault/quaivault-www) | Marketing site and documentation hub | React 19 · Vite · Three.js · TailwindCSS |

---

## Features

### Multisig Wallet
- **1-of-N to M-of-N** configurable approval thresholds with up to 20 owners
- **Full transaction lifecycle** — propose, approve, revoke, execute, cancel
- **Deterministic deployment** via CREATE2 with shard-aware address generation
- **Native QUAI** receiving and sending with reentrancy protection

### Zodiac Compatibility
Quai Vault implements the [Zodiac IAvatar](https://github.com/gnosis/zodiac) standard, making it compatible with the broader governance ecosystem:
- Moloch V3 / DAOhaus integration
- OpenZeppelin Governor support
- Zodiac Modifiers (Delay, Roles, Scope, Bridge, Exit)
- Snapshot + SafeSnap governance
- MultiSend for batched atomic transactions

### Security Modules

| Module | Purpose |
|---|---|
| **Daily Limit** | Allow single-owner transactions below a configurable daily spending cap |
| **Whitelist** | Pre-approve recipient addresses to bypass full multisig approval |
| **Social Recovery** | Guardian-based wallet recovery with time-delayed execution |

### Indexer
- Tracks **27 event types** across all vault and module contracts
- Real-time polling with circuit breaker and exponential backoff
- Historical backfill with resumable batch processing
- Kubernetes-ready with health check endpoints (`/health`, `/ready`, `/live`)

### Frontend dApp
- **Pelagus wallet** connection via EIP-1193
- **Hybrid data layer** — indexer-first reads with blockchain RPC fallback
- Real-time Supabase subscriptions with polling safety net
- Transaction decoding, gas estimation, and browser notifications
- Full module management UI for daily limits, whitelists, and social recovery

---

## Smart Contracts

Deployed on Quai Network Cyprus1:

| Contract | Address |
|---|---|
| QuaiVault (Implementation) | `0x00707D5c7e35253265267DE764d2625cAb04082C` |
| QuaiVaultFactory | `0x005261a837f1eFEa0e23b66dc526EB6054FD2250` |
| SocialRecoveryModule | `0x006A7e95c426C996bb8cf6f517ddCf9899F43d6d` |
| DailyLimitModule | `0x000d024268520A1fBA23aD688de4639e4b8d5322` |
| WhitelistModule | `0x001D58504c9Eb7e432d9e8591f34A4C73A2043d7` |
| MultiSend | `0x000bf87B9a7D4Bf60F95e0a27A6254dE7655b345` |

---

## Getting Started

### Use the App

Visit [testnet.quaivault.org](https://testnet.quaivault.org) to create and manage multisig wallets on Quai Network testnet.

### Run Locally

```bash
# Clone all repositories
git clone https://github.com/Quai-Vault/quaivault-contracts.git
git clone https://github.com/Quai-Vault/quaivault-frontend.git
git clone https://github.com/Quai-Vault/quaivault-indexer.git
git clone https://github.com/Quai-Vault/quaivault-www.git

# Smart contracts
cd quaivault-contracts
npm install
npm test                    # 154 tests
npm run deploy:cyprus1      # Deploy to Cyprus1

# Indexer
cd ../quaivault-indexer
npm install
cp .env.example .env        # Configure RPC & Supabase
npm run dev                  # Start indexing

# Frontend dApp
cd ../quaivault-frontend
npm install
cp .env.example .env        # Configure contract addresses
npm run dev                  # http://localhost:5173

# Marketing site
cd ../quaivault-www
npm install
npm run dev                  # http://localhost:5174
```

---

## How It Works

1. **Create a Vault** — Deploy a new multisig wallet through the factory with your chosen owners and approval threshold
2. **Propose Transactions** — Any owner submits a transaction proposal (transfers, owner changes, module configuration)
3. **Gather Approvals** — Owners review and approve proposals; the indexer tracks confirmations in real time
4. **Execute** — Once the threshold is met, any owner can execute the transaction on-chain

For module-enabled vaults, certain operations (small transfers, whitelisted recipients) can bypass full multisig approval while maintaining security guarantees.

---

## Security

- **OpenZeppelin v5** battle-tested contract libraries
- **ReentrancyGuard** on all execution paths
- **Nonce + Chain ID** transaction hashing prevents replay attacks
- **Module isolation** — modules cannot modify their own permissions
- **Time-delayed recovery** — social recovery enforces minimum waiting periods
- **154 passing tests** covering core wallet, factory, all modules, and Zodiac interface compliance

---

## Use Cases

- **DAO Treasuries** — Zodiac-compatible vault for on-chain governance
- **Team Wallets** — Shared operational funds with configurable approval requirements
- **High-Value Custody** — Multi-signature protection for institutional holdings
- **Escrow Services** — Trustless escrow with transparent approval tracking
- **Multi-Zone Operations** — Manage assets across Quai Network's sharded regions

---

## Contributing

Contributions are welcome. Please open an issue or submit a pull request in the relevant repository.

---

## License

MIT
