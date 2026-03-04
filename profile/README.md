<p align="center">
  <img src="../assets/logo.svg" alt="Quai Vault" width="160" />
</p>

<h1 align="center">Quai Vault</h1>

<p align="center">
  <strong>Decentralized multisig wallet infrastructure for the Quai Network.</strong>
</p>

<p align="center">
  <a href="https://testnet.quaivault.org"><img src="https://img.shields.io/badge/Testnet-Live-e63946?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAzMiAzMiI+PGNpcmNsZSBjeD0iMTYiIGN5PSIxNiIgcj0iMTUiIGZpbGw9IiMxYTFhMWEiLz48cG9seWdvbiBwb2ludHM9IjE2LDQgMjcsMTAgMjcsMjIgMTYsMjggNSwyMiA1LDEwIiBmaWxsPSIjMjAyMDIwIiBzdHJva2U9IiNlNjM5NDYiIHN0cm9rZS13aWR0aD0iMS41Ii8+PGNpcmNsZSBjeD0iMTYiIGN5PSIxNiIgcj0iNSIgZmlsbD0iIzE1MTUxNSIgc3Ryb2tlPSIjZTYzOTQ2Ii8+PGNpcmNsZSBjeD0iMTYiIGN5PSIxNiIgcj0iMiIgZmlsbD0iI2U2Mzk0NiIvPjwvc3ZnPg==&logoColor=white" alt="Testnet Live" /></a>
  <a href="https://github.com/Quai-Vault"><img src="https://img.shields.io/badge/License-MIT-333333?style=for-the-badge" alt="MIT License" /></a>
  <a href="https://github.com/Quai-Vault/quaivault-contracts"><img src="https://img.shields.io/badge/Tests-315_Passing-2ea043?style=for-the-badge&logo=checkmarx&logoColor=white" alt="315 Tests Passing" /></a>
</p>

<br />

<p align="center">
  <img src="../assets/og-image.png" alt="Quai Vault — Multisig Wallet" width="700" />
</p>

<br />

Quai Vault is an open-source multisig wallet system designed for secure, multi-owner treasury management across Quai Network's sharded architecture. It brings Zodiac-compatible smart account infrastructure to Quai, enabling DAOs, teams, and institutions to manage assets with configurable approval thresholds, native timelocks, token support, and full on-chain transparency.

> **Not a Safe clone.** QuaiVault is a purpose-built multisig with hash-based unordered transactions, integrated timelocks, epoch-based approval invalidation, and Option B failure handling. See the full [technical comparison](https://github.com/Quai-Vault/quaivault-contracts/blob/main/QUAIVAULT_VS_GNOSIS_SAFE.md).

---

## Repositories

| | Repository | Description | Stack |
|:---:|---|---|---|
| <img src="https://img.shields.io/badge/-Solidity-363636?logo=solidity&logoColor=white" /> | [**quaivault-contracts**](https://github.com/Quai-Vault/quaivault-contracts) | Smart contracts — multisig wallet, factory, proxy, and social recovery module | Solidity 0.8.22 · Hardhat · OpenZeppelin v5 |
| <img src="https://img.shields.io/badge/-React-363636?logo=react&logoColor=61DAFB" /> | [**quaivault-frontend**](https://github.com/Quai-Vault/quaivault-frontend) | Wallet management dApp — create, propose, approve, execute transactions | React 18 · Vite 7 · quais.js · Zustand · TanStack Query · wagmi |
| <img src="https://img.shields.io/badge/-TypeScript-363636?logo=typescript&logoColor=3178C6" /> | [**quaivault-indexer**](https://github.com/Quai-Vault/quaivault-indexer) | Blockchain event indexer — real-time tracking of all vault activity | TypeScript · Supabase · Docker · quais.js |
| <img src="https://img.shields.io/badge/-Vite-363636?logo=vite&logoColor=646CFF" /> | [**quaivault-www**](https://github.com/Quai-Vault/quaivault-www) | Marketing site and documentation hub | React 19 · Vite 7 · Three.js · Tailwind CSS 4 |

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

## Key Features

<table>
<tr>
<td width="50%" valign="top">

### Multisig Wallet
- **1-of-N to M-of-N** thresholds with up to 20 owners
- **5-state lifecycle** — pending, executed, cancelled, expired, failed
- **Hash-based transaction IDs** — unordered, parallel execution
- **CREATE2** deterministic, shard-aligned deployment

</td>
<td width="50%" valign="top">

### Native Timelocks & Expiration
- **Vault-level + per-transaction** execution delays
- **Clock-gaming protection** — permanent `approvedAt` timestamp
- **Self-call bypass** — admin ops execute immediately
- **Per-transaction expiry** with permissionless cleanup

</td>
</tr>
<tr>
<td width="50%" valign="top">

### Token & Signature Support
- **ERC-20 / ERC-721 / ERC-1155** — native receivers built-in
- **EIP-1271** contract signatures via multisig consensus
- **ERC-165** interface detection for all standards

</td>
<td width="50%" valign="top">

### Zodiac & Modules
- **[IAvatar](https://github.com/gnosis/zodiac)** standard — compatible with the full Zodiac ecosystem
- **Social Recovery** — guardian-based with 1-day minimum delay
- **MultiSend** for batched atomic transactions

</td>
</tr>
<tr>
<td width="50%" valign="top">

### Indexer
- **26+ event types** with token auto-discovery
- Circuit breaker, reorg detection, graceful shutdown
- Kubernetes-ready (`/health`, `/ready`, `/live`)

</td>
<td width="50%" valign="top">

### Frontend dApp
- **Pelagus + WalletConnect** via Reown AppKit
- **6 transaction modes** including contract call builder
- Indexer-first reads with blockchain RPC fallback

</td>
</tr>
</table>

---

## Smart Contracts

<table>
<tr><td><b>QuaiVault (Implementation)</b></td><td><code>0x0044AbC8bdAaD4D482eDB22E1B946ACAaB2460C5</code></td></tr>
<tr><td><b>QuaiVaultFactory</b></td><td><code>0x00475fA887b961E04Cddd6202D49a3949f2d45D7</code></td></tr>
<tr><td><b>SocialRecoveryModule</b></td><td><code>0x003Ee41F8fFacFCf54119B9cf223ab4CB65ebdF0</code></td></tr>
<tr><td><b>MultiSend</b></td><td><code>0x002BDFaA9e74022B44035995172B030Ead6aA6Bd</code></td></tr>
</table>

<sub>Deployed on Quai Network Cyprus1</sub>

---

## Security

- **OpenZeppelin v5** battle-tested libraries with **ReentrancyGuard** on all paths
- **Hash-based replay protection** with chain ID and vault address binding
- **Epoch-based approval invalidation** — owner removal invalidates all their approvals in O(1)
- **Option B failure handling** — failed external calls are terminal, never stuck
- **Immutable wallets** — ERC1967 constructor proxies, no upgrade path
- **315 tests** (266 unit + 49 E2E) covering wallet, factory, timelocks, expirations, tokens, recovery, and Zodiac compliance

<p align="center">
  <br />
  <a href="https://github.com/Quai-Vault/quaivault-contracts/blob/main/QUAIVAULT_VS_GNOSIS_SAFE.md"><img src="https://img.shields.io/badge/Read-QuaiVault_vs_Gnosis_Safe-e63946?style=for-the-badge" alt="QuaiVault vs Gnosis Safe" /></a>
  <a href="https://github.com/Quai-Vault/quaivault-contracts/blob/main/TRANSACTION_LIFECYCLE_DESIGN.md"><img src="https://img.shields.io/badge/Read-Transaction_Lifecycle-333333?style=for-the-badge" alt="Transaction Lifecycle" /></a>
  <a href="https://github.com/Quai-Vault/quaivault-contracts/blob/main/SECURITY_GUIDE.md"><img src="https://img.shields.io/badge/Read-Security_Guide-333333?style=for-the-badge" alt="Security Guide" /></a>
</p>

---

## Getting Started

<p align="center">
  <a href="https://testnet.quaivault.org"><img src="https://img.shields.io/badge/Launch_App-testnet.quaivault.org-e63946?style=for-the-badge" alt="Launch App" /></a>
  <a href="https://quaivault.org/docs"><img src="https://img.shields.io/badge/Read_Docs-quaivault.org-333333?style=for-the-badge" alt="Documentation" /></a>
</p>

```bash
# Clone all repositories
git clone https://github.com/Quai-Vault/quaivault-contracts.git
git clone https://github.com/Quai-Vault/quaivault-frontend.git
git clone https://github.com/Quai-Vault/quaivault-indexer.git
git clone https://github.com/Quai-Vault/quaivault-www.git

# Smart contracts
cd quaivault-contracts && npm install
npm test                    # 266 unit tests
npm run test:e2e            # 49 on-chain E2E tests

# Indexer
cd ../quaivault-indexer && npm install
cp .env.example .env && npm run dev

# Frontend
cd ../quaivault-frontend && npm install
cp .env.example .env && npm run dev    # http://localhost:5173

# Marketing & docs
cd ../quaivault-www && npm install
npm run dev                             # http://localhost:5174
```

---

## How It Works

1. **Create a Vault** — Deploy via the factory with owners, threshold, and optional minimum execution delay
2. **Propose Transactions** — Any owner submits a proposal with optional expiration and timelock
3. **Gather Approvals** — Owners review and approve; the indexer tracks confirmations in real time
4. **Wait for Timelock** — If set, the transaction becomes executable after the delay elapses from quorum
5. **Execute** — Once threshold is met and any timelock has elapsed, any owner executes on-chain

---

## Contributing

Contributions are welcome. Please open an issue or submit a pull request in the relevant repository.

---

<p align="center">
  <img src="../assets/vault.svg" alt="" width="24" />
  <br />
  <sub>MIT License</sub>
</p>
