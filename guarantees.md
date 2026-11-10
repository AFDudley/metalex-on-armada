# Guarantees by party

Status: draft, 2026-11-09.

For each party: what it can check itself, what it must trust, and what happens to it when another party fails. Section numbers refer to `system-design.md`. Terms follow `glossary.md`. The zkEVM is out of scope (§11), so every statement below holds with roots accepted on the party signature alone.

## 1. The parties

| Party | What it is |
|---|---|
| **Holder** | A buyer or other counterparty that holds MetaLeX assets or payment token of an issuer |
| **Issuer** | The company. It is a watcher member of its own watcher party, and it receives payments through `companyPayable()`. |
| **Watcher party** | An entity: the threshold signer for one issuer. Its party key needs a two-thirds majority of its n watcher members, with n = 3 and t = 2 initially. Through its custodian it is an armada-nitro participant and the wrapper's minter (§4.1). |
| **MetaLeX hub** | An entity distinct from MetaLeX. An armada-nitro hub that routes payments and runs watchtowers for website users (§9). As MetaLeX core, it is a watcher member of every party (R-6). |
| **MetaLeX** | The company. It receives the platform fee through `getPlatformPayable()`. It is a named plaintext audience through the MetaLeX hub (I-35, R-36). |
| **Recovery operator** | The independent watcher member that `mlx` names (§4.1) |
| **Wrapper holder** | A holder of the issuer's wrapper, as notes or unshielded (§10) |

## 2. What each party can check

| Party | Can check itself | How |
|---|---|---|
| Holder | Each of its records at each root | Inclusion and non-inclusion proofs against roots read through a light client (§8.2) |
| Holder | Any change to its records, even when the party RPC is silent | Its verifier checks every new root, and reads the blobs when no update arrives (§8.2) |
| Holder | That no change to its records is unexplained | Every change between consecutive roots matches a log in `eventsRoot` (§8.2) |
| Holder | The code that holds its records | The root commits to each contract's code hash (§8.2) |
| Holder | That its channel value is safe | It holds its latest co-signed channel state (§9.1) |
| Issuer | The full issuer state | It is a watcher member and stores the state (§4.3) |
| Issuer | Its payout | The payout record names the channel state that paid it, and its note is in the Armada pool (§9.2) |
| Wrapper holder | That its wrapper was minted against a lock | The watcher party's signed mint record and the lock in a hard-final root (§10) |
| Anyone | That the payment token and the wrapper are conserved at each root | The verifier tool: the conservation proof, against the published settlement-channel states, the adjudicator and the wrapper's supply on L1 (§11) |

## 3. What each party must trust

| Party | Must trust | For what | If that trust fails |
|---|---|---|---|
| Holder | The watcher party | Correct state transitions, and pay-out against a burn. With the zkEVM, L1 would enforce correct transitions instead (§11). | Holders detect the change. The signed root is evidence against the operators (§11). Value in its channel exits by ForceMove. |
| Holder | The sequencer | Inclusion of its writes | A payer whose write is refused gets no asset and has no L1 remedy. Its signed request and the refusal are its evidence to MetaLeX (§8.5). |
| Holder | A watchtower, while it is offline | An answer to a stale-state challenge on its channel | The MetaLeX hub runs one for website users, the recovery operator may run one, and the mobile app runs its own (§9.1) |
| Holder | A hub, the MetaLeX hub or an issuer's | Liveness of routed payments | It uses another hub, or a channel with the watcher party directly (§9.3) |
| Issuer | The other watcher members, acting together | Correct state transitions and pay-outs without the issuer's share, because t = 2 | The issuer detects the change, and the signed root is evidence against the operators (§11). The issuer also holds the full state as evidence. |
| Wrapper holder | The watcher party | That each mint reflects locked scrip, and that each lock is wrapped and each return unlocked. With the zkEVM, L1 would enforce correct mints instead (§11). | The conservation proof fails publicly for a mint with no lock. The signed lock, mint and return records are evidence against the operators (§11). |

A buyer's USDC stays in its own channel until settlement, and the watcher party cannot take it before then. It holds only the buyer's signed commitment, which it countersigns at settlement (§9.2). After settlement, the USDC is in the settlement channel, whose latest co-signed state is in each day's blob data. The issuer and MetaLeX hold payment token for it, and the watcher party's signatures bind it to pay out. Value in any channel exits by ForceMove with the latest co-signed state (§9.1). A routed payment is atomic, so no party has value in flight (§9.3).

## 4. Failures

Each row is one failure. Each cell states the effect of that failure on one party. "None" means the party is not affected.

| Failure | Effect on holders | Effect on the issuer | Effect on the MetaLeX hub | Effect on wrapper holders |
|---|---|---|---|---|
| The issuer stops | None. MetaLeX core and the recovery operator continue the RootChain. | The issuer is the party that stopped. | None | None |
| The recovery operator stops | None. MetaLeX core and the issuer continue the RootChain. | None | None | None |
| The MetaLeX hub stops | The issuer and the recovery operator continue the RootChain, so it advances only while the issuer's watcher member is up. Holders route through an issuer's hub, or use a channel with the watcher party directly. Website users lose the default watchtower. The recovery operator's watchtower, if it runs one, continues. | The issuer and the recovery operator continue the RootChain. The issuer is paid out through its own hub or a direct channel. | The hub is the party that stopped. | None |
| MetaLeX, the company, stops | None. The MetaLeX hub is a separate entity. | None | None | None |
| Fewer than t watcher members are live | No new roots and no pay-outs. Each holder can prove what it held at the last root (§8.3). Value in channels exits by ForceMove. Open commitments stay open. | No new roots and no pay-outs. The issuer holds the full state. | Its routes to that watcher party stop. | No new mints or unlocks. Existing wrapper notes are not affected. |
| The issuer, the MetaLeX hub and the recovery operator all stop | Self-help: each holder proves its records from its own store and the archived blobs against the last root (§8.3). Value in channels exits by ForceMove. | The issuer is one of the parties that stopped. | The hub is one of the parties that stopped. | No new mints or unlocks. Existing wrapper notes are not affected. |
| The sequencer refuses a write | The payer gets no asset and has no L1 remedy. Its signed request and the refusal are its evidence to MetaLeX (§8.5). | None | None | None |
| A lock is never wrapped, or a return is never unlocked | None | None | None | The holder has no L1 remedy. The signed lock and return records are its evidence (§5.4). |
| t watcher members sign a root that misaccounts. This is possible only without the zkEVM (§11). | Each holder detects it, and holds the signed root and its earlier proofs as evidence. Payment token with no commitment or settled USDC behind it fails the conservation proof (§11). | If the issuer did not sign, it holds the full state as evidence. | None. The hub only routes payments. | A wrapper mint with no locked scrip fails the conservation proof. |
| The party key signs two successors of one root. The party bond is slashed (§4.4). | None. The RootChain accepts one successor per root (§6.1). | None | None | None |
