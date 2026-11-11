# Glossary

Terms used in these documents. Each entry has one meaning. §5 lists words that have another meaning in MetaLeX's plan or in Armada, with the rule this repo follows.

Sources: `mlx` is MetaLeX's plan, *Build MetaLeX's private chain with recovery* (evidence as of 8 November 2026). `design` is `system-design.md`. Armada's own glossary covers the wider Armada stack.

## 1. MetaLeX terms

These are MetaLeX's terms. The definitions follow `mlx`.

| Term | Meaning |
|---|---|
| **Cybercorps** | MetaLeX's EVM protocol for companies, instruments, agreements and payments. Its contracts run unchanged in this design. |
| **CyberCorp** | One company deployed through `CyberCorpFactory`, with its officers, managers and component pointers |
| **BorgAuth** | The authority contract of a CyberCorp: roles, thresholds and adapters. Corporate authority lives here. |
| **IssuanceManager** | The contract that manages a company's instrument classes, LET and scrip beacons, and the scrip mint and burn |
| **LET** (Ledger Entry Token) | An ERC721 record of a security position. It keeps **custody** (the token owner) and the registered **legal owner** separate, with units, restrictions and endorsements. |
| **Legal owner** | The registered owner of a LET. A custody transfer alone does not change it. |
| **Endorsement floor** | The point in a LET's endorsement history below which endorsements are no longer actionable |
| **CyberScrip** (scrip) | The fungible form of LET units. `scripifyCert` turns LET units into scrip, and `convertScripToCert` (recertification) turns scrip back. |
| **CyberAgreementRegistry** (cyberSign) | The registry of agreements and their EIP-712 signatures |
| **DealManager, RoundManager** (cyberRAISE) | The contracts for deals, rounds, expressions of interest and allocations |
| **LexScrow** (LeXscroW) | MetaLeX's escrow contract |
| **MetaVesT** | MetaLeX's grant and vesting contracts. Not in `mlx`, and not in the contracts at the commit this design cites. |
| **BORG** | A Safe with MetaLeX's guard, whose owners are its BORG members. Not in `mlx`, and not in the contracts at the commit this design cites. |
| **Issuer** | A company that issues instruments through Cybercorps |
| **Holder** | A party that holds MetaLeX assets of an issuer |
| **Recovery operator** | The independent operator that `mlx` names to hold complete recoverable state |
| **Prividium, Prividium Core, Commander** | The ZKsync permissioned-validium product that `mlx` selects, its open-source permissioning engine, and its commercial control plane |

## 2. Terms of this design

| Term | Meaning |
|---|---|
| **MetaLeX assets** | Everything a holder can have in the Cybercorps contracts: LETs, CyberScrip balances, agreements, round allocations and MetaVesT grants. `mlx` has no single term. |
| **Issuer RootChain** | The L1 contract of one issuer. It stores the hard-final roots of that issuer's state, one successor per root. |
| **Issuer state** | One EVM state per issuer: its Cybercorps company graph, its channel-balance contract and its payout records. Watcher members execute it. Every asset in it is one that the issuer's watcher party issues. |
| **Watcher party** | An entity: the threshold signer for one issuer. It runs the issuer state and anchors it to L1 through the issuer's RootChain. Through its custodian, it is an armada-nitro participant and the minter of the issuer's wrapper. One per issuer. |
| **Watcher member** | One participant in a watcher party: MetaLeX core, the recovery operator or the issuer. Each runs its own watcher. |
| **MetaLeX core** | The MetaLeX hub in its role as a watcher member of every party (R-6). The default sequencer. |
| **Party key** | One FROST threshold key per watcher party, with a two-thirds-majority threshold. Its threshold never needs MetaLeX's share. `HubCustodian` is its on-chain identity. |
| **Batch** | An ordered group of transactions that one root covers |
| **Batch commitment** | A hash over a batch. The party key signs it as one field of the root. |
| **Hard-final** | A batch whose root the RootChain accepted. Only hard-final batches affect anything outside the issuer state. |
| **Discarded** | An executed batch whose root lost to a different successor of the same root |
| **Live, Retired** | The two states of a RootChain. It enters Retired when a departure is finalized, and then accepts no roots. |
| **Events root** | A per-batch Merkle root over all logs. Holders prove logs against it. |
| **Blinded tree data** | The blob content of each root: the salted position and salted leaf of each slot changed since the previous root. It binds the state and reveals nothing about it. Anyone with the archive rebuilds the tree from it. |
| **Position salt, value salt** | The secret salts that blind a slot's tree position and its value. A holder receives its own through the party RPC. |
| **Party RPC** | A watcher party's own private, authenticated RPC, in MetaLeX's sense. The only interface to an issuer state. Every method is scoped by the contracts' authority. It gives holders their records, salts and proofs. |
| **MetaLeX hub** | An armada-nitro hub. An entity distinct from MetaLeX, the company (R-27), through which MetaLeX is a plaintext audience. It routes Nitro payments, runs watchtowers for website users, and supplies user IDs to each issuer state. It is not in the path of a user's request to the party RPC. |
| **Self-help** | A holder proving its own records against any root from its salts, its values and the public blob data, with no watcher member |
| **Party custodian** | The watcher party's `HubCustodian`, a smart wallet. It signs the watcher party's channel states and wrapper mints with the FROST party key. |
| **Channel-balance contract** | A system contract in an issuer state with an ERC20 interface, and the `paymentToken` of every deal. Each balance is USDC that a holder committed to the watcher party in armada-nitro and has not withdrawn. The watcher party updates it from co-signed states it receives over Nitro p2p. |
| **Verifier tool** | A tool that reads a root and the wrapper's supply from L1, and checks the conservation proof against them |
| **Checkpoint** | Recording a channel's latest co-signed state on the adjudicator, as in any Nitro channel |
| **Watchtower** | A service that answers a stale-state challenge on a user's channel within the challenge window, with the user's latest co-signed state |
| **Payout record** | The issuer-state record of a paid withdrawal: the withdrawal, the `channelId` and the hash of the co-signed channel state that paid it |
| **Pay in, withdraw** | A holder pays in with a co-signed armada-nitro state that moves USDC to the watcher party's allocation, and its channel balance is credited. It withdraws with its own transaction, and the watcher party pays it through armada-nitro once that batch is hard final. |
| **Routed payment** | One Nitro payment across several channels, through a hub, made atomic by guarantees and reclaim. The hub advances no value (Armada ADR-0021). |
| **Scrip lock** | Canonical scrip locked in the issuer state against wrapper notes outside it. States: Locked, Wrapped, Returned, Unlocked. |
| **Issuer wrapper** | An ERC20 on L1, one per issuer, whose only minter is the party custodian. It is held as notes in the Armada pool. |
| **Wrapper factory** | The L1 factory that deploys each issuer's wrapper. A hub may name it in its asset list. |
| **Conservation proof** | An off-chain zero-knowledge proof for each hard-final root: locked scrip equals wrapper supply plus pending locks and returns. The USDC needs no such proof, because it never leaves armada-nitro. |
| **Entry guard** | The rule that a scrip lock reverts unless the asset's force features are off and its implementation hash is the pinned one |
| **Daily aggregation** | MetaLeX core's default publishing run: one blob transaction per day, with all parties' data packed by size. It calls the aggregator with a Merkle root of the day's party roots. |
| **Daily aggregator** | The L1 contract that records the day's root of party roots and the hashes of published blobs |
| **Party bond** | One armada-nitro channel per watcher party, funded by its watcher members, slashable on proven equivocation |
| **Slashing arbiter** | An EIP-1271 participant in the bond channel that signs only states carrying a valid fault proof |
| **`lib/`** | The Nim library behind the harness and the WASM holder verifier: the JMT, the events tree and the batch commitment |
| **Harness** | The watcher member software around the embedded nimbus EVM: sequencing, history, the read scope and the call-trace inspector |
| **I-n, A-n, D-n** | Invariant IDs: from `mlx` (I-1 to I-65), the operator's (A-n), and this design's own (D-n) |

## 3. Proof and data terms

| Term | Meaning |
|---|---|
| **zkVM** | A virtual machine whose execution can be proven |
| **zkEVM** | An EVM state transition proven in a zkVM. Out of scope in this design, because of its operating cost (`design` §11). |
| **ZisK** | An RV64IMA zkVM. Release `v1.4.0-alpha` at the time of writing, with audits ongoing. |
| **RISC Zero** | An RV32IM zkVM. Release 3.0. |
| **nimbus EVM** | The EVM of `status-im/nimbus-eth1`, written in Nim. It builds natively, for iOS and Android, and as WASM. The harness runs it against an in-memory ledger. |
| **JMT** (Jellyfish Merkle Tree) | A 256-bit binary sparse Merkle tree in which empty and single-leaf subtrees collapse. It proves inclusion and non-inclusion. |
| **Inclusion proof, non-inclusion proof** | A proof that a key has a given value under a root, or that a key is absent |
| **Blob** | 128 KiB of data that a transaction carries. Ethereum keeps it about 18 days. |
| **Light client** | A client that checks chain data against block headers without trusting an RPC. Armada's is the nimbus verified proxy (Armada T1). |
| **EIP-1271** | The standard by which a contract validates a signature |
| **FROST** | A threshold Schnorr signature scheme. armada-nitro's `HubCustodian` verifies it. |

## 4. Armada terms used here

| Term | Meaning |
|---|---|
| **Armada pool** | Armada's shielded pool. Value inside it exists only as notes. |
| **Note** | A shielded unit of value in the pool |
| **Shield, unshield** | Move value into the pool, or out of it |
| **armada-nitro** | Armada's state-channel adjudicator: ForceMove plus note-native custody, EIP-1271 participants and the deposit and payout boundary |
| **armada-nitro asset** | Value held as a note or in a note-funded armada-nitro channel. All value in this design is an armada-nitro asset. |
| **Hub** | A channel counterparty that routes payments. It cannot take funds and is trusted for liveness only. The MetaLeX hub is one, and an issuer may run one. |
| **ForceMove** | Nitro's dispute game: challenge, checkpoint, conclude. It honors only signed states. |
| **Design A** | Armada's default: amounts are public only at the pool and channel boundary |

## 5. Word collisions

| Word | Meanings elsewhere | Rule in this repo |
|---|---|---|
| **party** | An agreement party in `CyberAgreementRegistry` | "Watcher party" for the threshold signer. "Watcher member" for one participant. "Agreement party" for the contract sense. |
| **watcher party** | Armada: "a bonded federation of service providers serving one data need … not a BFT chain" | The same kind of federation. Its one data need is one issuer's RootChain. |
| **core** | Prividium Core, the open-source permissioning engine | "MetaLeX core" for the watcher member. "Prividium Core" for the product. |
| **escrow** | LexScrow, MetaLeX's escrow contract. The scrip bridge escrow of `mlx` §2. | "Escrow" means LexScrow only. Locked scrip is a "scrip lock". |
| **bridge** | The go-nitro mirrored L1↔L2 bridge. Armada's cross-chain swap design. A rollup's canonical bridge. `mlx` §2's scrip bridge to a public chain. | Not used. Value moves through the watcher party's channels, and scrip leaves as wrapper notes. |
| **L2, rollup, chain, instance** | The ZKsync and OP Stack chains discussed in `mlx` and Armada | "Issuer state" and "issuer RootChain". There is no shared chain. |
| **layer** | Armada bans it for its tiers (T0–T6) | Not used. "L1" means Ethereum only. |
| **encryption** | Earlier drafts of `design`: encrypted batch data in blobs | Not used for blob data. Blob data is blinded, not encrypted. |
| **attestation** | Earlier drafts of `design`: storage signatures by watcher members | Not used. A watcher member's signature share on a root follows its storage of the batch. |
| **fallback** | Any implicit second path on failure | Not used. Every path is a typed transition with a guard. |
| **finality** | Ethereum's own finality | "Hard-final" for a batch whose root L1 accepted |
| **recovery** | `mlx`: promotion of a warm standby operator | Any t watcher members continuing the RootChain. There is no promotion step. Holders' own recovery is "self-help". |
| **operator** | `mlx`: MetaLeX as primary operator, plus the recovery operator | "MetaLeX core" and "recovery operator" by name |
| **p2p, peer-to-peer** | A separate peer-to-peer messaging transport | Delivery to holders is through the party RPC |
| **RPC** | An Ethereum node's JSON-RPC. Prividium's private RPC. | "Party RPC" for the watcher party's own endpoint. A light client does not trust any RPC. |
