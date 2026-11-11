# Guarantees by party

Status: draft, 2026-11-09.

For each party, this document states what it can check itself, what it must trust, and what happens to it when another party fails. Section numbers refer to `system-design.md`. Terms follow `glossary.md`.

The zkEVM is out of scope (§11). So every statement below holds with roots that L1 accepts on the party signature alone, with no validity proof.

## 1. The parties

- **Holder:** a buyer or other counterparty that holds an issuer's MetaLeX assets or a channel balance.
- **Issuer:** the company. It is a watcher member of its own watcher party, and it receives payments through `companyPayable()`.
- **Watcher party:** the threshold signer for one issuer. Its party key needs a two-thirds majority of its n watcher members, so n = 3 and t = 2 at the start. Through its custodian, it is an armada-nitro participant and the wrapper's minter (§4.1).
- **MetaLeX hub:** an armada-nitro hub that routes payments and runs watchtowers for website users (§9). It is an entity distinct from MetaLeX. As MetaLeX core, it is a watcher member of every party.
- **MetaLeX:** the company. It receives the platform fee through `getPlatformPayable()`. It is a named plaintext audience, through the MetaLeX hub.
- **Recovery operator:** the independent watcher member that `mlx` names (§4.1).
- **Holder of wrapped scrip:** anyone who holds an issuer's scrip in its wrapped form on Ethereum, as notes or unshielded (§10).

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
| Holder of wrapped scrip | That its wrapped scrip was minted against a lock | The watcher party's signed mint record and the lock in a hard-final root (§10) |
| Anyone | That the wrapper is conserved at each root | The verifier tool checks the conservation proof against the root and the supply of wrapped scrip on L1 (§11) |

## 3. What each party must trust

### Holder

- **The watcher party,** for correct state transitions and for payment of each withdrawal. If that trust fails, holders detect the change, and the signed root is evidence against the operators (§11). Value in the holder's channel exits by ForceMove. With the zkEVM, L1 would enforce correct transitions instead.
- **The sequencer,** to include its writes. A payer whose write is refused gets no asset and has no remedy on L1. Its signed request and the refusal are its evidence to MetaLeX (§8.5).
- **A watchtower,** while the holder is offline, to answer a stale-state challenge on its channel. The MetaLeX hub runs one for website users, the recovery operator may run one, and the mobile app runs its own (§9.1).
- **A hub,** the MetaLeX hub or an issuer's, for routed payments to stay live. If one hub stops, the holder uses another hub, or a channel with the watcher party directly (§9.3).

### Issuer

- **The other watcher members acting together,** for correct state transitions and payments without the issuer's share, because t = 2. If that trust fails, the issuer detects the change, and the signed root is evidence against the operators (§11). The issuer also holds the full state as evidence.

### Holder of wrapped scrip

- **The watcher party,** for three duties: each mint reflects locked scrip, each lock is wrapped, and each return is unlocked. A mint with no lock fails the conservation proof publicly. The signed lock, mint and return records are evidence against the operators (§11). With the zkEVM, L1 would enforce correct mints instead.

### Where the money is at each step

The USDC never leaves armada-nitro. Paying in moves it to the watcher party's allocation in the buyer's own channel, with a state that both sign (§9.2). From then on, the buyer holds a channel balance in the issuer state. The watcher party's signatures bind it to pay that balance out on withdrawal.

A holder withdraws with its own transaction. Once that batch is hard final, the watcher party pays it through armada-nitro, and the holder exits to a shielded note when it chooses.

Value in any channel exits by ForceMove with the latest co-signed state (§9.1). A routed payment is atomic, so no party has value in flight (§9.3).

## 4. Failures

Each subsection is one failure. Parties that the failure does not affect are listed last.

### The issuer stops

MetaLeX core and the recovery operator continue the RootChain. Holders, the MetaLeX hub and holders of wrapped scrip are not affected.

### The recovery operator stops

MetaLeX core and the issuer continue the RootChain. No other party is affected.

### The MetaLeX hub stops

- **The RootChain:** the issuer and the recovery operator continue it. So it advances only while the issuer's watcher member is up.
- **Holders:** they route through an issuer's hub, or use a channel with the watcher party directly. Website users lose the default watchtower. The recovery operator's watchtower continues, if it runs one.
- **The issuer:** it is paid out through its own hub or a direct channel.
- **Not affected:** holders of wrapped scrip.

### MetaLeX, the company, stops

No party is affected, because the MetaLeX hub is a separate entity.

### Fewer than t watcher members are live

- **Holders:** no new roots and no payments out. Each holder can prove what it held at the last root (§8.3). Value in channels exits by ForceMove. Balances stay in the issuer state.
- **The issuer:** no new roots and no payments out. The issuer holds the full state.
- **The MetaLeX hub:** its routes to that watcher party stop.
- **Holders of wrapped scrip:** no new mints or unlocks. Existing wrapped-scrip notes are not affected.

### The issuer, the MetaLeX hub and the recovery operator all stop

- **Holders:** they use self-help. Each holder proves its records from its own store and the archived blobs, against the last root (§8.3). Value in channels exits by ForceMove.
- **Holders of wrapped scrip:** no new mints or unlocks. Existing wrapped-scrip notes are not affected.

### The sequencer refuses a write

The payer gets no asset and has no remedy on L1. Its signed request and the refusal are its evidence to MetaLeX (§8.5). No other party is affected.

### A lock is never wrapped, or a return is never unlocked

The holder of wrapped scrip has no remedy on L1. The signed lock and return records are its evidence (§5.4). No other party is affected.

### t watcher members sign a root that misaccounts

This is possible only because the zkEVM is out of scope (§11).

- **Holders:** each holder detects it, and holds the signed root and its earlier proofs as evidence. A channel balance with no co-signed state behind it is visible to every watcher member, because each batch records the states it applies.
- **The issuer:** if it did not sign, it holds the full state as evidence.
- **Holders of wrapped scrip:** a wrapped-scrip mint with no locked scrip fails the conservation proof.
- **Not affected:** the MetaLeX hub, which only routes payments.

### The party key signs two successors of one root

The RootChain accepts only one successor per root, so no party is affected (§6.1). The party bond is slashed (§4.4).
