# Quai Vault

**Decentralized multisig wallet infrastructure for the Quai Network.**

Quai Vault is an open-source multisig wallet system designed for secure, multi-owner treasury management across Quai Network's sharded architecture. It brings Zodiac-compatible smart account infrastructure to Quai, enabling DAOs, teams, and institutions to manage assets with configurable approval thresholds, native timelocks, token support, and full on-chain transparency.

---

## Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                         Users & DAOs                             │
├──────────────┬───────────────────────────────────┬───────────────┤
│  quaivault-  │       quaivault-frontend          │  quaivault-   │
│  www         │       Wallet Management dApp      │  www          │
│  Marketing   │  Create · Propose · Approve ·     │  Docs &       │
│  & Landing   │  Execute · Timelocks · Recovery   │  Guides       │
├──────────────┴──────────────┬────────────────────┴───────────────┤
│                             │                                    │
│                    quaivault-indexer                              │
│   Real-time event indexing · Supabase · Token auto-discovery     │
│   Circuit breaker · Health checks · Reorg detection              │
│                             │                                    │
├─────────────────────────────┴────────────────────────────────────┤
│                    quaivault-contracts                            │
│  QuaiVault · Factory · Proxy · SocialRecovery · MultiSend       │
│  Hash-based TXs · Native timelocks · Zodiac IAvatar             │
├──────────────────────────────────────────────────────────────────┤
│                        Quai Network                              │
│             Cyprus · Paxos · Hydra (9 shards)                    │
└──────────────────────────────────────────────────────────────────┘
```

---

## Repositories

| Repository | Description | Stack |
|---|---|---|
| [**quaivault-contracts**](https://github.com/Quai-Vault/quaivault-contracts) | Smart contracts — multisig wallet, factory, proxy, and social recovery module | Solidity 0.8.22 · Hardhat · OpenZeppelin v5 |
| [**quaivault-frontend**](https://github.com/Quai-Vault/quaivault-frontend) | Wallet management dApp — create, propose, approve, execute transactions | React 18 · TypeScript · Vite 7 · quais.js · Zustand · TanStack Query · wagmi |
| [**quaivault-indexer**](https://github.com/Quai-Vault/quaivault-indexer) | Blockchain event indexer — real-time tracking of all vault activity | TypeScript · Supabase · Docker · Pino · quais.js |
| [**quaivault-www**](https://github.com/Quai-Vault/quaivault-www) | Marketing site and documentation hub | React 19 · Vite 7 · Three.js · Tailwind CSS 4 · Supabase |

---

## Features

### Multisig Wallet
- **1-of-N to M-of-N** configurable approval thresholds with up to 20 owners
- **5-state transaction lifecycle** — pending, executed, cancelled, expired, failed
- **Hash-based transaction IDs** — unordered execution with no head-of-line blocking
- **Deterministic deployment** via CREATE2 with shard-aware address generation
- **Native QUAI** receiving and sending with reentrancy protection

### Native Timelocks
- **Vault-level minimum delay** (`minExecutionDelay`) enforced on all external transactions
- **Per-transaction delay** — proposers can request additional execution delay beyond the vault minimum
- **Clock-gaming protection** — `approvedAt` is set once when quorum is first reached and never cleared
- **Self-call bypass** — admin operations (owner changes, threshold changes) execute immediately for incident response

### Transaction Expiration
- **Optional per-transaction expiry** — proposers set a Unix timestamp after which the transaction cannot execute
- **Permissionless cleanup** — anyone can call `expireTransaction()` after the expiry passes
- **Unambiguous state** — `expiredTxs` mapping distinguishes expired from cancelled

### Token Support
- **ERC-20** — hold and send fungible tokens
- **ERC-721** — hold and send NFTs with `onERC721Received` support
- **ERC-1155** — hold and send multi-tokens with `onERC1155Received` / `onERC1155BatchReceived` support
- **ERC-165** — native interface detection for all supported token standards

### EIP-1271 Contract Signatures
- **Mapping-based pre-approval** — multisig consensus to sign messages (no ECDSA)
- **Sign/unsign workflow** — messages can be approved and later revoked via multisig proposals
- **EIP-712 domain separator** — includes name ("QuaiVault") and version ("1") for wallet UI display

### Zodiac Compatibility
Quai Vault implements the [Zodiac IAvatar](https://github.com/gnosis/zodiac) standard, making it compatible with the broader governance ecosystem:
- Moloch V3 / DAOhaus integration
- OpenZeppelin Governor support
- Zodiac Modifiers (Delay, Roles, Scope, Bridge, Exit)
- Snapshot + SafeSnap governance
- MultiSend for batched atomic transactions
- Any Zodiac-compatible module can be enabled via multisig consensus

### Social Recovery Module
- Guardian-based wallet recovery with configurable threshold and time-delayed execution
- Minimum 1-day recovery period enforced on-chain
- Wallet owners can cancel pending recoveries during the delay window
- Setup requires multisig consensus (guardians cannot self-configure)
- Max 20 guardians and 10 pending recoveries per wallet

### Indexer
- **26+ event types** indexed across factory, vault, module, and token contracts
- Real-time polling with circuit breaker (closed → open → half-open) and exponential backoff with jitter
- **Token auto-discovery** — probes unknown contracts for ERC-20/721/1155 metadata on first encounter
- **Reorg detection** — persists block hashes and rolls back on chain reorganization
- Historical backfill with resumable batch processing and concurrent wallet backfills
- Priority-based event processing — factory → wallets → modules → token transfers
- Transaction calldata decoding (transfer, wallet admin, module config, token ops, batched calls)
- Multi-schema database support (testnet/mainnet via `SUPABASE_SCHEMA`)
- Kubernetes-ready with health check endpoints (`/health`, `/ready`, `/live`) and per-IP rate limiting
- Graceful shutdown with in-flight work completion and 10s force-exit timeout

### Frontend dApp
- **Pelagus + WalletConnect** — supports Quai's native wallet and WalletConnect v2 (Tangem, others) via Reown AppKit
- **Indexer-first architecture** — Supabase for fast reads with real-time subscriptions, blockchain RPC as automatic fallback
- **6 transaction modes** — Send QUAI, Send Token (ERC-20), Send NFT (ERC-721), Send ERC-1155, Contract Call, Sign Message
- **Contract interaction builder** — auto-fetches ABIs from Sourcify with function selector and parameter builder
- **Advanced transaction options** — per-transaction expiration and execution delay on all proposal types
- Token balances, NFT collection browser with image previews, and ERC-1155 inventory panels
- Timelock countdown timers and expiration tracking
- Owner and module management with add/remove/threshold change modals
- Social recovery configuration and guardian management
- **CREATE2 salt mining** via Web Worker for shard-aligned deterministic deployment
- **Dark/light/system theme** with flash prevention and system preference detection
- Transaction decoding, gas estimation, and browser notifications
- Signed message browser with sign/unsign history

### Marketing & Documentation Site
- **3D animated background** — Three.js scene with dodecahedron vault core, orbiting icosahedrons, and floating hash blocks
- **Live vault statistics** — queries Supabase for real-time wallet count and QUAI secured
- **8 documentation sections** — Getting Started, Multisig Wallets, Modules, Frontend Guide, Developer Guide, Security, FAQ
- Dark/light theme with SEO optimization, Open Graph, and security headers

---

## Smart Contracts

Deployed on Quai Network Cyprus1:

| Contract | Address |
|---|---|
| QuaiVault (Implementation) | `0x0044AbC8bdAaD4D482eDB22E1B946ACAaB2460C5` |
| QuaiVaultFactory | `0x00475fA887b961E04Cddd6202D49a3949f2d45D7` |
| SocialRecoveryModule | `0x003Ee41F8fFacFCf54119B9cf223ab4CB65ebdF0` |
| MultiSend | `0x002BDFaA9e74022B44035995172B030Ead6aA6Bd` |

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
npm test                    # 266 unit tests
npm run test:e2e            # 49 on-chain E2E tests
npm run deploy:cyprus1      # Deploy to Cyprus1

# Indexer
cd ../quaivault-indexer
npm install
cp .env.example .env        # Configure RPC & Supabase
npm run dev                  # Start indexing

# Frontend dApp
cd ../quaivault-frontend
npm install
cp .env.example .env        # Configure contract addresses & Supabase
npm run dev                  # http://localhost:5173

# Marketing site
cd ../quaivault-www
npm install
npm run dev                  # http://localhost:5174
```

---

## How It Works

1. **Create a Vault** — Deploy a new multisig wallet through the factory with your chosen owners, approval threshold, and optional minimum execution delay
2. **Propose Transactions** — Any owner submits a transaction proposal with optional expiration and execution delay (transfers, token sends, owner changes, module configuration, message signing)
3. **Gather Approvals** — Owners review and approve proposals; the indexer tracks confirmations in real time via Supabase subscriptions
4. **Wait for Timelock** — If the transaction has an execution delay, it becomes executable only after the delay elapses from the moment quorum was reached
5. **Execute** — Once the threshold is met and any timelock has elapsed, any owner can execute the transaction on-chain

---

## Security

- **OpenZeppelin v5** battle-tested contract libraries
- **ReentrancyGuard** on all execution paths
- **Hash-based transaction IDs** — replay protection via content-addressed hashing with chain ID and vault address
- **Epoch-based approval invalidation** — owner removal atomically invalidates all in-flight approvals from that owner (O(1))
- **Option B failure handling** — failed external calls are terminal, preventing permanent stuck transactions
- **Native timelocks** — clock-gaming protection with permanent `approvedAt` timestamps
- **Self-call bypass** — admin operations execute immediately for fast incident response
- **Module isolation** — modules cannot modify their own permissions
- **Time-delayed recovery** — social recovery enforces minimum 1-day waiting periods
- **Immutable wallets** — ERC1967 constructor proxies cannot be upgraded after deployment
- **266 unit tests + 49 E2E tests** covering core wallet, factory, timelocks, expirations, token receivers, social recovery, and Zodiac interface compliance

### QuaiVault vs Gnosis Safe

QuaiVault is **not a Safe clone** — it's a purpose-built multisig with fundamentally different design decisions. For a detailed technical comparison across 9 dimensions (architecture, transaction model, timelock, cancellation, expiration, failure handling, approval system, module system, and proxy pattern), see:

> **[QUAIVAULT_VS_GNOSIS_SAFE.md](quaivault-contracts/QUAIVAULT_VS_GNOSIS_SAFE.md)** — Side-by-side comparison for developers and integrators

Additional design documentation in the contracts repo:
- [**TRANSACTION_LIFECYCLE_DESIGN.md**](quaivault-contracts/TRANSACTION_LIFECYCLE_DESIGN.md) — Canonical state machine, timelock system, epoch-based invalidation, and failure handling rationale
- [**SECURITY_GUIDE.md**](quaivault-contracts/SECURITY_GUIDE.md) — Threat model, realistic attack vectors, and operational security recommendations

---

## Use Cases

- **DAO Treasuries** — Zodiac-compatible vault for on-chain governance
- **Team Wallets** — Shared operational funds with configurable approval requirements
- **High-Value Custody** — Multi-signature protection with timelocks for institutional holdings
- **Token Management** — Hold and manage ERC-20, ERC-721, and ERC-1155 assets under multisig control
- **Multi-Zone Operations** — Manage assets across Quai Network's sharded regions

---

## Contributing

Contributions are welcome. Please open an issue or submit a pull request in the relevant repository.

---

## License

MIT
