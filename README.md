# Epoch — Trustless Token Vesting on Sui

> **Token vesting, trustless on SUI.**
> No custodian. No monthly fees. Your tokens, your rules.

Live at [epochsui.com](https://epochsui.com)

---

## What is Epoch?

Epoch is a fully trustless, non-custodial token vesting protocol built on Sui. Token projects use Epoch to lock team, advisor, and investor allocations on-chain with cryptographic guarantees — no admin keys, no middlemen, no cancel.

Once a vault is deployed, it is **immutable and non-cancellable**. Tokens vest according to a schedule defined at creation time and can only ever flow to the designated beneficiary. The creator has zero control over the vault after deployment.

---

## Why Epoch?

Traditional vesting solutions rely on:
- Custodians that can rug or freeze funds
- Centralized dashboards with admin overrides
- Monthly subscription fees
- Trust assumptions that contradict DeFi principles

Epoch eliminates all of these. The protocol is fully on-chain, permissionless, and works with **any SUI coin type**.

---

## Features

- **Trustless VestingVault** — immutable after creation, no cancel, no admin override
- **Multi-beneficiary vaults** — up to 20 beneficiaries with custom share allocations (in basis points)
- **Three vesting schedules:**
  - Pure Cliff — 100% unlocks at a single timestamp
  - Pure Linear — tokens unlock continuously from start to end
  - Hybrid — X% at cliff, remainder unlocks linearly
- **Any token** — fully generic over coin type, works with every token on the Sui blockchain (SUI, USDC, USDT, meme coins, project tokens — anything with a `Coin<T>`)
- **Developer CLI** — integrate vesting into any token launch programmatically
- **AI Agent** — natural language queries over vaults, token prices, staking, and ecosystem data

---

## Contract Architecture

```
vesting_service::vesting
│
├── VestingVault<T>           — Single beneficiary vault
├── MultiVestingVault<T>      — Multi-beneficiary vault (up to 20)
├── Treasury                  — Collects deploy fees
└── AdminCap                  — Fee management only (no vault control)
```

### Vesting Schedule Logic

```
cliff_bps = 10000, linear = 0          → Pure Cliff
cliff_bps = 0,     linear start → end  → Pure Linear
cliff_bps = N,     linear start → end  → Hybrid (N% at cliff, rest linear)
```

All amounts are computed in basis points (1 BPS = 0.01%) for precision.

---

## Deployed Contracts

| Network  | Package ID |
|----------|-----------|
| Mainnet  | `0x848cb7edf8b5f7650b3188dec459394472c8ccf206a031497bf55fe40c165da2` |
| Testnet  | `0xc1427dd16f3d6ee090d48b24fc2cdb3effb4d28898504e742987f4eddb61118c` |

---

## Creating a Vault

```typescript
// Single beneficiary — pure linear vesting over 12 months
await client.tx(createVault({
  token: "0x2::sui::SUI",
  amount: 1_000_000_000_000,   // 1000 SUI
  beneficiary: "0xABCD...",
  cliffBps: 0,
  linearStartMs: Date.now(),
  linearEndMs: Date.now() + 365 * 24 * 60 * 60 * 1000,
}))

// Hybrid — 20% at cliff, 80% linear over 2 years
await client.tx(createVault({
  token: "0x...",
  amount: 10_000_000,
  beneficiary: "0xABCD...",
  cliffBps: 2000,              // 20% at cliff
  cliffTsMs: Date.now() + 90 * 24 * 60 * 60 * 1000,   // 90 days
  linearStartMs: ...,
  linearEndMs: ...,
}))
```

---

## Product Overview

### Vaults
Create and manage vesting vaults directly from the Epoch dashboard. Set the schedule, the beneficiary, and the token — the contract handles the rest immutably on-chain.

### Unlocks
A real-time timeline view showing when tokens unlock across all active vaults. Beneficiaries can track exactly how much is vested and claimable at any given moment, with a full history of past claims.

### Analytics
Protocol-wide stats and per-vault breakdowns: total locked value, circulating vs. locked supply across all vaults, vault creation history, and claim activity over time.

### Epoch AI Agent
A conversational AI assistant built directly into the app. Users can ask questions in plain language and get real-time answers powered by live on-chain data:
- *"How much of my tokens have vested?"*
- *"What is the current price of DEEP?"*
- *"Show me the top staking validators on Sui"*
- *"What are the trending tokens on Sui today?"*
- *"Which projects are currently on Sui testnet?"*

The agent uses the Anthropic Claude API and queries Sui RPC, Cetus, and other ecosystem data sources directly — no third-party aggregators, no stale data.

---

## Fee Model

A one-time deploy fee (10 SUI default) is charged per vault creation. No recurring fees. The fee goes to the Epoch treasury and is the only protocol revenue. Vault logic itself is entirely permissionless after deployment.

---

## Security Properties

- Vaults are **shared objects** on Sui — no single owner can modify or cancel them
- The `AdminCap` controls only fee updates and treasury withdrawals — it has **zero power over any vault**
- No upgradeability on vault logic — what you deploy is what you get
- Extensively stress-tested on testnet across all schedule types (cliff, linear, hybrid), multi-beneficiary configurations, and edge cases including boundary timestamps and maximum beneficiary counts:
  - **264 vaults** created (single + multi-beneficiary)
  - **415 wallets** with active vesting positions
  - **249 claim transactions** executed on-chain
  - **14 unique creators** (distinct deployers)

---

## Built With

- [Sui Move](https://docs.sui.io/build/move) — smart contract language
- [Sui TypeScript SDK](https://sdk.mystenlabs.com/typescript) — frontend and CLI integration
- [Supabase Edge Functions](https://supabase.com/docs/guides/functions) — AI Agent backend (Deno runtime)
- [Anthropic Claude](https://anthropic.com) — AI Agent LLM

---

## Links

- Website: [epochsui.com](https://epochsui.com)
- Twitter: [@EpochSui](https://x.com/EpochSui)

---

## Sui Overflow 2026

This project is submitted to **Sui Overflow 2026** under the **DeFi & Payments** track.

> *Epoch provides the trustless financial primitive that any token project on Sui needs at launch — a vesting protocol you can point investors and advisors to and say: the contract is immutable, your tokens are safe, no one can touch them.*
