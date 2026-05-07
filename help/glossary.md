# Glossary

Quick definitions for the jargon you'll hit across the docs and Discord. Casual, not academic.

## B

**Base fee** *(EVM)* — the minimum gas price the network is currently charging. Set algorithmically; you can't bid below it. Often written `gwei`.

**Batch mode** — Tasks setting that coordinates many wallets minting the same contract from one machine. Leader broadcasts, followers fire in lockstep. Reduces RPC pressure. See [Tasks → Batch mode](../features/tasks.md#batch-mode).

## C

**Calldata** — the raw hex payload of a transaction. Encodes the function being called + arguments. The Dashboard's Contract Hex Generator builds it for you.

**Captcha solver** — third-party API (CapMonster / 2Captcha / CapSolver) that solves Turnstile / hCaptcha / reCAPTCHA challenges in exchange for a per-solve fee.

**Cold storage** — a wallet whose private key isn't loaded in any always-online software. Hardware wallets, paper backups. The opposite of a hot wallet.

## D

**Drop** — a collection's mint event. Includes one or more phases (WL / Public / etc.) with their own pricing and timing.

## E

**ERC-721** — Ethereum's standard for unique, non-fungible tokens. Each token has a unique ID; one address can own many.

**ERC-1155** — Ethereum's standard for tokens that mix fungible and non-fungible. One contract can hold many distinct token IDs, each with its own supply.

**EIP-1559** — Ethereum's gas pricing scheme since the London hard fork. Splits gas into a network-set "base fee" + a user-set "priority fee" tip. Most modern wallets default to this.

## F

**Floor price** — the lowest active listing for a collection on a given marketplace. Used for PnL math and the Dashboard's floor-based gas calculator.

**Free mint** — a drop with mint price = 0 ETH. You still pay gas.

## G

**Gas** — the network fee for executing a transaction. Paid in the chain's native token. `total cost = mint price + (gas limit × gas price)`.

**Gas limit** — the max units of computation you authorize for a tx. Unused gas is refunded; if the tx needs more than your limit, it reverts (and you still pay for the partial work).

**Gwei** — `10^9 wei`, or one billionth of an ETH. Standard unit for gas prices because raw wei is unwieldy and ETH is too coarse.

## H

**hCaptcha** — a captcha challenge type used by some platforms. Most modern solver services support it.

**Hot wallet** — a wallet whose private key is loaded in software (extension, app, server). Convenient but riskier than cold storage. Most minting wallets are hot.

## L

**Lab manifest** — a JSON document in [Laboratory](../features/laboratory.md) that defines a custom platform's behavior. Tells the engine which HTTP calls to make, how to compute eligibility, and how to build the mint transaction.

## M

**Mempool** — the chain's queue of pending transactions waiting to be included in a block. Validators pick from here when building blocks.

**Mint price** — what the contract charges per token, set by the creator. Not the same as total cost — platform fees and royalties may be added.

**Monitor mode** — Tasks setting that keeps watching the tx after broadcast for re-orgs or replacement. Default off.

## N

**Nonce** — a per-wallet counter the chain uses to order transactions. Each new tx increments it. If your local nonce drifts from the chain's (e.g., a previous tx is stuck pending), see [Reset Nonce](../core-concepts/wallets.md#bulk-operations).

## P

**Phase** *(also "stage")* — a slice of a drop with its own pricing and eligibility rules. Common phases: Allowlist / Whitelist / Public.

**Phase 2** *(Laboratory)* — the schema-v2 manifest level, which unlocks signing, contract reads, and transaction sends. Phase 1 manifests are read-only.

**Priority fee** — your tip to the validator on top of the base fee. Higher tip = faster inclusion (in theory). Sometimes called "max priority fee per gas".

**Proxy** *(networking)* — an intermediate server your traffic routes through. Used to spread requests across IPs so platforms don't rate-limit you.

**Public sale** — the open phase of a drop where anyone can mint. Usually higher price than WL.

## R

**reCAPTCHA** — Google's captcha service. Older but still common. Solver services support it.

**Repeater** — Tasks setting that runs the same task N times in succession on one wallet. Useful for FCFS public mints.

**Residential proxy** — a proxy whose IP belongs to a real consumer ISP. Slower but harder to fingerprint vs datacenter proxies, which most modern platforms blacklist.

**RPC** *(EVM)* — Remote Procedure Call. The HTTP endpoint your client uses to read chain state and broadcast transactions. Examples: `https://mainnet.base.org`, an Alchemy URL.

## S

**Seed phrase** *(also "mnemonic")* — 12 or 24 words that deterministically derive a wallet's keys. Whoever has these owns the wallet.

**Send mode** — Tasks setting that auto-transfers a successful mint to a destination wallet (set in [Settings → Wallet Settings](../settings/settings.md#wallet-settings)). Skips the manual consolidate step.

**Slippage** — the gap between the price you expect and the price you actually pay/receive. Some mint contracts have a `maxPrice` arg to enforce a slippage ceiling.

**Spam mode** — Tasks setting that retries on every error type, very aggressively. For ultra-competitive drops where you'd rather burn gas than miss.

## T

**Turnstile** *(Cloudflare)* — Cloudflare's lighter-weight captcha alternative. Common on Mintify, Scatter, and Cloudflare-fronted drops.

## V

**Vault wallet** — a cold wallet you use only as a destination for winnings. Doesn't sign mint transactions itself.

## W

**Watch-only wallet** — a wallet imported by address only, no key. Can show balances and be referenced in displays, but can't sign anything.

**Wei** — the smallest unit of ETH. `1 ETH = 10^18 wei`. Most APIs return values in wei to avoid float precision loss.

**Whitelist (WL)** *(also "allowlist")* — the gated phase of a drop where only pre-approved wallets can mint, usually at a discount. The [WL Checker](../features/whitelist-checker.md) is built around this.

---

Don't see a term? It's probably in [FAQ](faq.md) or you can ask in Discord support.
