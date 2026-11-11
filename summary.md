# Summary

Status: draft, 2026-11-10. Not built.

The notes at the end map each part of this summary to the requirements it meets, group by group. Every group of requirements has a note.

## The proposal

MetaLeX wants to run its Cybercorps contracts privately, with an independent operator who can recover every issuer's records. MetaLeX's own plan does this with one shared private chain on ZKsync Prividium. This proposal does it differently, with the same contracts and no changes to them.[^protocol]

Each issuer gets its own EVM state. Three operators run it together: MetaLeX, an independent recovery operator, and the issuer. This group is the issuer's *watcher party*. Once a day, the watcher party posts a fingerprint of the state, its *root*, to a contract on Ethereum. Any two of the three operators can sign a root, so no single operator can stop an issuer or change its records alone.[^parties]

Operator keys run the system, but they hold no corporate authority. Corporate authority stays in the contracts, and no operator key can sign for a user.[^authority]

Money does not live in the private state. It stays on Ethereum in armada-nitro, Armada's payment system: shielded notes, and payment channels funded from notes. A buyer pays by a signed armada-nitro payment in its own channel. The private state sees that payment only as a balance, and every payee is paid out in armada-nitro.[^value]

## What MetaLeX and its users get

- **Privacy between issuers.** Each issuer's records are in a separate state. No issuer can read another issuer's data through any interface. A holder sees only its own records. MetaLeX and the recovery operator see everything, as MetaLeX's plan accepts.[^privacy]
- **Recovery without MetaLeX.** The recovery operator and the issuer hold the full state, and either can continue with the other. No promotion step and no MetaLeX signature is needed.[^recovery]
- **Verified self-help for holders.** Each holder can recover its own records and check them cryptographically against the roots on Ethereum, with no operator involved. It needs only its own stored records and the public blob data.[^selfhelp]
- **Safe money.** No operator can take a user's funds. A user can always exit a channel on Ethereum with its latest signed state.[^value]
- **Scrip on Ethereum.** A holder can lock scrip and receive notes of the issuer's wrapper token on Ethereum, one for one. MetaLeX's bridge accounting holds unchanged, and every lock, mint and return is a signed record.[^scrip][^bridge]
- **The issuer's own records.** The issuer is one of the operators. So it holds its full state and history, and it can export them without anyone's permission.[^records]
- **Departure.** An issuer can leave for a new set of operators. Its holders prove their records to the new operators.[^departure]
- **Any interface.** MetaLeX's web app needs no new transport. Any other interface gets the same standard endpoint, with the same scope and checks.[^parity]

## How it differs from Prividium

Prividium proves every batch with a zkEVM on GPU hardware. This proposal does not need one for a reasonable level of safety. It relies on three things instead:

- every root is a signed commitment that the operators cannot deny;
- each holder checks its own records against every root;
- a published conservation proof shows that no wrapper token exists without locked scrip behind it. Anyone can check it.

So a dishonest majority of operators could sign a wrong root, and Ethereum would accept it. But every holder would detect the change, and the signed root is evidence against those operators. Prividium's zkEVM would reject such a root. A zkEVM can be added later if MetaLeX wants that protection.[^trust]

Prividium keeps transaction data and state off chain, in the operators' databases. So a Prividium holder needs an operator to read or prove its records. In this proposal, a holder does not.[^data]

## What it costs

On the same basis as MetaLeX's own cost tables, this design costs about $4,400 per month. That includes $1,449 that the issuers pay for their own watcher members. Prividium's three scenarios cost $4,600, $7,700 and $11,300 per month. The cost comparison gives every line.

## What MetaLeX must trust

- **The operators,** for correct records. A wrong record is detectable and provable, but Ethereum does not block it.
- **The sequencer,** to accept writes. A refused write has no remedy on Ethereum. The user's evidence goes to MetaLeX.
- **A watchtower,** to protect an offline user's channel. The MetaLeX hub runs one for website users.[^limits]

## What MetaLeX must decide

1. Which companies share one issuer state.
2. Whether to accept the requirements: the 65 from MetaLeX's plan, and the 46 from this design.[^accept]

## Notes

[^protocol]: **Protocol and portability, [I-1 to I-9](invariants.md#I-1).** The contracts run unchanged in an embedded EVM with a pinned version and hardfork. Each issuer state has its own `chainid`, so signatures stay bound to their domain. The deployment pins code hashes and the corporate graph. Also [R-1](invariants.md#R-1), [R-12](invariants.md#R-12) and [R-45](invariants.md#R-45). System design §3.

[^parties]: **Operators and keys, [R-2](invariants.md#R-2), [R-3](invariants.md#R-3), [R-6](invariants.md#R-6), [R-14](invariants.md#R-14), [R-27](invariants.md#R-27), [R-34](invariants.md#R-34), [R-38](invariants.md#R-38) and [R-42](invariants.md#R-42).** One watcher party per issuer anchors one RootChain, with a two-thirds-majority FROST key. The MetaLeX hub is a watcher member of every party, but liveness never depends on it. A party key that signs two successors of one root loses its bond. System design §4.

[^authority]: **Authority, [I-10 to I-13](invariants.md#I-10).** Infrastructure keys and data keys are separate from corporate authority. The recovery operator cannot invent balances, sign as a user or choose a beneficiary. Also [R-11](invariants.md#R-11): only an asset's holder can start an action against it. System design §4.1, §8.1.

[^value]: **Value and payments, [R-5](invariants.md#R-5), [R-13](invariants.md#R-13), [R-24 to R-26](invariants.md#R-24), [R-29](invariants.md#R-29), [R-30](invariants.md#R-30), [R-35](invariants.md#R-35), [R-41](invariants.md#R-41) and [R-46](invariants.md#R-46).** All value is armada-nitro notes and channels. The issuer state sees USDC only as channel balances, which the watcher party updates from co-signed states it receives over Nitro p2p. Routed payments are atomic, and every signed act binds the watcher party. System design §9.

[^privacy]: **Privacy and access, [I-34 to I-41](invariants.md#I-34).** One state per issuer, blinded blob data, and a party RPC scoped by the contracts' own authority. Nested calls are checked frame by frame. Also [R-4](invariants.md#R-4), [R-16](invariants.md#R-16), [R-22](invariants.md#R-22) and [R-36](invariants.md#R-36). System design §3, §7, §8.4, §8.5.

[^recovery]: **Recovery, [I-42 to I-51](invariants.md#I-42).** A root is final only after t watcher members store its batch. Ethereum accepts one successor per root, so two operators cannot continue in conflict. Any t watcher members continue with no promotion step. System design §4.2, §5.1, §6.

[^selfhelp]: **Holder access and self-help, [R-9](invariants.md#R-9), [R-10](invariants.md#R-10), [R-15](invariants.md#R-15), [R-19 to R-21](invariants.md#R-19), [R-31](invariants.md#R-31), [R-33](invariants.md#R-33) and [R-37](invariants.md#R-37).** A holder reads roots through a light client and checks inclusion proofs. It checks its history against each root's events, and rebuilds its proofs from its own store and the blobs. System design §8.

[^scrip]: **LET and scrip, [I-14 to I-23](invariants.md#I-14).** Scrip is locked, not burned, so the scrip ratio stays fixed. The lock requires the force features off and no transfer hooks. LET custody and legal ownership stay as the contracts define them. System design §5.4, §10.

[^bridge]: **Bridge accounting and messages, [I-24 to I-33](invariants.md#I-24).** K = Q + O + R holds, and the conservation proof shows it at each root. Each lock and return record binds `BridgeMessageV1`'s fields, waits for finality, and has one disposition. System design §5.4, §10, §11.

[^records]: **Issuer records, [I-52 to I-58](invariants.md#I-52).** The issuer holds its full state, logs and history. Completeness without the zkEVM is labeled attested, and no hash of guessable personal data is published. System design §3, §4.3, §7.

[^departure]: **Departure, [I-59 to I-64](invariants.md#I-59).** An on-chain registry records the old state's final root and its successor, and the old RootChain then accepts no roots. Each wrapper note and channel balance gets one disposition in the new state. Also [R-39](invariants.md#R-39). System design §5.5.

[^parity]: **Interface parity, [I-65](invariants.md#I-65).** The party RPC is one standard, authority-scoped endpoint for MetaLeX's app and any other interface. System design §8.5, §8.6.

[^trust]: **Trust without the zkEVM, [R-7](invariants.md#R-7), [R-23](invariants.md#R-23), [R-28](invariants.md#R-28) and [R-44](invariants.md#R-44).** A zkEVM is not required for a reasonable level of safety. A conservation proof and a public verifier tool take its place for the wrapper's supply. System design §11.

[^data]: **Publishing, [R-8](invariants.md#R-8), [R-17](invariants.md#R-17), [R-18](invariants.md#R-18) and [R-43](invariants.md#R-43).** Once a day, one transaction posts all roots and the blinded data, packed by size. An issuer with no changes posts nothing. System design §6.2, §7, §13.

[^limits]: **Limits, [R-40](invariants.md#R-40).** A refused write has no remedy on Ethereum. Watchtowers follow [R-37](invariants.md#R-37). System design §8.5, §9.1.

[^accept]: **Acceptance, [R-32](invariants.md#R-32).** The requirements document shows which requirements come from MetaLeX's plan and which come from this design, and MetaLeX accepts both.
