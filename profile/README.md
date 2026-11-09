# Mileseal

> **Milestone escrow on Stellar Soroban.** A funder locks the full budget up front. Payees get paid milestone by milestone. Disputes go to a named arbiter. No middleman holds the money.

[![Stellar](https://img.shields.io/badge/Network-Stellar-blue?logo=stellar)](https://stellar.org)
[![Soroban](https://img.shields.io/badge/Smart%20Contracts-Soroban-purple)](https://soroban.stellar.org)
[![License](https://img.shields.io/badge/License-MIT-green)](https://github.com/Mileseal/mileseal-contracts/blob/main/LICENSE)
[![Drips Wave](https://img.shields.io/badge/Drips-Stellar%20Wave%20Program-blueviolet)](https://drips.network/wave)

---

## What is Mileseal?

Mileseal is a smart contract for paying for work in stages. The funder locks the **sum of every milestone** into the contract when the escrow is created. Each milestone has an amount, a deadline and one or more payees, each with a share in basis points.

The funder can't quietly walk away from a deal, and the payees can't be left waiting forever. The rules are in the contract.

---

## How it works

```
1. Funder creates an escrow
   └── Sets an arbiter, a token, a review window and the milestones
   └── The total of all milestones is locked in the contract

2. Payee submits a milestone before its deadline
   └── Attaches a 32-byte proof hash of the deliverable

3. Funder reviews, within the review window
   ├── approve  → payees are paid by their shares
   ├── dispute  → the arbiter rules and sets payee_bps
   └── silence  → after the window, a payee calls claim_unreviewed

4. Nothing submitted by the deadline?
   └── Anyone can call refund_expired and the funder gets that milestone back
```

---

## Key features

- 🔒 **Fully funded up front.** The whole budget is locked at creation.
- 🎯 **Per-milestone payouts.** Each milestone has its own amount, deadline and payees.
- 👥 **Split payees.** Shares are in basis points and must total 10,000. Each payee gets the floor of their share, and the last payee gets the remainder, so no dust is left behind.
- ⏳ **Review window.** A silent funder can't block payment forever.
- ⚖️ **Arbiter.** A disputed milestone is settled by one named address that sets the split between payees and funder.
- ↩️ **Refunds.** Expired, unsubmitted milestones return to the funder.
- 📏 **Limits.** Up to 20 milestones per escrow and 10 payees per milestone.
- 📣 **Events.** Every state change emits an event.

---

## Repositories

| Repo | Language | Description | Status |
|---|---|---|---|
| [mileseal-contracts](https://github.com/Mileseal/mileseal-contracts) | ![Rust](https://img.shields.io/badge/-Rust-orange?logo=rust) | Soroban escrow contract and its test suite | Implemented, tested, deployed to testnet |
| mileseal-sdk | ![TypeScript](https://img.shields.io/badge/-TypeScript-blue?logo=typescript) | `@mileseal/sdk`: typed client that builds unsigned transactions, with decoding and error mapping | Not started yet |
| mileseal-app | ![Next.js](https://img.shields.io/badge/-Next.js-black?logo=next.js) | Next.js frontend with Freighter wallet signing | Not started yet |

---

## Contract API

| Function | Who calls it | What it does |
|---|---|---|
| `create` | funder | Locks the total and creates the escrow |
| `submit` | payee | Submits a milestone with a proof hash |
| `approve` | funder | Pays the milestone out |
| `claim_unreviewed` | payee | Claims after a silent review window |
| `dispute` | funder | Disputes inside the review window |
| `resolve` | arbiter | Splits a disputed milestone between payees and funder |
| `refund_expired` | anyone | Returns an expired, unsubmitted milestone to the funder |
| `get` / `count` | anyone | Reads an escrow / the escrow count |

Errors: `NotFound`, `BadIndex`, `InvalidInput`, `Unauthorized`, `WrongState`, `DeadlinePassed`, `ReviewWindow`, `NotExpired`.

---

## SDK example (planned API)

`@mileseal/sdk` has not been built yet — this shows the target shape of the client API once `mileseal-sdk` exists, not something you can run today.

```ts
import { MilesealClient } from "@mileseal/sdk";

const client = new MilesealClient({ contractId: "C...", networkPassphrase: "..." });

// `wallet.sign` stands in for your wallet's signing call.
// Builds an unsigned transaction. You sign it with Freighter or any wallet.
const unsignedXdr = await client.approve(funderAddress, 0n, 1);
const signedXdr = await wallet.sign(unsignedXdr);
const result = await client.sendSigned(signedXdr);
```

The SDK is designed to never hold keys. Signing always happens on the caller's side.

---

## Tech stack

| Layer | Technology |
|---|---|
| Smart contract | Rust, Soroban SDK 29 |
| Blockchain | Stellar (testnet first) |
| Token | Any Stellar Asset Contract token, such as USDC |
| SDK | TypeScript, `@stellar/stellar-sdk` |
| Wallet | Freighter |
| Frontend | Next.js, Tailwind CSS |

---

## Status

`mileseal-contracts` is implemented, unit-tested (21 tests), and deployed to Stellar
**testnet** — see the [repo](https://github.com/Mileseal/mileseal-contracts) for the real
Contract ID and CI status. `mileseal-sdk` and `mileseal-app` have not been started yet.
**Nothing here is audited, so don't use it with real funds.**

---

## Contributing via Drips Wave

Mileseal is applying to the **Stellar Wave Program** on Drips. Issues across the three repos will be labeled by complexity.

> **Please don't start work on an issue until a maintainer assigns it to you.**
> Comment on the issue to say you're interested and wait for assignment.

👉 **[Stellar Wave Program](https://drips.network/wave)**

---

## License

MIT. See each repository for details.

---

<div align="center">
  <sub>Built on <a href="https://stellar.org">Stellar</a> · <a href="https://drips.network/wave">Stellar Wave Program</a></sub>
</div>
