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
> *I am building MoniPay because I believe the original vision — **the tool you own, not the service you rent** — is the only one worth building.*

---

## The Walkaway Test

Vitalik Buterin draws a distinction between a **"Hammer"** and a **"Corposlop AI Dishwasher."**

The Dishwasher requires a subscription, spies on you, and stops working if the vendor goes offline.

The Hammer is a tool. You buy it once. It is yours. It works forever.

Most modern Fintech and Crypto payment gateways are **Dishwashers**. They are rent-seeking intermediaries that can deplatform you at a keystroke.

**MoniPay is a Hammer.**

It is a decentralized, terminal-free POS protocol designed to pass the "Walkaway Test." Even if MoniPay the entity disappears:
- Your funds remain accessible
- Your keys remain in your local enclave
- The protocol remains on-chain

I am not building a bank. I am building the **Uniswap of Payments**: Permissionless, Neutral, and Immutable.

---

## What is MoniPay?

MoniPay is a **decentralized, terminal-free micropayment rail** on Base & BNB Smart Chain. We are solving the "last mile" adoption problem in emerging markets by replacing expensive POS hardware and hexadecimal addresses with an **"invisible" non-custodial wallet** and human-readable social identities (**MoniTags**).

Our architecture merges financial infrastructure with **Agentic Commerce** via MoniBot — an autonomous AI agent that verifies social identity and executes gasless, on-chain transactions directly on social timelines. By utilizing a custom EIP-712 Relayer and local AES-GCM encryption, we've abstracted gas fees and seed phrases into a seamless, neobank-like experience.

- **No gas fees.** Ever. For anyone.
- **No wallet extensions.** No MetaMask popups. No seed phrases shown.
- **No blockchain literacy required.** Users see MoniTags, PINs, and instant confirmations.
- **No hardware terminals.** Any smartphone is a POS.
- **1% flat fee.** That's it. No hidden charges, no tiered pricing, no monthly subscriptions.

Built as a Progressive Web App with native mobile deployment via CapacitorJS, MoniPay works everywhere — from a street vendor's phone in Lagos to a checkout page in London.

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

---

## MoniBot: The Autonomous Financial Agent

> *"What if money didn't wait for you to move it?"*

**MoniBot** ([@monibot](https://x.com/monibot)) is not a chatbot with a wallet. It is a **financial agent** — an autonomous entity that lives on Twitter, thinks with Gemini, and executes on-chain. It represents a fundamentally new category: **Agentic Commerce**, where AI doesn't just assist transactions but *initiates, evaluates, and completes them* without human intermediation.

This is the **Request → Intent → Execution** loop:

```
"@monibot send $5 to @alice"     ← Natural language intent
         ↓
   AI parses, validates,          ← Agent reasoning (Gemini 2.0 Flash)
   checks balances, allowances
         ↓
   Router.executeP2P()            ← Atomic on-chain execution
         ↓
   "✅ Sent! tx: 0xab3f..."      ← Verifiable confirmation
```

No app opened. No button clicked. No gas paid. Just a sentence, and value moved.

### The Two-Bot System

MoniBot operates as **two isolated, resilient services** — a deliberate architectural choice ensuring no single point of failure can halt the financial layer:

| Service | Role | What It Does |
|---------|------|------------|
| **Worker Bot** | The Silent Executor | Polls Twitter, parses commands, evaluates with AI, executes on-chain via MoniBotRouter, logs everything |
| **Reply Bot** | The Social Voice | Reads transaction logs, generates context-aware AI replies, posts verifiable confirmations to Twitter |

```
  User tweets "@monibot send $5 to @alice"
                    │
                    ▼
  ┌─────────────────────────────────────┐
  │         WORKER BOT (Silent)         │
  │  1. Poll Twitter API               │
  │  2. Parse: amount=$5, target=alice  │
  │  3. Resolve MoniTags → addresses    │
  │  4. Check allowance to Router       │
  │  5. executeP2P() on-chain           │
  │  6. Log to shared ledger            │
  └──────────────────┬──────────────────┘
                     │
                     ▼
  ┌─────────────────────────────────────┐
  │       REPLY BOT (Social Voice)      │
  │  1. Poll unreplied transactions     │
  │  2. Generate AI reply (Gemini,      │
  │     context: chain, token, amount)  │
  │  3. Post to Twitter                 │
  │  4. Mark as replied                 │
  └─────────────────────────────────────┘
```

Each chain (Base and BSC) runs its own isolated worker and reply service on Railway — total redundancy across networks. The bot services operate in **Silent Mode**, logging transactions to a shared Supabase ledger for the social agents to process asynchronously.

### Agent Intelligence: AI-Powered Grant Evaluation

MoniBot uses **Gemini 2.0 Flash** for real-time decision-making. The AI doesn't just check eligibility — it **judges intent**. For campaign grants, every reply is evaluated against a rubric that rewards genuine engagement and penalizes gaming:

| Grant Tier | Amount | Criteria |
|------------|--------|----------|
| REJECT | $0.00 | Spam, bots, self-tagging, template replies |
| MINIMAL | $0.10 | Basic participation |
| STANDARD | $0.25 | Genuine engagement, new user bonus |
| QUALITY | $0.50 | Exceptional, thoughtful engagement |
| MAXIMUM | $1.00 | Outstanding contribution (rare) |

**Anti-Gaming Intelligence** — The AI actively defends against:
- Self-referral loops and copy-paste template farming
- Single emoji/word low-effort replies
- Bot pattern detection and repeat claiming
- Duplicate grant prevention via on-chain nonce + tweet ID guards

### Multi-Chain Execution

MoniBot operates natively on **two chains simultaneously**, with intelligent routing:

| Aspect | Base | BSC |
|--------|------|-----|
| **Token** | USDC (6 decimals) | USDT (18 decimals) |
| **Router** | `0xBEE37c...` | `0x9EED3c...` |
| **Campaign Routing** | `WHERE network = 'base'` | `WHERE network = 'bsc'` |
| **P2P Routing** | Default (no keywords) | Keywords: `usdt`, `bnb`, `bsc` |

**Why two routing strategies?** Campaigns have a known network at creation — route via database. P2P commands are ad-hoc tweets — route via keyword detection. Simple. Robust. No ambiguity.

### MoniBotRouter: On-Chain Verified Execution

The **MoniBotRouter** replaces direct `transferFrom` calls with atomic, on-chain-verified operations:

```
Before (v1):                    After (v2 — Router):
─────────────                   ────────────────────
Bot calls transferFrom          Bot calls Router.executeP2P()
Users approve bot wallet        Users approve Router contract
Two separate transactions       Single atomic transaction
Off-chain dedup only            On-chain nonce + tweet ID guard
Fee logic in bot code           Fee logic in contract
```

**On-chain security features:**
- Nonce-based replay protection
- Tweet ID stored on-chain (prevents double-execution)
- Campaign + recipient tracking (prevents duplicate grants)
- Hardcoded treasury address
- Maximum fee cap (500 bps)
- Executor whitelist (only authorized bot wallets)

### MoniBot Admin Dashboard

The admin interface features an **AI-powered operational chat** (Gemini-backed) following a hybrid control model:
- **Autonomous**: Real-time performance insights, campaign analytics, transaction monitoring
- **Admin-gated**: High-risk operations (budget allocation, campaign creation) require human confirmation
- **Explainable**: The AI provides clear intent, rationale, and timing for every proposed action

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
- **Fund via exchange** — Copy your address, send USDC/USDT from Binance/Bybit
- **Transaction receipts** — Full history with on-chain verification links
- **Multi-chain support** — Switch between Base (USDC) and BSC (USDT) in settings

### 🤖 For Social Commerce (MoniBot) — Finance as Conversation

Money that moves through conversation. Tweet a command, the agent executes.

- `@monibot send $5 to @alice` — P2P transfer via Twitter
- `@monibot pay @bob $10 on bsc` — Cross-chain routing via keywords
- **Campaign Grants** — AI-evaluated airdrops for community engagement
- **Multi-recipient transfers** — One command, multiple payouts
- **Scheduled transactions** — Set it and forget it
- **Social identity verification** — Link your MoniTag to your X account

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
|-------|-----------|
| **Frontend** | React 18 · TypeScript · Vite · TailwindCSS · Framer Motion · Radix UI · TanStack Query |
| **Blockchain** | Base · BNB Smart Chain · USDC/USDT · viem · wagmi · EIP-712 · ERC-2771 Meta-Transactions |
| **Backend** | Supabase (PostgreSQL + Edge Functions + RLS) · Deno Runtime |
| **Mobile** | CapacitorJS · PWA · WebAuthn Biometrics |
| **AI** | Gemini 2.0 Flash · Context-aware social reply generation · Campaign grant evaluation · Admin operational chat |
| **Bot Infrastructure** | Railway · Node.js · twitter-api-v2 · Isolated multi-chain services |

---

## Smart Contracts

| Contract | Chain | Address |
|----------|-------|---------|
| **MoniPayRouter** | Base | `0x4048d18F71E723647f83B61202362425C5a7D2c0` |
| **MoniPayRouter** | BSC | `0x557285AbC46038E898d90eB00943Ff42c4Fbcb54` |
| **MoniBotRouter** | Base | `0xBEE37c2f3Ce9a48D498FC0D47629a1E10356A516` |
| **MoniBotRouter** | BSC | `0x9EED3cF32690FfFaD0b8BB44CaC65B3B801c832E` |
| **USDC** | Base | `0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913` |
| **USDT** | BSC | `0x55d398326f99059fF775485246999027B3197955` |
| **Treasury** | All | `0xDC9B47551734bE984D7Aa2a365251E002f8FF2D7` |

---

## Repository Structure

```
monipay/
├── src/                          # React frontend (PWA)
│   ├── components/               # UI components
│   ├── contexts/                 # MoniTag global state
│   ├── hooks/                    # Custom React hooks
│   ├── lib/                      # Wallet, crypto, contract ABIs
│   └── pages/                    # Route pages
├── supabase/
│   └── functions/                # Edge Functions (Deno)
│       ├── relay-payment/        # Gasless payment relayer
│       ├── check-paytag/         # Auth & registration
│       ├── monibot-ai/           # AI reply generation
│       ├── monibot-chat/         # Admin AI chat
│       └── ...                   # Products, orders, invoices, etc.
├── contracts/                    # Solidity smart contracts
│   ├── MoniPayRouter.sol         # Base payment router
│   ├── MoniPayRouter.bsc.sol     # BSC payment router
│   └── MoniBotRouter.sol         # Bot execution router
├── worker-bot/                   # MoniBot Base worker (Node.js)
├── worker-bot-bsc/               # MoniBot BSC worker (Node.js)
├── vp-social/                    # MoniBot social reply agent (Base)
└── reply-bot-bsc/                # MoniBot social reply agent (BSC)
```

---

## Related Repositories

| Repo | Description |
|------|-------------|
| [MoniBot BSC Worker](https://github.com/Monipay/monibot-BSC) | BSC chain transaction executor |
| [MoniBot BSC Reply Bot](https://github.com/Monipay/reply-bot-bsc) | BSC social reply agent |
| [MoniBotRouter (Base)](https://basescan.org/address/0xBEE37c2f3Ce9a48D498FC0D47629a1E10356A516) | On-chain contract |
| [MoniBotRouter (BSC)](https://bscscan.com/address/0x9EED3cF32690FfFaD0b8BB44CaC65B3B801c832E) | On-chain contract |

---

## Getting Started

```bash
# Clone
git clone https://github.com/Monipay/monipay.git
cd monipay

# Install
npm install

# Dev server
npm run dev
```

**Environment:**
```env
VITE_SUPABASE_URL=your_supabase_url
VITE_SUPABASE_ANON_KEY=your_anon_key
```

---

## Links

- 🌐 **App**: [monipay.lovable.app](https://monipay.lovable.app)
- 🐦 **MoniPay**: [@monipay_xyz](https://x.com/monipay_xyz)
- 🤖 **MoniBot**: [@monibot](https://x.com/monibot)
- 📖 **Technical Docs**: [DOCUMENTATION.md](DOCUMENTATION.md)

---

<p align="center">
  <strong>Built with 💙 on Base & BNB Smart Chain</strong><br/>
  <em>"The best financial infrastructure is the one nobody notices."</em><br/><br/>
  <strong>MoniPay is a Hammer. Not a Dishwasher.</strong>
</p>
