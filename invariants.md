# Requirements of the system

Status: draft for review, 2026-11-09.

This document lists every requirement that the system must keep, in two parts:
- **I-1 to I-65** come from MetaLeX's plan, *Build MetaLeX's private chain with recovery* (`mlx`), evidence as of 8 November 2026. Each row quotes the plan.
- **R-1 to R-46** come from Armada's design.

**Acceptance.** MetaLeX accepts both lists as the requirements of the system. An objection names the ID that it concerns. Section numbers in the last columns refer to `system-design.md`. Terms follow `glossary.md`.

## 1. From MetaLeX's plan (I-1 to I-65)

**Method.** Armada extracted these from `mlx` in one reading. Each row quotes the `mlx` sentence that states the invariant. A script checked that every quotation occurs verbatim in the plan's text. The extraction can still have missed an invariant. A second, independent reading is the check for that.

**Status.**
- **Design:** the design holds it by construction, through the mechanism named.
- **Build:** it holds once ordinary engineering is done. `mlx`'s own design needs the same work.
- **Partial:** the design holds part of it. The row states which part, and what would hold the rest.

| Status | Count |
|---|---:|
| Design | 44 |
| Build | 20 |
| Partial | 1 |

### Protocol and portability

| ID | Invariant | `mlx` text | Location | Status | How this design holds it |
|---|---|---|---|---|---|
| <a id="I-1"></a>I-1 | One EVM-native Cybercorps protocol on public and private chains; no note-based or UTXO redesign | "a note-based redesign is excluded" | Summary | Design | The contracts run unchanged in the nimbus EVM (§3). Value stays in armada-nitro, so no note model touches Cybercorps state (§9). |
| <a id="I-2"></a>I-2 | Preserve corporate authority, LET legal-owner and custody, agreement identities, escrow obligations, scrip accounting and recertification rules | "Preserve CyberCorp authority, LET legal-owner/custody distinctions, agreement identities, escrow obligations, scrip accounting and recertification rules." | §1, "One EVM protocol architecture is a fixed requirement" | Design | Unchanged contracts and bytecode (§3). |
| <a id="I-3"></a>I-3 | Same contract release, storage meanings and business transitions on every chain | "Same supported contract release, storage meanings and business transitions" | §1, Table 02 | Design | Every watcher member runs one pinned nimbus EVM commit and hardfork (§3). |
| <a id="I-4"></a>I-4 | Signatures are bound to their domain; historical signatures are not portable authorizations | "historical signatures are not portable authorizations" | §1, Table 02 | Design | A unique `chainid` per issuer (§3). |
| <a id="I-5"></a>I-5 | Contract rights do not depend on a Prividium login | "Contract rights do not depend on a Prividium login" | §1, Table 02 | Design | The contracts take no login as input (§8.1). |
| <a id="I-6"></a>I-6 | Unknown capabilities refuse | "Unknown capabilities refuse the relevant operation." | §1, "Source capability and deployed capability remain separate" | Build | Runtime manifest in the harness and the deploy module. |
| <a id="I-7"></a>I-7 | Deploy the full corporate graph, with code hashes, ABI and storage-layout digests | "Store compiler settings, link addresses, initialization transactions, ABI and storage-layout digests with the code hashes." | §2, "Deploy the complete corporate graph" | Build | Deploy module for the full graph. Each code hash is in the state root, so holders check it (§8.2). |
| <a id="I-8"></a>I-8 | The pilot creates fresh companies; no migration or silent upgrade of legacy flows | "it does not migrate existing companies or silently upgrade legacy/v4 flows" | §1, "Source capability and deployed capability remain separate" | Design | Deployment policy: fresh companies only. |
| <a id="I-9"></a>I-9 | Upgrades are constrained by factory reference implementations; no arbitrary issuer recovery code | "Existing upgrade methods do not allow arbitrary issuer recovery code." | §2, Table 04 | Design | Enforced by the unchanged factory and beacon contracts. |

### Authority

| ID | Invariant | `mlx` text | Location | Status | How this design holds it |
|---|---|---|---|---|---|
| <a id="I-10"></a>I-10 | An access administrator is not corporate authority | "do not interpret an access administrator as corporate authority" | §2, Table 04 | Design | Corporate authority stays in the contracts. The read scope uses that authority and holds none (§8.4). |
| <a id="I-11"></a>I-11 | Enumerate every privileged account; map every bridge key to an actor | "Every release/upgrade/pause key mapped to an actor" | §4, Table 07 | Build | Key inventory: party keys and bridge keys (§4.1). |
| <a id="I-12"></a>I-12 | The recovery operator cannot invent balances, sign as users or choose beneficiaries | "No power to invent balances, sign as users or choose beneficiaries" | §4, Table 07 | Partial | The recovery operator alone cannot sign a root: the party key needs a two-thirds majority of shares (§4.1). With one other watcher member it can sign a root that misaccounts. That root binds them, holders detect it, and the conservation proof catches supply that is not backed (§11). Watcher members hold no user keys. |
| <a id="I-13"></a>I-13 | Data keys never authorize transactions, registry signatures or bridge releases | "Data keys never authorize transactions, registry signatures or bridge releases." | §3, "Specify the bundle before building its exporter" | Design | No data keys exist: blob data is blinded, not encrypted (§7). |

### LET and scrip

| ID | Invariant | `mlx` text | Location | Status | How this design holds it |
|---|---|---|---|---|---|
| <a id="I-14"></a>I-14 | Custody and legal ownership are distinct; custody transfer alone changes no legal owner | "custody transfer alone changes no legal owner" | §5, Table 10 | Design | Contract rule, unchanged. |
| <a id="I-15"></a>I-15 | Scripification acts for the actual caller; a relay cannot act for a holder | "A relay contract cannot simply call on behalf of a holder: its own address becomes the caller." | §2, "Why the first bridge locks canonical scrip" | Design | Contract rule, unchanged. |
| <a id="I-16"></a>I-16 | Void, frozen and reserved LETs refuse prohibited operations; the endorsement floor survives a restore | "All prohibited operations refuse; endorsement floor preserved." | §5, Table 10 | Design | Contract rule, unchanged. |
| <a id="I-17"></a>I-17 | Canonical scrip mint and burn stay IssuanceManager-only; the ordinary bridge cannot mint or burn | "Ordinary bridge cannot mint/burn" | §5, Table 10 | Design | The party custodian mints only the issuer's wrapper, never canonical scrip (§10). |
| <a id="I-18"></a>I-18 | The scrip ratio cannot change while canonical supply is nonzero, so the bridge locks scrip | "the scrip ratio cannot change while canonical totalSupply() is nonzero" | §2, "Why the first bridge locks canonical scrip" | Design | Contract rule. The bridge locks, as `mlx` §2 specifies (§10). |
| <a id="I-19"></a>I-19 | No force action destroys or moves collateral while public wrappers remain | "they must not destroy or move collateral while public wrappers remain outstanding" | §2, "Why the first bridge locks canonical scrip" | Design | Entry guard on the lock: force features off, no restriction hooks, pinned implementation (§5.4). A hook added later that blocks an unlock is a signed misaccounting (§5.4, §11). Beacon upgrades with outstanding wrapper notes belong to `mlx`'s powers review. |
| <a id="I-20"></a>I-20 | Private holder limits do not constrain the public wrapper; the wrapper's policy is explicit | "Define freeze handling and the wrapper's public transfer policy explicitly." | §2, "Why the first bridge locks canonical scrip" | Design | The wrapper is a plain ERC20 held as notes. Unshielded, it moves as a plain ERC20, and no private holder limit applies to it (§10). |
| <a id="I-21"></a>I-21 | Bridge return and recertification are distinct; a public purchaser does not inherit eligibility | "A public purchaser does not automatically inherit immediate recertification eligibility." | §2, "Execute and reconcile the full round trip" | Design | Contract rule, unchanged. |
| <a id="I-22"></a>I-22 | Preserve the raw pool state; attribution never exceeds the aggregate; ERC20 transfers do not move attribution | "ERC20 transfers do not transfer LET attribution." | §2, "Execute and reconcile the full round trip" | Design | Contract rule, unchanged. |
| <a id="I-23"></a>I-23 | Do not assert exact equality between token supply and underlying units under floor rounding | "do not assert exact equality between all token supply and underlying units" | §2, "Execute and reconcile the full round trip" | Design | Accounting rule of `mlx`, unchanged. |

### Bridge accounting

| ID | Invariant | `mlx` text | Location | Status | How this design holds it |
|---|---|---|---|---|---|
| <a id="I-24"></a>I-24 | S = F + B = F + K + X and B = K + X | "Enforce S = F + B = F + K + X and B = K + X." | §2, "Execute and reconcile the full round trip" | Build | `mlx` §2 accounting over locks, wrapper supply and returns (§10). |
| <a id="I-25"></a>I-25 | At a reconciled checkpoint, K = Q + O + R | "K = public wrapper supply Q + outbound locks awaiting mint O + public burns awaiting release R" | §2, "Execute and reconcile the full round trip" | Design | The conservation proof shows it at every hard-final root (§11). |
| <a id="I-26"></a>I-26 | A donation raises B and X only, never mint authority or liabilities | "An unsolicited donation increases B and X, never wrapper mint authority or bridge liabilities." | §2, "Execute and reconcile the full round trip" | Build | Same. |
| <a id="I-27"></a>I-27 | Keep explicit liabilities; never infer issuance from an escrow balance | "Maintain liabilities rather than inferring issuance from an escrow balance." | §2, "Execute and reconcile the full round trip" | Build | Same. |

### Bridge messages

| ID | Invariant | `mlx` text | Location | Status | How this design holds it |
|---|---|---|---|---|---|
| <a id="I-28"></a>I-28 | BridgeMessageV1 binds protocol, asset, chains, gateways, epochs, direction, nonce, parties, amount and source event | "Each proposed BridgeMessageV1 binds protocol/version" | §2, "Execute and reconcile the full round trip" | Build | Each lock and return record binds issuer, epoch, direction, asset, holder, note key, amount, nonce and source event (§5.4). |
| <a id="I-29"></a>I-29 | Any wrong field, proof or finality refuses without a balance change | "refuses without balance change" | §2, "Execute and reconcile the full round trip" | Build | The party custodian refuses a mint or return whose record does not match a lock or a wrapper payment (§5.4, §10). |
| <a id="I-30"></a>I-30 | Each message is consumed once; exactly one release or recovery disposition | "Exactly one valid release or defined recovery disposition; no double refund." | §5, Table 10 | Design | Each lock is wrapped once, and each return is unlocked once (§5.4). |
| <a id="I-31"></a>I-31 | Expiry never leaves a burned token without release or refund | "Expiry cannot leave a burned public token with neither release nor authenticated refund." | §2, "Execute and reconcile the full round trip" | Build | Every return is unlocked by the watcher party (§5.4). |
| <a id="I-32"></a>I-32 | Wait for source finality; no unbacked supply after a reorg | "Waits for finality; authenticated completion/refund; no unbacked supply." | §5, Table 10 | Design | The party custodian mints only for locks in hard-final batches, and the RootChain accepts one successor per root (§5.1, §5.4, §6.1). No L1 check enforces the first rule; the signed mint record binds the watcher party (§11). |
| <a id="I-33"></a>I-33 | Message statuses are independent facts | "these are independent facts, not one green checkmark" | §2, "Execute and reconcile the full round trip" | Design | `mlx`'s six statuses map onto the scrip lock's states, with message-proven replaced by the watcher party's signed record (§5.4). |

### Privacy and access

| ID | Invariant | `mlx` text | Location | Status | How this design holds it |
|---|---|---|---|---|---|
| <a id="I-34"></a>I-34 | LETs and their corporate, agreement and payment state stay private; public scrip exposes addresses, amounts and timing | "Keep LETs and their supporting corporate, agreement and payment state private" | Summary | Design | One state per issuer; blob data is blinded (§3, §7). |
| <a id="I-35"></a>I-35 | MetaLeX and the recovery operator are the accepted plaintext audiences | "The design accepts MetaLeX and that operator as plaintext audiences." | §1, "Orientation" | Design | The watcher members hold plaintext: MetaLeX core, the recovery operator and the issuer (§4). MetaLeX is a plaintext audience through the MetaLeX hub (R-36). Holds if the watcher members are bound to their principals. |
| <a id="I-36"></a>I-36 | No cross-issuer reads through any interface | "No unauthorized fields or rows returned." | §5, Table 10 | Design | One state per issuer: no other issuer's data exists in it (§3). |
| <a id="I-37"></a>I-37 | A holder sees less than an issuer officer | "Holder access is narrower than issuer-officer access" | §4, "Isolate issuers without changing the EVM state model" | Design | Read scope from contract authority (§8.4). |
| <a id="I-38"></a>I-38 | Access to one agreement does not reveal another issuer's agreement by a changed ID | "access to one agreement must not reveal another issuer's agreement by changing its ID" | §4, "Adapt actual application transports and call paths" | Design | Per-issuer registries plus the read scope (§3, §8.4). |
| <a id="I-39"></a>I-39 | Nested call paths are controlled; top-level selector allowlists are not sufficient | "top-level selector allowlists alone are insufficient" | §4, "Isolate issuers without changing the EVM state model" | Design | Call-trace inspector on every frame before sequencing (§8.5). |
| <a id="I-40"></a>I-40 | No user credential in a shared client cache; fail closed | "fail closed on private-chain authentication failure" | §4, "Adapt actual application transports and call paths" | Build | MetaLeX app transport changes and the party RPC's request signatures (`mlx` A1, §8.1, §8.5). |
| <a id="I-41"></a>I-41 | Promise protected record access, not zero observable information | "promise protected record access rather than zero observable information" | §4, "Isolate issuers without changing the EVM state model" | Design | Stated scope. Blob data reveals change counts, timing, and that the same position changed again (§7). |

### Recovery

| ID | Invariant | `mlx` text | Location | Status | How this design holds it |
|---|---|---|---|---|---|
| <a id="I-42"></a>I-42 | Never roll back past a public bridge obligation and call the result current | "never roll back past a public bridge obligation and call the result current" | §3, "P2 recovery is operational; P3 adds enforceable departure" | Design | Only hard-final batches affect anything outside the issuer state, and hard-final roots cannot be replaced (§5.1). |
| <a id="I-43"></a>I-43 | No loss of finalized obligations | "no loss of finalized obligations" | §3, "P2 recovery is operational; P3 adds enforceable departure" | Design | A root is hard-final only with the party signature. A watcher member signs only after it stores the batch, so t watcher members hold every hard-final batch (§4.2, §6.1). Holders also keep their own records (§8.1). |
| <a id="I-44"></a>I-44 | If data or authority is missing, the drill fails | "If data or authority is missing, the drill fails." | §3, "P2 recovery is operational; P3 adds enforceable departure" | Build | Recovery drills. |
| <a id="I-45"></a>I-45 | Two operators cannot continue conflicting operation; DNS failover is insufficient | "DNS failover is insufficient." | §3, "P2 recovery is operational; P3 adds enforceable departure" | Design | One successor per root on L1, and two successors of one root are slashable (§4.4, §6.1). |
| <a id="I-46"></a>I-46 | Promotion needs no MetaLeX signature and no discretionary vendor permission | "If promotion requires an unavailable MetaLeX signature or discretionary vendor permission, independent recovery has failed." | §3, "P2 recovery is operational; P3 adds enforceable departure" | Design | No promotion step. Any t watcher members continue, and no signing set needs MetaLeX's share (§4.1, §6.2). |
| <a id="I-47"></a>I-47 | Immutable recovery copies survive mistakes replicated into the live database | "Immutable recovery copies must survive mistakes replicated into the live database." | §3, "Three artifacts have three different guarantees" | Design | Blob data on L1 cannot be changed, and every watcher member archives it (§7). Each wallet also keeps its latest proof of each record. |
| <a id="I-48"></a>I-48 | Recovery ends with an independently produced next accepted proof | "Recovery operator restores access, resumes and obtains next accepted proof independently." | §5, Table 10 | Design | Any t watcher members produce and submit the next root (§6.2). |
| <a id="I-49"></a>I-49 | Reconcile receipts before any replay | "recovery must reconcile receipts before replaying anything" | §3, "Specify the bundle before building its exporter" | Build | Harness replay rules. |
| <a id="I-50"></a>I-50 | Pause new writes when independent recoverability falls behind | "Pause new writes operationally when independent recoverability falls behind its configured threshold" | §3, "P2 recovery is operational; P3 adds enforceable departure" | Design | Recoverability is a precondition of hard finality: t watcher members store a batch before it is signed (§4.2). Nothing outside the issuer state depends on a batch before that (§5.1). |
| <a id="I-51"></a>I-51 | No atomicity between chain and database is implied | "Do not imply cross-chain/database atomicity." | §3, "Specify the bundle before building its exporter" | Design | Stated scope. |

### Issuer records

| ID | Invariant | `mlx` text | Location | Status | How this design holds it |
|---|---|---|---|---|---|
| <a id="I-52"></a>I-52 | The bundle is independently decryptable and readable; a scoped URL is not authorization | "a scoped URL is not authorization" | §3, "Specify the bundle before building its exporter" | Build | Exporter. The issuer is a watcher member and holds the full state (§4.3). |
| <a id="I-53"></a>I-53 | Raw ABI bytes beside decoded records; exact integer strings; canonical serialization with golden vectors | "Preserve raw ABI bytes beside decoded records; represent quantities as exact integer strings." | §3, "Specify the bundle before building its exporter" | Build | Exporter schema. |
| <a id="I-54"></a>I-54 | Actual document bytes with SHA-256; a CID, URL or hash cannot recreate a lost file | "A CID, URL or hash cannot recreate a lost file." | §3, "Specify the bundle before building its exporter" | Build | Document export. |
| <a id="I-55"></a>I-55 | Shared-registry membership is identified and derived; counts or a Merkle leaf do not prove absence | "Counts and a valid Merkle leaf do not prove absence of omitted records." | §3, "Specify the bundle before building its exporter" | Build | Exporter. Logs are proven against `eventsRoot` (§3). |
| <a id="I-56"></a>I-56 | Label completeness as attested unless a verified state or projection proof exists | "Label it attested completeness, unless a separately verified state/projection proof establishes more." | §3, "Specify the bundle before building its exporter" | Design | Records are checked with inclusion and non-inclusion proofs against party-signed L1 roots (§8.2). Without the zkEVM, completeness is attested by the party signature, and is labeled attested (§11). |
| <a id="I-57"></a>I-57 | A signature over a manifest hash does not prove the manifest is complete or matches accepted state | "does not prove that manifest is complete or corresponds to the accepted EVM state" | §3, "P2 recovery is operational; P3 adds enforceable departure" | Design | The party signs the state root, `eventsRoot` and the blob hashes (§6.1). The signature binds the data. It does not show that the state follows from the previous root (§11). |
| <a id="I-58"></a>I-58 | Do not publish hashes of guessable personal data | "publishing hashes of guessable personal data can disclose information" | §3, "Specify the bundle before building its exporter" | Design | No hashes of unsalted data are published: every leaf and position is salted (§3, §7). |

### Departure (P3)

| ID | Invariant | `mlx` text | Location | Status | How this design holds it |
|---|---|---|---|---|---|
| <a id="I-59"></a>I-59 | Departure is limited to issuers with settled deals, drained escrows, no reserved units and resolved allocations | "Initially support only issuers with settled deals, drained escrows, no reserved units and resolved allocations." | §3, "P2 recovery is operational; P3 adds enforceable departure" | Build | Departure scope (§5.5). |
| <a id="I-60"></a>I-60 | The source freezes and retires the accepted epoch | "The source must freeze and retire the accepted epoch" | §3, "P2 recovery is operational; P3 adds enforceable departure" | Design | `finalizeDeparture` records the final root and the successor. The RootChain then accepts no further roots (§5.2, §5.5). |
| <a id="I-61"></a>I-61 | Importer completeness and economic checks precede activation | "importer completeness and economic checks must precede activation" | §3, "P2 recovery is operational; P3 adds enforceable departure" | Build | The successor's watcher party runs its acceptance checks before it takes the state (§5.5). |
| <a id="I-62"></a>I-62 | Every outstanding wrapper and pending message has one disposition, including late burns | "Outstanding wrappers and every pending message need one disposition, including late burns." | §3, "P2 recovery is operational; P3 adds enforceable departure" | Build | Each wrapper note, pending lock or return, and channel balance gets one disposition in the successor's state (§5.5). |
| <a id="I-63"></a>I-63 | Old collateral is not released while successor backing is also created | "Old collateral cannot be released while successor backing is also created." | §3, "P2 recovery is operational; P3 adds enforceable departure" | Build | The successor backs the outstanding wrapper notes. The old RootChain is Retired and unlocks nothing (§5.5, §10). |
| <a id="I-64"></a>I-64 | New actions use fresh signature domains; old signatures remain evidence | "New actions use fresh signature domains; old signatures remain historical evidence." | §3, "P2 recovery is operational; P3 adds enforceable departure" | Design | A successor has a new `chainid`, so new signature domains (§3). |

### Interface parity

| ID | Invariant | `mlx` text | Location | Status | How this design holds it |
|---|---|---|---|---|---|
| <a id="I-65"></a>I-65 | An independently hosted interface gets equivalent reads, submission, fees and treatment | "Equivalent authorized reads/submission, fees and treatment." | §5, Table 10 | Design | The party RPC is the one authority-scoped interface, for MetaLeX's app and any other (§8.1, §8.5). |

## 2. From Armada's design (R-1 to R-46)

These are the design requirements that Armada sets for the system. Each is written in the terms of `glossary.md`.

| ID | Requirement | Where the design holds it |
|---|---|---|
| <a id="R-1"></a>R-1 | Each watcher member runs the Cybercorps contracts in an EVM embedded in its harness. No external node. | §3 |
| <a id="R-2"></a>R-2 | Each issuer has its own watcher party, and the set of watcher parties holds I-46. | §4.1 |
| <a id="R-3"></a>R-3 | Each party bond is an armada-nitro channel on L1. | §4.4 |
| <a id="R-4"></a>R-4 | Confidentiality comes from one state per issuer, blinded blob data and the watcher parties, not from an access gateway. | §3, §7 |
| <a id="R-5"></a>R-5 | Value moves into and out of an issuer state only through armada-nitro. | §9 |
| <a id="R-6"></a>R-6 | The MetaLeX hub is a watcher member of every party, and the system's liveness never depends on it. Any t watcher members continue the RootChain and the pay-outs without it. | §4.1 |
| <a id="R-7"></a>R-7 | ZisK stays a candidate zkVM. | §11 |
| <a id="R-8"></a>R-8 | The tree hash is pluggable, and the documents state why each hash is better or worse. | §14 |
| <a id="R-9"></a>R-9 | Blinded blob data on L1 lets holders use self-help without any watcher member. Plaintext availability rests on t watcher members. | §4.2, §7 |
| <a id="R-10"></a>R-10 | Each batch commits to an events root. | §3, §8 |
| <a id="R-11"></a>R-11 | No party other than an asset holder can start an action against that holder's assets. | §1 |
| <a id="R-12"></a>R-12 | No fallbacks. Every component is a typed state machine with guarded transitions. | §5 |
| <a id="R-13"></a>R-13 | Funding a channel with the watcher party needs a working hub: the MetaLeX hub or an issuer's hub. | §9.1 |
| <a id="R-14"></a>R-14 | Each watcher party anchors one issuer RootChain. The issuer state holds only assets that the watcher party issues. All value is armada-nitro assets. | §2, §3, §9 |
| <a id="R-15"></a>R-15 | The party RPC gives each holder its records and proofs. If the issuer, MetaLeX core and the recovery operator all fail, blob data on L1 lets the holder use self-help. | §7, §8 |
| <a id="R-16"></a>R-16 | Blob data is blinded tree data, not ciphertext. Breaking it reveals no record. | §7 |
| <a id="R-17"></a>R-17 | An issuer with no transactions publishes no blob. | §5.2, §6.2 |
| <a id="R-18"></a>R-18 | Daily aggregation by MetaLeX core is the default. | §6.2 |
| <a id="R-19"></a>R-19 | Delivery to holders is through the party RPC. | §8.1 |
| <a id="R-20"></a>R-20 | The MetaLeX hub mediates authentication by supplying user IDs to each issuer state. | §8.1 |
| <a id="R-21"></a>R-21 | A user signs its own requests to the party RPC, and the MetaLeX hub is not in the request path. | §8.1 |
| <a id="R-22"></a>R-22 | The issuer sees every counterparty's private data. No counterparty sees another counterparty's private data. | §8.4 |
| <a id="R-23"></a>R-23 | The zkEVM matters for completeness, but it is out of scope because of its operating cost. | §11 |
| <a id="R-24"></a>R-24 | The ERC20 that the Cybercorps contracts pull is a view of armada-nitro. Each balance is USDC that a holder paid to the watcher party and has not withdrawn. | §9.2 |
| <a id="R-25"></a>R-25 | A payment to the issuer ends as a note owned by the issuer's identity, and a payout record in the issuer state points to it. | §9.2, §9.4 |
| <a id="R-26"></a>R-26 | Every signed act of the watcher party is a binding commitment, even if a later root fails to account for it. The zkEVM adds conservation; the signature already commits the watcher party to n shares for y USDC. | §9.2, §11 |
| <a id="R-27"></a>R-27 | The watcher party is an entity. The MetaLeX hub is an entity distinct from MetaLeX, the company. | §2, §4 |
| <a id="R-28"></a>R-28 | A conservation proof, verified off chain, shows conservation of the wrapper without the zkEVM. | §11 |
| <a id="R-29"></a>R-29 | A hub names the wrapper factory in its asset list, so issuer wrappers need no per-token listing. | §10 |
| <a id="R-30"></a>R-30 | Value moves as routed payments, which are atomic. No fronting and no hash lock. | §9.3 |
| <a id="R-31"></a>R-31 | The party RPC's standard endpoint and the browser absorb most changes to MetaLeX's web app. Each watcher party does its own indexing. | §8.6 |
| <a id="R-32"></a>R-32 | MetaLeX accepts the invariants, and the invariants show which come from MetaLeX's plan and which from the operator's design requirements. | `metalex/invariants.md` |
| <a id="R-33"></a>R-33 | Most users use the website. Users who want to keep their records use Armada's mobile app, which contains the website and a watcher. Browser storage holds the same data, but it is more easily deleted. | §7, §8.3 |
| <a id="R-34"></a>R-34 | The party custodian has no hot delegate. Roots finalize daily, so a threshold signature on each channel state costs no time that matters. | §4.1 |
| <a id="R-35"></a>R-35 | A buyer's USDC stays in armada-nitro. Paying in moves it to the watcher party's allocation in the buyer's own channel. No app reads an issuer root, because the signatures are binding. | §9.2 |
| <a id="R-36"></a>R-36 | MetaLeX is a plaintext audience through the MetaLeX hub. | §4.1, §8.4 |
| <a id="R-37"></a>R-37 | The MetaLeX hub runs watchtowers for website users by default. The recovery operator may run them too. The mobile app runs its own. | §9.1 |
| <a id="R-38"></a>R-38 | The party-key threshold is a two-thirds majority, a tunable parameter. Without MetaLeX core, an issuer's RootChain advances only while that issuer's watcher member is up. | §4.1 |
| <a id="R-39"></a>R-39 | A departure uses the holders' own stores and the MetaLeX hub's copy of the state. Nothing is imported on L1. | §5.5 |
| <a id="R-40"></a>R-40 | A payer whose write is refused gets no asset and has no L1 remedy. Its evidence goes to MetaLeX. | §8.5 |
| <a id="R-41"></a>R-41 | The adjudicator works as normal, and no channel state is published. | §9.2 |
| <a id="R-42"></a>R-42 | Equivocation is the standard rule: two signed successors of one root. | §4.4 |
| <a id="R-43"></a>R-43 | Publishing is aggregated: once per day, one root of party roots and the blobs of all parties, packed by size. | §6.2, §7 |
| <a id="R-44"></a>R-44 | Third parties verify the conservation proof independently, with a tool that reads a root and the wrapper's supply from L1. | §11 |
| <a id="R-45"></a>R-45 | The EVM is the nimbus EVM. The server, the browser and the mobile app run one EVM, on the same runtime as Armada's light client. | §3 |
| <a id="R-46"></a>R-46 | The issuer state reads armada-nitro through a system contract, the channel-balance contract. The watcher party updates it from co-signed states it receives over Nitro p2p. | §9.2 |
