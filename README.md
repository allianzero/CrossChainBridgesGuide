# CrossChainBridgesGuide

Cross-chain bridges explained: how they work and where they fail. A bridge moves assets between blockchains that cannot natively talk to each other. Bridges are essential infrastructure — and historically the most hacked part of crypto.

> This guide is educational only, not financial advice. Bridge hacks have caused billions of dollars in losses. Bridge only what you need, through established bridges, and understand the risks first.

## 1. Why bridges exist

Blockchains are separate systems. Bitcoin cannot natively send BTC to Ethereum, and Ethereum cannot natively send ETH to BNB Chain. Each chain has its own ledger, consensus, and rules, and none of them can read another chain's state directly.

Yet users want to move assets between chains: to use a cheaper chain, to access an app that only exists on another chain, or to trade assets that live elsewhere.

A bridge solves this by coordinating two sides: something happens on the source chain, and something corresponding happens on the destination chain. How that coordination is secured is the entire security question.

## 2. How bridges work: the lock-and-mint model

The most common design:

1. **Lock (source chain):** you send your asset to a bridge contract on the source chain, which locks it. The locked asset stays there, untouched, as backing.
2. **Proof:** the bridge's validators, oracles, or relayers observe the lock and attest that it happened.
3. **Mint (destination chain):** a contract on the destination chain mints a corresponding "wrapped" or "bridged" token to you, representing the locked asset. For example, locking ETH on Ethereum might mint "bridged ETH" on another chain.
4. **Return journey:** to go back, you burn the bridged token on the destination chain, the bridge observes the burn, and the original contract unlocks and returns your asset on the source chain.

Key insight: **bridged tokens are IOUs, not the original asset.** Their value depends entirely on the bridge honestly holding the locked backing and honouring redemptions. If the bridge is hacked and its backing is stolen, bridged tokens can become worthless even if both chains are perfectly fine.

## 3. Other bridge designs

### Native / canonical bridges
Built by a Layer 2 or chain team itself (for example, an L2's official bridge to its L1). Usually the most trustworthy option for that specific route, because the L2's own security model backs it.

### Third-party / general bridges
Independent protocols that connect many chains. Convenient, but each adds its own validators, contracts, and risks. A general bridge connecting 20 chains has 20 chains' worth of attack surface.

### Liquidity-pool bridges
Instead of locking and minting, these bridges hold liquidity pools on both chains and swap through them. You deposit on one side and receive from a pool on the other side. Faster, but limited by pool depth, and exposed to pool and slippage risks.

### Wrapped assets via custodians
A company holds the original asset (for example, BTC) and issues a token representing it on another chain (for example, WBTC on Ethereum). This is a bridge with a central custodian: you trust the company, its audits, and its reserves, not a smart contract alone.

### Atomic swaps and cross-chain messaging
More advanced designs let chains exchange assets or messages without a central bridge contract holding everything — for example, hash time-locked contracts (HTLCs) for direct swaps, or messaging protocols that let contracts on one chain trigger actions on another. These reduce, but do not eliminate, trust assumptions.

## 4. How bridges are secured — and where that fails

A bridge's security is only as strong as its weakest attestation mechanism. Common designs, weakest to strongest (roughly):

1. **Single multisig / small validator set:** a handful of signers attest to locks and burns. If a majority of signers are compromised or collude, the bridge can mint unlimited unbacked tokens. Several historic hacks were exactly this: stolen signer keys.
2. **Proof-of-stake validator set:** a larger, economically bonded set of validators attests. Stronger, but still vulnerable if the set is small, centralised, or if its own token (used for bonding) crashes.
3. **Optimistic verification:** anyone can challenge a bridge transaction during a waiting period, with fraud proofs. Stronger in theory, but requires active, honest challengers watching constantly.
4. **Zero-knowledge / light-client verification:** the destination chain cryptographically verifies the source chain's state (for example, by verifying block headers and proofs). Strongest design, but complex, expensive, and still maturing.
5. **L1-anchored (rollup bridges):** for a rollup, the bridge is part of the rollup's own settlement on the L1, inheriting L1 security. This is why L2 official bridges are generally safer than general third-party bridges.

**The pattern of historic bridge hacks:** compromised validator keys, bugs in the minting contract (allowing minting without a corresponding lock), faulty proof verification (accepting fake proofs), and compromised frontends or infrastructure. In each case, the attacker minted or withdrew assets that were never locked — creating money from nothing, at users' expense.

## 5. Risks, honestly listed

| Risk | What happens |
|---|---|
| Validator / signer compromise | Attacker mints unbacked tokens or drains locked assets |
| Smart contract bug | Minting without locking, or unlocking without burning |
| Proof verification flaw | Fake proofs accepted as real locks |
| Custodian failure (wrapped assets) | The company holding backing fails, freezes, or misstates reserves |
| Liquidity risk (pool bridges) | Not enough liquidity on the destination side; large transfers fail or suffer huge slippage |
| Chain reorganisation | A source chain reorg undoes a lock after the destination mint already happened |
| Governance capture | Bridge governance votes to change rules, fees, or validators maliciously |
| Frontend phishing | A fake bridge site steals your deposit directly |
| Regulatory / freezing risk | A centralised bridge or custodian freezes assets or stops operating in your region |

## 6. Bridge safety rules

1. **Prefer official bridges** for the specific route, especially L2 canonical bridges, over general third-party bridges.
2. **Bridge only what you need, when you need it.** Do not leave large amounts in bridged form longer than necessary, and do not treat a bridged token as equivalent to the original asset for large holdings.
3. **Check the destination token address.** Bridged tokens have different addresses than native tokens, and fake bridged tokens are common. Get the address from the bridge's official documentation, not from a post.
4. **Start with a test transfer.** Bridge a tiny amount, confirm it arrives correctly on the destination chain, then bridge the rest.
5. **Check fees and time honestly.** Bridge fees, gas on both chains, and waiting periods (especially optimistic designs) can make small transfers uneconomical. Add all costs before confirming.
6. **Verify the bridge's security model.** How many validators? Who are they? Is there an audit? Has the bridge operated through a crisis? If you cannot find answers, do not use it for meaningful amounts.
7. **Never bridge from a link in a DM, post, or ad.** Use the project's official site, bookmarked by you.
8. **Understand withdrawal times.** Some bridges impose delays (for fraud-proof windows or liquidity). Plan exits before you need funds urgently.

## 7. What to do if a bridge you used is hacked

1. **Stop using it immediately.** Do not bridge more funds in, hoping to "average" or recover.
2. **Check whether your bridged tokens still have backing.** If backing was stolen, bridged tokens may trade far below the original asset's price, or become worthless. Selling quickly may recover something; holding may recover nothing. There is no guaranteed right answer — assess the specific incident from official sources.
3. **Revoke approvals** for the bridge contract.
4. **Move unaffected funds** to a fresh wallet if you suspect compromise.
5. **Follow only official announcements.** Ignore DMs offering recovery, refunds, or "new bridge" links — these are second scams targeting hack victims.

## 8. The future: are bridges getting safer?

Slowly, in specific ways:

- **Light-client and ZK bridges** reduce trust in validator sets by verifying chain state cryptographically. They are complex and still maturing, but they are the most promising direction.
- **Shared security models** (where a bridge inherits a large chain's validator set) reduce the "small committee" problem.
- **Rate limits and circuit breakers** cap how much can be withdrawn in a period, limiting hack damage — at the cost of slower legitimate withdrawals during demand spikes.
- **Better audits and bug bounties** for bridge contracts specifically, after years of expensive lessons.

None of this makes bridges safe today. It makes the best bridges safer than the worst ones were — which is still a meaningful distinction when choosing where to put your funds.

## 9. Frequently asked questions

**Is a bridged token the same as the original?**
No. It is a representation, backed by locked originals held by the bridge. Its value depends on the bridge's honesty and security.

**Can I bridge any token to any chain?**
No. Bridges support specific tokens and routes. A token that exists natively on one chain may not exist, or may exist as a different bridged token, on another.

**Why do bridge transfers sometimes take hours or days?**
Waiting periods for fraud proofs, liquidity availability, validator confirmation thresholds, and congestion on either chain all add time. Optimistic designs deliberately wait (often days for finality on the source side in some designs) to allow challenges.

**Are centralised exchange transfers a safer alternative to bridges?**
Often, for simple moves, yes: deposit on an exchange on one chain and withdraw on another chain, letting the exchange handle the cross-chain movement internally. This replaces bridge risk with exchange custody risk — the exchange holds your funds during the transfer and can fail, freeze, or delay. For large amounts, neither option is risk-free; compare the specific risks.

> Educational content only. This is not financial, investment, or tax advice.
