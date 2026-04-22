<p align="center">
  <img src="https://vdaeojxonqmzejwiioaq.supabase.co/storage/v1/object/public/monipay/monipay-m-logo.png" alt="MoniPay Logo" width="80" />
</p>

<h1 align="center">MoniPay</h1>

<p align="center">
  <strong>The Uniswap of Payments</strong><br/>
  <em>Permissionless. Neutral. Immutable.</em>
</p>

<p align="center">
  <a href="https://monipay.xyz">Live App</a> ·
  <a href="https://x.com/monipay_xyz">@monipay_xyz</a> ·
  <a href="https://x.com/monibot">@monibot</a> ·
  <a href="DOCUMENTATION.md">Technical Docs</a>
</p>

---

## The Thesis: Why I Am Building This

> *In 2014, the vision of Ethereum was clear: a world computer for permissionless, decentralized applications. A promise of a parallel web where users owned their data and their value.*
>
> *Today, we face what Dasha ([@0xdasha](https://x.com/0xdasha)) calls "compliance drift." The industry has become enamored with institutional adoption, trading the cypherpunk ethos for the approval of global finance. We are seeing a pivot where Ethereum is increasingly viewed as a settlement layer for regulated securities, where wallets are gated, and history is surveilled.*
>
> *I have watched many builders accept this. They argue that for payments to work at scale, we must embrace custody, KYC, and proprietary hardware. They suggest that a truly decentralized payments solution is impossible to scale.*
>
> *I disagree.*
>
> *I am building MoniPay because I believe the original vision **the tool you own, not the service you rent** is the only one worth building.*

---

## The Walkaway Test

Vitalik Buterin draws a distinction between a **"Hammer"** and a **"Corposlop AI Dishwasher."**

The Dishwasher requires a subscription, spies on you, and stops working if the vendor goes offline.

The Hammer is a tool. You buy it once. It is yours. It works forever.

Most modern Fintech and Crypto payment gateways are **Dishwashers**. They are rent-seeking intermediaries that can deplatform you at a keystroke.

**MoniPay is a Hammer.**

It is a non-custodial smart payment infrastructure powered by AI. Monipay is a terminal-free POS protocol designed to pass the "Walkaway Test." Even if MoniPay the entity disappears:
- Your funds remain accessible
- Your keys remain in your local enclave
- The protocol remains on-chain

We are not building a bank. We are building the **Uniswap of Payments**: Permissionless, Neutral, and Immutable.

---

## What is MoniPay?

MoniPay is a **non-custodial, terminal-free micropayment rail** on Base, BNBChain, Celo, Ink & Solan. We are solving the "last mile" adoption problem in emerging markets by replacing expensive POS hardware and hexadecimal addresses with an **"invisible" non-custodial wallet** and human-readable social identities (**MoniTags**).

Our architecture merges financial infrastructure with **Agentic Commerce** via MoniBot an autonomous AI agent that verifies social identity and executes gasless, on-chain transactions directly on social timelines. By utilizing a custom EIP-712 Relayer and local AES-GCM encryption, we've abstracted gas fees and seed phrases into a seamless, neobank-like experience.

- **No gas fees.** Ever. For anyone.
- **No hexadecimal addresses.**  Monitag for all.
- **No wallet extensions.** No MetaMask popups. No seed phrases shown.
- **No blockchain literacy required.** Users see MoniTags, PINs, and instant confirmations.
- **No hardware terminals.** Any smartphone is a POS.
- **No Monipay account/no crypto wallet required to receive tips & payments for the first time, just your social identity** Magicpay

MoniPay works everywhere — from a street vendor's phone in Dubai to a checkout page in Bali.

---

## Architecture: The Invisible Wallet

> *"The best wallet is one you never see."*

We reject custodial shortcuts. Instead, we use **Local Key Generation** — ephemeral Ethereum keys are created client-side, encrypted via AES-256-GCM (salted with a user PIN via PBKDF2, 100K iterations), and stored in the device enclave. Users own their keys, but never see a seed phrase.

```
┌──────────────────────────────────────────────────┐
│              THE INVISIBLE WALLET                │
│──────────────────────────────────────────────────│
│                                                  │
│  On Signup:                                      │
│    1. Generate random Ethereum private key        │
│    2. Encrypt with user's PIN (AES-256-GCM)      │
│    3. Store encrypted key in localStorage         │
│    4. Register MoniTag → wallet address mapping   │
│                                                  │
│  On Payment:                                     │
│    1. User enters amount + recipient MoniTag      │
│    2. App signs EIP-712 message with local key    │
│    3. Signature relayed to MoniPay backend        │
│    4. Paymaster submits tx and pays all gas       │
│    5. Smart contract splits: 99% merchant, 1% us │
│                                                  │
│  The user typed a name and a number.             │
│  Everything else was invisible.                  │
│                                                  │
└──────────────────────────────────────────────────┘
```

### Gas Abstraction (Custom Relayer)

To bypass geo-blocked SDKs, we built a **proprietary EIP-712 Relayer**. Users sign readable intents locally ("Pay $10 to @Merchant"), and our backend submits the transaction to the MoniPayRouter smart contract, sponsoring the gas fees. The user never touches ETH or BNB. They never know a blockchain exists.

### Security Model

| Layer | Mechanism |
|-------|-----------|
| **Key Encryption** | AES-256-GCM with PBKDF2 (100K iterations) |
| **Request Signing** | HMAC-SHA256 on every mutation |
| **Database** | Row-Level Security — all writes gated through Edge Functions |
| **Rate Limiting** | Per-endpoint throttling (10 tx/min, 3 registrations/10min) |
| **PIN Protection** | Escalating lockouts (1min → 5min → 15min → 1hr) |
| **Backup** | Google Drive encrypted backup to hidden appDataFolder |

Because settlement is on-chain and keys are local, **if our servers disappear, the user's money survives.** That is the Walkaway Test. That is the point.


## MoniBot: The Autonomous Financial Agent
 
> *"What if money didn't wait for you to move it?"*
 
**MoniBot** ([@monibot](https://x.com/monibot)) is not a chatbot with a wallet. It is a **financial agent** — an autonomous entity that lives on X (Twitter), Discord, and Telegram, thinks with Gemini, and executes on-chain. It represents a fundamentally new category: **Agentic Commerce**, where AI doesn't just assist transactions but *initiates, evaluates, and completes them* without human intermediation.
 
This is the **Request → Intent → Execution** loop:
 
```
"@monibot send $5 to @alice"     ← Natural language intent
         ↓
   AI parses, validates,          ← Agent reasoning (Gemini 2.0 Flash)
   checks balances, allowances
         ↓
   CasualPay.executeP2P()         ← Atomic on-chain execution
         ↓
   "✅ Sent! tx: 0xab3f..."      ← Verifiable confirmation
```
 
No app opened. No button clicked. No gas paid. Just a sentence, and value moved.
 
---
 
## MoniBot Payment Modes
 
MoniBot operates two distinct on-chain payment rails, each designed for a specific social context:
 
### 💸 CasualPay — For Linked Users
 
CasualPay (powered by the **MoniBotRouter** contract) handles direct P2P transfers between registered MoniPay users. Both sender and recipient must have linked MoniTags.
 
- Sender types: `@monibot send $5 to @alice`
- MoniBot verifies both MoniTags on-chain
- `CasualPay.executeP2P()` executes atomically
- Funds land in @alice's wallet instantly
- Single transaction. No gas. Verifiable on-chain.
### 🪄 MagicPay — For Anyone, Even the Unregistered
 
MagicPay (powered by the **IOURegistry** contract) is Social Escrow. It lets you send funds to *anyone* even people who haven't created a MoniPay account yet. Funds are locked in escrow on-chain, tied to the recipient's social identity hash. When they register and link their account, the funds release automatically.
 
```
Sender: "@monibot send $10 to @jade"
         ↓
   MoniBot detects @jade has no MoniTag
         ↓
   IOURegistry.executeCreate()       ← Funds escrowed on-chain
   recipientId = keccak256("twitter:jade_numeric_id")
         ↓
   @jade signs up at monipay.xyz
   Links their Twitter account
         ↓
   IOURegistry releases funds        ← Claimed automatically
   180-day expiry window
```
 
**Why this matters:** You can pay street vendors, friends, or customers who have never heard of MoniPay. The money waits for them. Unclaimed funds return to sender after 180 days — on-chain, trustlessly.
 
| Feature | CasualPay | MagicPay |
|---|---|---|
| Recipient must be registered | ✅ Yes | ❌ No |
| Funds held in escrow | No — direct transfer | Yes — IOURegistry |
| Recipient identity | MoniTag | Social identity hash |
| Expiry | None | 180 days |
| Contract | MoniBotRouter | IOURegistry |
| Cross-platform | Base · BSC · Celo · Ink | Base · BSC · Celo · Ink |
 
---
 
## The Two-Bot System
 
MoniBot operates as **two isolated, resilient services** — a deliberate architectural choice ensuring no single point of failure can halt the financial layer:
 
| Service | Role | What It Does |
|---|---|---|
| **Worker Bot** | The Silent Executor | Polls Twitter/Discord/Telegram, parses commands, evaluates with AI, executes CasualPay or MagicPay on-chain, logs everything |
| **VP-Social** | The Social Voice | Reads transaction logs, generates context-aware AI replies, posts verifiable confirmations back to the platform |
 
```
  User tweets "@monibot send $5 to @alice"
                    │
                    ▼
  ┌─────────────────────────────────────────┐
  │           WORKER BOT (Silent)           │
  │  1. Poll mentions                       │
  │  2. Parse: amount=$5, target=alice      │
  │  3. Resolve MoniTag → address           │
  │     If unregistered → MagicPay route    │
  │  4. Check allowance (CasualPay/MagicPay)│
  │  5. executeP2P() or executeCreate()     │
  │  6. Log to shared ledger                │
  └──────────────────┬──────────────────────┘
                     │
                     ▼
  ┌─────────────────────────────────────────┐
  │          VP-SOCIAL (Social Voice)       │
  │  1. Poll unreplied transactions         │
  │  2. Generate AI reply (sigma persona)   │
  │  3. Post reply to platform              │
  │  4. Mark transaction as replied         │
  └─────────────────────────────────────────┘
```
 
---
 
## Supported Networks
 
MoniBot operates natively across **five chains**, with intelligent routing and cross-chain fallback:
 
| Chain | Token | Symbol | CasualPay Router | MagicPay (IOURegistry) |
|---|---|---|---|---|
| **Base** | USDC | USDC | `0xBEE37c2f3Ce9a48D498FC0D47629a1E10356A516` | `0x1945c633659Ae71991aE37eE2Bdfe64E00514650` |
| **BNB Smart Chain** | USDT | USDT | `0x9eed16952d734dfc84b7c4e75e9a3228b42d832e` | `0xF602b559eE5c51ED122F667d101be105d9eDf90d` |
| **Celo** | USDT | USDT | `0x2a6Ff7552F296A8C5e8688FbA32685E73e138B9e` | `0x6bB3C64C382fcF8fB65b24234C455bB62b155742` |
| **Ink** | USDT0 | USDT0 | `0x046875a42B8F79E72349d38CB8225cbd6d24C7c5` | `0xD294Ecaa25f9122FD3e16014D2f4923fEf874a08` |
| **Solana** | USDC | USDC | SPL Token Relay | — *(MagicPay is EVM-only)* |
| **Tempo** *(testnet)* | αUSD | αUSD | `0x78A824fDE7Ee3E69B2e2Ee52d1136EECD76749fc` | — |
 
**Network routing logic:**
- Default chain: **Base (USDC)**
- BSC: triggered by keywords `usdt`, `bnb`, `bsc`, `binance`
- Celo: triggered by `on celo`, `celo`, `minipay`
- Ink: triggered by `on ink`, `ink chain`, `inkonchain`
- Solana: triggered by `on solana`, `solana`, `sol`
- **Cross-chain fallback:** If the sender's primary chain has insufficient balance or allowance, MoniBot automatically rereroutes to the best available chain — silently, without asking.
---
 
## Agent Intelligence
 
### AI-Powered Grant Evaluation
 
For campaign grants, MoniBot uses **Gemini 2.0 Flash** to evaluate every reply against an engagement rubric. The AI doesn't just check eligibility — it **judges intent**:
 
| Grant Tier | Amount | Criteria |
|---|---|---|
| REJECT | $0.00 | Spam, bots, self-tagging, template replies |
| MINIMAL | $0.10 | Basic participation |
| STANDARD | $0.25 | Genuine engagement, new user bonus |
| QUALITY | $0.50 | Exceptional, thoughtful engagement |
| MAXIMUM | $1.00 | Outstanding contribution (rare) |
 
**Anti-Gaming Intelligence** actively defends against:
- Self-referral loops and copy-paste template farming
- Single emoji / low-effort replies
- Bot pattern detection
- Duplicate grant prevention via on-chain nonce + tweet ID guards
- Cross-platform claim deduplication
### Sigma Persona (VP-Social)
 
Every reply MoniBot posts is generated in the **Gen Alpha Sigma** voice — specific, actionable, and entertaining. Error replies include exact amounts, exact chain names, and exact fix instructions. No vague "something went wrong." Users always know what happened and precisely how to fix it.
 
---
 
## Smart Contracts
 
### MoniPay App Contracts
 
| Contract | Chain | Address |
|---|---|---|
| **MoniPayRouter** | Base | `0x4048d18F71E723647f83B61202362425C5a7D2c0` |
| **MoniPayRouter** | BSC | `0x557285AbC46038E898d90eB00943Ff42c4Fbcb54` |
| **Treasury** | All chains | `0xDC9B47551734bE984D7Aa2a365251E002f8FF2D7` |
 
### MoniBot Contracts
 
| Contract | Purpose | Chain | Address |
|---|---|---|---|
| **MoniBotRouter (CasualPay)** | P2P between registered users | Base | `0xBEE37c2f3Ce9a48D498FC0D47629a1E10356A516` |
| **MoniBotRouter (CasualPay)** | P2P between registered users | BSC | `0x9EED3cF32690FfFaD0b8BB44CaC65B3B801c832E` |
| **IOURegistry (MagicPay)** | Social escrow for unregistered | Base | `0x1945c633659Ae71991aE37eE2Bdfe64E00514650` |
| **IOURegistry (MagicPay)** | Social escrow for unregistered | BSC | `0xF602b559eE5c51ED122F667d101be105d9eDf90d` |
| **IOURegistry (MagicPay)** | Social escrow for unregistered | Celo | `0x6bB3C64C382fcF8fB65b24234C455bB62b155742` |
| **IOURegistry (MagicPay)** | Social escrow for unregistered | Ink | `0xD294Ecaa25f9122FD3e16014D2f4923fEf874a08` |
 
### Token Addresses
 
| Token | Chain | Address |
|---|---|---|
| **USDC** | Base | `0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913` |
| **USDT** | BSC | `0x55d398326f99059fF775485246999027B3197955` |
| **USDT** | Celo | `0x48065fbBE25f71C9282ddf5e1cD6D6A887483D5e` |
| **USDT0** | Ink | `0x0200C29006150606B650577BBE7B6248F58470c1` |
| **USDC** | Solana | `EPjFWdd5AufqnvUePlk4kJ2d8c1gb2cpEH43t1YpTrW` |
| **αUSD** | Tempo | `0x20c0000000000000000000000000000000000001` |
 
---
 
## Use Cases
 
### 🏪 For Merchants — The $0 POS Terminal
 
Any smartphone becomes a point-of-sale system. No hardware. No merchant account applications. No 30-day settlement windows.
 
- **Quick Add Grid** — Pin top products for one-tap checkout
- **QR Scan-to-Pay** — Customer scans, confirms amount, done
- **Product Catalog** — Full inventory management with categories
- **Analytics Dashboard** — Revenue tracking, customer insights, transaction history
- **Invoice System** — Send invoices by MoniTag, track payment status
- **Customer CRM** — Track repeat buyers, total spend, purchase history
### 💸 For Users (P2P) — Money as Message
 
Send money like sending a text. Type a MoniTag, enter an amount, confirm with PIN.
 
- **Gasless transfers** — Sign a message, we handle the rest
- **QR payments** — Scan any MoniPay QR to pay instantly
- **Fund via exchange** — Copy your address, send USDC/USDT from Binance/Bybit *(the last time you will ever interact with a hexadecimal address)*
- **Transaction receipts** — Full history with on-chain verification links
- **Multi-chain support** — Base · BSC · Celo · Ink · Solana
### 🤖 For Social Commerce (MoniBot) — Finance as Conversation
 
Money that moves through conversation. Tweet, message, or post a command — the agent executes.
 
**Platforms:** X (Twitter) · Discord · Telegram
 
**CasualPay commands (registered users):**
- `@monibot send $5 to @alice` — P2P transfer
- `@monibot pay @bob $10 on bsc` — Cross-chain routing
- `@monibot send $1 each to @alice, @bob, @charlie` — Multi-recipient batch
- `@monibot send $5 to @alice in 30mins` — Scheduled transaction
**MagicPay commands (anyone, registered or not):**
- `@monibot send $10 to @jade` — If @jade is unregistered, funds go to escrow
- @jade signs up → funds release automatically
- 180-day claim window — funds return to sender if unclaimed
**Campaign Grants:**
- AI-evaluated airdrops for community engagement
- Anti-gaming protection built in
- Autonomous execution, no human approval needed
### 🔗 For Developers — Stripe for Stablecoins
 
Full payment gateway with hosted checkout, webhooks, and embeddable links.
 
- **API Keys** — `pk_live_` / `sk_live_` key pairs
- **Payment Links** — Shareable `monipay.xyz/pay/pl_abc123` URLs
- **Hosted Checkout** — Redirect customers to our branded checkout page
- **Webhooks** — HMAC-SHA256 signed `payment.completed` events
- **Order Tracking** — Full lifecycle: pending → completed → webhook sent
---
 
## Technology Stack
 
| Layer | Technology |
|---|---|
| **Frontend** | React 18 · TypeScript · Vite · TailwindCSS · Framer Motion · Radix UI · TanStack Query |
| **Blockchain** | Base · BNB Smart Chain · Celo · Ink · Solana · USDC/USDT · viem · wagmi · EIP-712 · ERC-2771 |
| **Backend** | Supabase (PostgreSQL + Edge Functions + RLS) · Deno Runtime |
| **Mobile** | CapacitorJS · PWA · WebAuthn Biometrics |
| **AI** | Gemini 2.0 Flash · Context-aware social reply generation · Campaign grant evaluation · Admin operational chat |
| **Bot Infrastructure** | Railway · Node.js · twitter-api-v2 · discord.js · node-telegram-bot-api · Isolated multi-chain services |
 
---
 
## Architecture: The Walkaway Guarantee
 
Every architectural decision in MoniPay is evaluated against one question: *"If we shut down tomorrow, what happens to users?"*
 
| Component | Custodial Risk | Walkaway Outcome |
|---|---|---|
| **User funds** | Non-custodial keys in device enclave | Accessible via any EVM wallet forever |
| **MoniTag identity** | On-chain registry | Readable from any block explorer |
| **CasualPay (Router)** | Executor is whitelisted bot, not custodian | Users can always transfer directly |
| **MagicPay (IOURegistry)** | Escrow is trustless smart contract | 180-day auto-return to sender on-chain |
| **Transaction history** | On-chain + Supabase | On-chain copy is permanent |
 
The protocol is the product. The app is just the interface.
 
---
 
## Related Repositories
 
| Repo | Description |
|---|---|
| [MoniBot Worker (Base)](https://github.com/Monipay/monibot) | Base chain silent executor |
| [MoniBot VP-Social](https://github.com/Monipay/monibot-vp-social) | AI reply engine |
| [MoniBot BSC Worker](https://github.com/Monipay/monibot-BSC) | BSC chain executor |
| [MoniBot BSC Reply Bot](https://github.com/Monipay/reply-bot-bsc) | BSC social reply agent |
| [MoniBotRouter — Base](https://basescan.org/address/0xBEE37c2f3Ce9a48D498FC0D47629a1E10356A516) | CasualPay contract |
| [IOURegistry — Base](https://basescan.org/address/0x1945c633659Ae71991aE37eE2Bdfe64E00514650) | MagicPay contract |
 
---
 
## Links
 
- 🌐 **App**: [monipay.xyz](https://monipay.xyz)
- 🐦 **MoniPay on X**: [@monipay_xyz](https://x.com/monipay_xyz)
- 🤖 **MoniBot on X**: [@monibot](https://x.com/monibot)
- 💬 **Discord**: [MoniPay Community](https://discord.gg/monipay)
- ✈️ **Telegram**: [MoniPay Official](https://t.me/monipaybot)
---
 
<p align="center">
  <strong>Built with 💙🖤💛💜💚  on Base · BSC · Celo · Ink · Solana</strong><br/>
  <em>"What if money didn't wait for you to move it?"</em><br/><br/>
  <strong>MoniPay is a Hammer. Not a Dishwasher.</strong>
</p>
 

---

