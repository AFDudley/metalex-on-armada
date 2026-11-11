# Requirements of the system

Status: draft for review, 2026-11-11.

The system must keep 65 requirements from MetaLeX's plan (I-1 to I-65) and 46 from Armada's design (R-1 to R-46). MetaLeX accepts both lists as the requirements of the system, and an objection names the ID it concerns. Each group below has one sentence. Open a group to see its requirements. Section numbers refer to `system-design.md`.

## 1. From MetaLeX's plan (I-1 to I-65)

Armada read MetaLeX's plan once and took out every statement of something the system must keep. Each requirement below quotes the sentence in the plan that states it. A script checked that each quotation appears word for word in the plan. One reading can miss a requirement, so a second, independent reading is the check for that.

Each requirement also says how this design holds it. 44 hold by design: a mechanism in the design makes them true. 20 hold once built: they need ordinary engineering, the same work that MetaLeX's own design needs. One holds in part, and its explanation says which part is still open.


### Protocol and portability

The unchanged Cybercorps contracts run in an embedded EVM with a pinned release, and each issuer state has its own chain ID.

<details markdown="1">
<summary>I-1 to I-9 (9 requirements)</summary>

<div class="req" markdown="1">

<a id="I-1"></a>**I-1.** The system keeps one EVM-native Cybercorps protocol on public and private chains, with no note-based or UTXO redesign.

*How the design holds it (by design):* The contracts run unchanged in the nimbus EVM (§3). Value stays in armada-nitro, so no note model touches Cybercorps state (§9).

*MetaLeX's plan, Summary:* "a note-based redesign is excluded"

</div>

<div class="req" markdown="1">

<a id="I-2"></a>**I-2.** The design preserves CyberCorp authority, LET legal-owner and custody distinctions, agreement identities, escrow obligations, scrip accounting and recertification rules.

*How the design holds it (by design):* The contracts and their bytecode run unchanged (§3).

*MetaLeX's plan, §1, "One EVM protocol architecture is a fixed requirement":* "Preserve CyberCorp authority, LET legal-owner/custody distinctions, agreement identities, escrow obligations, scrip accounting and recertification rules."

</div>

<div class="req" markdown="1">

<a id="I-3"></a>**I-3.** Every chain runs the same contract release, the same storage meanings and the same business transitions.

*How the design holds it (by design):* Every watcher member runs one pinned nimbus EVM commit and hardfork (§3).

*MetaLeX's plan, §1, Table 02:* "Same supported contract release, storage meanings and business transitions"

</div>

<div class="req" markdown="1">

<a id="I-4"></a>**I-4.** A signature is bound to its own domain, so no historical signature is a portable authorization.

*How the design holds it (by design):* Each issuer state has its own `chainid`, so a signature for one issuer state is not valid in another (§3).

*MetaLeX's plan, §1, Table 02:* "historical signatures are not portable authorizations"

</div>

<div class="req" markdown="1">

<a id="I-5"></a>**I-5.** Contract rights never depend on a Prividium login.

*How the design holds it (by design):* The contracts take no login as input (§8.1).

*MetaLeX's plan, §1, Table 02:* "Contract rights do not depend on a Prividium login"

</div>

<div class="req" markdown="1">

<a id="I-6"></a>**I-6.** An unknown capability refuses the operation it would otherwise grant.

*How the design holds it (once built):* Runtime manifest in the harness and the deploy module.

*MetaLeX's plan, §1, "Source capability and deployed capability remain separate":* "Unknown capabilities refuse the relevant operation."

</div>

<div class="req" markdown="1">

<a id="I-7"></a>**I-7.** The system deploys the full corporate graph, and it stores code hashes, ABI and storage-layout digests together.

*How the design holds it (once built):* Deploy module for the full graph. Each code hash is in the state root, so holders check it (§8.2).

*MetaLeX's plan, §2, "Deploy the complete corporate graph":* "Store compiler settings, link addresses, initialization transactions, ABI and storage-layout digests with the code hashes."

</div>

<div class="req" markdown="1">

<a id="I-8"></a>**I-8.** The pilot creates fresh companies only, and it migrates no existing company or legacy flow.

*How the design holds it (by design):* The deployment creates fresh companies only. It migrates no existing company.

*MetaLeX's plan, §1, "Source capability and deployed capability remain separate":* "it does not migrate existing companies or silently upgrade legacy/v4 flows"

</div>

<div class="req" markdown="1">

<a id="I-9"></a>**I-9.** Upgrade methods allow no arbitrary issuer recovery code, and factory reference implementations constrain them.

*How the design holds it (by design):* Enforced by the unchanged factory and beacon contracts.

*MetaLeX's plan, §2, Table 04:* "Existing upgrade methods do not allow arbitrary issuer recovery code."

</div>


</details>


### Authority

Operator keys and data keys hold no corporate authority, and the recovery operator cannot create balances or sign for a user.

<details markdown="1">
<summary>I-10 to I-13 (4 requirements)</summary>

<div class="req" markdown="1">

<a id="I-10"></a>**I-10.** An access administrator is not corporate authority.

*How the design holds it (by design):* Corporate authority stays in the contracts. The read scope uses that authority and holds none (§8.4).

*MetaLeX's plan, §2, Table 04:* "do not interpret an access administrator as corporate authority"

</div>

<div class="req" markdown="1">

<a id="I-11"></a>**I-11.** The system enumerates every privileged account and maps every bridge key to an actor.

*How the design holds it (once built):* Key inventory: party keys and bridge keys (§4.1).

*MetaLeX's plan, §4, Table 07:* "Every release/upgrade/pause key mapped to an actor"

</div>

<div class="req" markdown="1">

<a id="I-12"></a>**I-12.** The recovery operator cannot invent balances, sign as users or choose beneficiaries.

*How the design holds it (in part):* The recovery operator alone cannot sign a root: the party key needs a two-thirds majority of shares (§4.1). With one other watcher member it can sign a root that misaccounts. That root binds them, holders detect it, and the conservation proof catches supply that is not backed (§11). Watcher members hold no user keys.

*MetaLeX's plan, §4, Table 07:* "No power to invent balances, sign as users or choose beneficiaries"

</div>

<div class="req" markdown="1">

<a id="I-13"></a>**I-13.** A data key never authorizes a transaction, a registry signature or a bridge release.

*How the design holds it (by design):* No data keys exist: blob data is blinded, not encrypted (§7).

*MetaLeX's plan, §3, "Specify the bundle before building its exporter":* "Data keys never authorize transactions, registry signatures or bridge releases."

</div>


</details>


### LET and scrip

Scrip is locked, not burned, when it leaves the issuer state, and LET custody and legal ownership stay as the contracts define them.

<details markdown="1">
<summary>I-14 to I-23 (10 requirements)</summary>

<div class="req" markdown="1">

<a id="I-14"></a>**I-14.** Custody and legal ownership stay distinct, so a custody transfer alone changes no legal owner.

*How the design holds it (by design):* The Cybercorps contracts already enforce this rule, and they run unchanged.

*MetaLeX's plan, §5, Table 10:* "custody transfer alone changes no legal owner"

</div>

<div class="req" markdown="1">

<a id="I-15"></a>**I-15.** Scripification acts for the actual caller, so a relay cannot act on behalf of a holder.

*How the design holds it (by design):* The Cybercorps contracts already enforce this rule, and they run unchanged.

*MetaLeX's plan, §2, "Why the first bridge locks canonical scrip":* "A relay contract cannot simply call on behalf of a holder: its own address becomes the caller."

</div>

<div class="req" markdown="1">

<a id="I-16"></a>**I-16.** A void, frozen or reserved LET refuses every prohibited operation, and a restore keeps the endorsement floor.

*How the design holds it (by design):* The Cybercorps contracts already enforce this rule, and they run unchanged.

*MetaLeX's plan, §5, Table 10:* "All prohibited operations refuse; endorsement floor preserved."

</div>

<div class="req" markdown="1">

<a id="I-17"></a>**I-17.** Only the IssuanceManager mints or burns canonical scrip, and the ordinary bridge cannot mint or burn it.

*How the design holds it (by design):* The party custodian mints only the issuer's wrapper, never canonical scrip (§10).

*MetaLeX's plan, §5, Table 10:* "Ordinary bridge cannot mint/burn"

</div>

<div class="req" markdown="1">

<a id="I-18"></a>**I-18.** The scrip ratio cannot change while canonical supply is nonzero, so the bridge locks scrip instead.

*How the design holds it (by design):* Contract rule. The bridge locks, as `mlx` §2 specifies (§10).

*MetaLeX's plan, §2, "Why the first bridge locks canonical scrip":* "the scrip ratio cannot change while canonical totalSupply() is nonzero"

</div>

<div class="req" markdown="1">

<a id="I-19"></a>**I-19.** No force action destroys or moves locked collateral while wrapped scrip remains outstanding.

*How the design holds it (by design):* Entry guard on the lock: force features off, no restriction hooks, pinned implementation (§5.4). A hook added later that blocks an unlock is a signed misaccounting (§5.4, §11). Beacon upgrades with outstanding wrapped-scrip notes belong to `mlx`'s powers review.

*MetaLeX's plan, §2, "Why the first bridge locks canonical scrip":* "they must not destroy or move collateral while public wrappers remain outstanding"

</div>

<div class="req" markdown="1">

<a id="I-20"></a>**I-20.** A private holder limit does not constrain wrapped scrip, and wrapped scrip states its own transfer policy.

*How the design holds it (by design):* The wrapper is a plain ERC20 held as notes. Unshielded, it moves as a plain ERC20, and no private holder limit applies to it (§10).

*MetaLeX's plan, §2, "Why the first bridge locks canonical scrip":* "Define freeze handling and the wrapper's public transfer policy explicitly."

</div>

<div class="req" markdown="1">

<a id="I-21"></a>**I-21.** Bridge return and recertification stay distinct, so a public purchaser does not inherit recertification eligibility.

*How the design holds it (by design):* The Cybercorps contracts already enforce this rule, and they run unchanged.

*MetaLeX's plan, §2, "Execute and reconcile the full round trip":* "A public purchaser does not automatically inherit immediate recertification eligibility."

</div>

<div class="req" markdown="1">

<a id="I-22"></a>**I-22.** The system preserves the raw pool state, keeps attribution within the aggregate, and never lets an ERC20 transfer move attribution.

*How the design holds it (by design):* The Cybercorps contracts already enforce this rule, and they run unchanged.

*MetaLeX's plan, §2, "Execute and reconcile the full round trip":* "ERC20 transfers do not transfer LET attribution."

</div>

<div class="req" markdown="1">

<a id="I-23"></a>**I-23.** The system does not assert exact equality between token supply and underlying units under floor rounding.

*How the design holds it (by design):* The design keeps this accounting rule from MetaLeX's plan unchanged.

*MetaLeX's plan, §2, "Execute and reconcile the full round trip":* "do not assert exact equality between all token supply and underlying units"

</div>


</details>


### Bridge accounting

Locked scrip equals the supply of scrip tokens outside the issuer state plus pending locks and returns, and the conservation proof shows this at each root.

<details markdown="1">
<summary>I-24 to I-27 (4 requirements)</summary>

<div class="req" markdown="1">

<a id="I-24"></a>**I-24.** Canonical scrip supply equals the balances outside the bridge escrow plus the escrow balance, and the escrow balance equals locked backing plus surplus.

*How the design holds it (once built):* The watcher party keeps MetaLeX's accounting over locks, wrapped-scrip supply and returns (§10).

*MetaLeX's plan, §2, "Execute and reconcile the full round trip":* "Enforce S = F + B = F + K + X and B = K + X."

</div>

<div class="req" markdown="1">

<a id="I-25"></a>**I-25.** At every reconciled checkpoint, locked backing equals wrapped-scrip supply plus locks awaiting a mint plus returns awaiting release.

*How the design holds it (by design):* The conservation proof shows it at every hard-final root (§11).

*MetaLeX's plan, §2, "Execute and reconcile the full round trip":* "K = public wrapper supply Q + outbound locks awaiting mint O + public burns awaiting release R"

</div>

<div class="req" markdown="1">

<a id="I-26"></a>**I-26.** An unsolicited donation to the escrow raises only its balance and surplus, never wrapped-scrip mint authority or bridge liabilities.

*How the design holds it (once built):* The watcher party keeps MetaLeX's accounting over locks, wrapped-scrip supply and returns (§10).

*MetaLeX's plan, §2, "Execute and reconcile the full round trip":* "An unsolicited donation increases B and X, never wrapper mint authority or bridge liabilities."

</div>

<div class="req" markdown="1">

<a id="I-27"></a>**I-27.** The system keeps explicit liabilities, and it never infers issuance from an escrow balance.

*How the design holds it (once built):* The watcher party keeps MetaLeX's accounting over locks, wrapped-scrip supply and returns (§10).

*MetaLeX's plan, §2, "Execute and reconcile the full round trip":* "Maintain liabilities rather than inferring issuance from an escrow balance."

</div>


</details>


### Bridge messages

Each lock and return record binds every message field, waits for finality, and has exactly one disposition.

<details markdown="1">
<summary>I-28 to I-33 (6 requirements)</summary>

<div class="req" markdown="1">

<a id="I-28"></a>**I-28.** Each BridgeMessageV1 binds protocol, asset, chains, gateways, epochs, direction, nonce, parties, amount and source event.

*How the design holds it (once built):* Each lock and return record binds issuer, epoch, direction, asset, holder, note key, amount, nonce and source event (§5.4).

*MetaLeX's plan, §2, "Execute and reconcile the full round trip":* "Each proposed BridgeMessageV1 binds protocol/version"

</div>

<div class="req" markdown="1">

<a id="I-29"></a>**I-29.** A wrong field, proof or finality check makes the message refuse without changing any balance.

*How the design holds it (once built):* The party custodian refuses a mint or return whose record does not match a lock or a wrapped-scrip payment (§5.4, §10).

*MetaLeX's plan, §2, "Execute and reconcile the full round trip":* "refuses without balance change"

</div>

<div class="req" markdown="1">

<a id="I-30"></a>**I-30.** The system consumes each message once, and gives it exactly one release or recovery disposition.

*How the design holds it (by design):* Each lock is wrapped once, and each return is unlocked once (§5.4).

*MetaLeX's plan, §5, Table 10:* "Exactly one valid release or defined recovery disposition; no double refund."

</div>

<div class="req" markdown="1">

<a id="I-31"></a>**I-31.** An expiry never leaves a burned token with neither a release nor a refund.

*How the design holds it (once built):* Every return is unlocked by the watcher party (§5.4).

*MetaLeX's plan, §2, "Execute and reconcile the full round trip":* "Expiry cannot leave a burned public token with neither release nor authenticated refund."

</div>

<div class="req" markdown="1">

<a id="I-32"></a>**I-32.** The system waits for source finality, so a reorg leaves no unbacked supply.

*How the design holds it (by design):* The party custodian mints only for locks in hard-final batches, and the RootChain accepts one successor per root (§5.1, §5.4, §6.1). No L1 check enforces the first rule; the signed mint record binds the watcher party (§11).

*MetaLeX's plan, §5, Table 10:* "Waits for finality; authenticated completion/refund; no unbacked supply."

</div>

<div class="req" markdown="1">

<a id="I-33"></a>**I-33.** Each message status is an independent fact, not one combined checkmark.

*How the design holds it (by design):* `mlx`'s six statuses map onto the scrip lock's states, with message-proven replaced by the watcher party's signed record (§5.4).

*MetaLeX's plan, §2, "Execute and reconcile the full round trip":* "these are independent facts, not one green checkmark"

</div>


</details>


### Privacy and access

Each issuer's records are in a separate state, and every read is scoped by the contracts' own authority.

<details markdown="1">
<summary>I-34 to I-41 (8 requirements)</summary>

<div class="req" markdown="1">

<a id="I-34"></a>**I-34.** LETs and their corporate, agreement and payment state stay private, while public scrip exposes addresses, amounts and timing.

*How the design holds it (by design):* One state per issuer; blob data is blinded (§3, §7).

*MetaLeX's plan, Summary:* "Keep LETs and their supporting corporate, agreement and payment state private"

</div>

<div class="req" markdown="1">

<a id="I-35"></a>**I-35.** MetaLeX and the recovery operator are the only accepted plaintext audiences.

*How the design holds it (by design):* The watcher members hold plaintext: MetaLeX core, the recovery operator and the issuer (§4). MetaLeX is a plaintext audience through the MetaLeX hub (R-36). Holds if the watcher members are bound to their principals.

*MetaLeX's plan, §1, "Orientation":* "The design accepts MetaLeX and that operator as plaintext audiences."

</div>

<div class="req" markdown="1">

<a id="I-36"></a>**I-36.** No interface lets one issuer's data leak into a cross-issuer read.

*How the design holds it (by design):* One state per issuer: no other issuer's data exists in it (§3).

*MetaLeX's plan, §5, Table 10:* "No unauthorized fields or rows returned."

</div>

<div class="req" markdown="1">

<a id="I-37"></a>**I-37.** A holder sees less data than an issuer officer sees.

*How the design holds it (by design):* The party RPC scopes every read by the contracts' own authority, so a holder sees less than an issuer officer (§8.4).

*MetaLeX's plan, §4, "Isolate issuers without changing the EVM state model":* "Holder access is narrower than issuer-officer access"

</div>

<div class="req" markdown="1">

<a id="I-38"></a>**I-38.** Access to one agreement does not reveal another issuer's agreement, even by a changed ID.

*How the design holds it (by design):* Per-issuer registries plus the read scope (§3, §8.4).

*MetaLeX's plan, §4, "Adapt actual application transports and call paths":* "access to one agreement must not reveal another issuer's agreement by changing its ID"

</div>

<div class="req" markdown="1">

<a id="I-39"></a>**I-39.** The system controls nested call paths, because top-level selector allowlists alone are not sufficient.

*How the design holds it (by design):* Call-trace inspector on every frame before sequencing (§8.5).

*MetaLeX's plan, §4, "Isolate issuers without changing the EVM state model":* "top-level selector allowlists alone are insufficient"

</div>

<div class="req" markdown="1">

<a id="I-40"></a>**I-40.** No shared client cache holds a user credential, and the system fails closed on an authentication failure.

*How the design holds it (once built):* MetaLeX app transport changes and the party RPC's request signatures (`mlx` A1, §8.1, §8.5).

*MetaLeX's plan, §4, "Adapt actual application transports and call paths":* "fail closed on private-chain authentication failure"

</div>

<div class="req" markdown="1">

<a id="I-41"></a>**I-41.** The system promises protected record access, not zero observable information.

*How the design holds it (by design):* Stated scope. Blob data reveals change counts, timing, and that the same position changed again (§7).

*MetaLeX's plan, §4, "Isolate issuers without changing the EVM state model":* "promise protected record access rather than zero observable information"

</div>


</details>


### Recovery

A root is final only after t watcher members store its batch, and any t watcher members continue with no promotion step.

<details markdown="1">
<summary>I-42 to I-51 (10 requirements)</summary>

<div class="req" markdown="1">

<a id="I-42"></a>**I-42.** The system never rolls back past a public bridge obligation and calls the result current.

*How the design holds it (by design):* Only hard-final batches affect anything outside the issuer state, and hard-final roots cannot be replaced (§5.1).

*MetaLeX's plan, §3, "P2 recovery is operational; P3 adds enforceable departure":* "never roll back past a public bridge obligation and call the result current"

</div>

<div class="req" markdown="1">

<a id="I-43"></a>**I-43.** The system loses no finalized obligation.

*How the design holds it (by design):* A root is hard-final only with the party signature. A watcher member signs only after it stores the batch, so t watcher members hold every hard-final batch (§4.2, §6.1). Holders also keep their own records (§8.1).

*MetaLeX's plan, §3, "P2 recovery is operational; P3 adds enforceable departure":* "no loss of finalized obligations"

</div>

<div class="req" markdown="1">

<a id="I-44"></a>**I-44.** If data or authority is missing, the recovery drill fails.

*How the design holds it (once built):* The watcher members run recovery drills. A drill fails when data or authority is missing.

*MetaLeX's plan, §3, "P2 recovery is operational; P3 adds enforceable departure":* "If data or authority is missing, the drill fails."

</div>

<div class="req" markdown="1">

<a id="I-45"></a>**I-45.** Two operators cannot continue conflicting operation, because DNS failover alone is not sufficient.

*How the design holds it (by design):* One successor per root on L1, and two successors of one root are slashable (§4.4, §6.1).

*MetaLeX's plan, §3, "P2 recovery is operational; P3 adds enforceable departure":* "DNS failover is insufficient."

</div>

<div class="req" markdown="1">

<a id="I-46"></a>**I-46.** Promotion needs no MetaLeX signature and no discretionary vendor permission.

*How the design holds it (by design):* No promotion step. Any t watcher members continue, and no signing set needs MetaLeX's share (§4.1, §6.2).

*MetaLeX's plan, §3, "P2 recovery is operational; P3 adds enforceable departure":* "If promotion requires an unavailable MetaLeX signature or discretionary vendor permission, independent recovery has failed."

</div>

<div class="req" markdown="1">

<a id="I-47"></a>**I-47.** An immutable recovery copy survives a mistake that was replicated into the live database.

*How the design holds it (by design):* Blob data on L1 cannot be changed, and every watcher member archives it (§7). Each wallet also keeps its latest proof of each record.

*MetaLeX's plan, §3, "Three artifacts have three different guarantees":* "Immutable recovery copies must survive mistakes replicated into the live database."

</div>

<div class="req" markdown="1">

<a id="I-48"></a>**I-48.** Recovery ends when the system independently produces the next accepted proof.

*How the design holds it (by design):* Any t watcher members produce and submit the next root (§6.2).

*MetaLeX's plan, §5, Table 10:* "Recovery operator restores access, resumes and obtains next accepted proof independently."

</div>

<div class="req" markdown="1">

<a id="I-49"></a>**I-49.** The system reconciles receipts before it replays anything.

*How the design holds it (once built):* The harness reconciles receipts before it replays any batch.

*MetaLeX's plan, §3, "Specify the bundle before building its exporter":* "recovery must reconcile receipts before replaying anything"

</div>

<div class="req" markdown="1">

<a id="I-50"></a>**I-50.** The system pauses new writes when independent recoverability falls behind its threshold.

*How the design holds it (by design):* Recoverability is a precondition of hard finality: t watcher members store a batch before it is signed (§4.2). Nothing outside the issuer state depends on a batch before that (§5.1).

*MetaLeX's plan, §3, "P2 recovery is operational; P3 adds enforceable departure":* "Pause new writes operationally when independent recoverability falls behind its configured threshold"

</div>

<div class="req" markdown="1">

<a id="I-51"></a>**I-51.** The system implies no atomicity between the chain and the database.

*How the design holds it (by design):* The design claims no atomicity between a chain and a database.

*MetaLeX's plan, §3, "Specify the bundle before building its exporter":* "Do not imply cross-chain/database atomicity."

</div>


</details>


### Issuer records

The issuer holds its full state and history, and completeness without the zkEVM is labelled attested.

<details markdown="1">
<summary>I-52 to I-58 (7 requirements)</summary>

<div class="req" markdown="1">

<a id="I-52"></a>**I-52.** The bundle is independently decryptable and readable, and a scoped URL alone is not authorization.

*How the design holds it (once built):* Exporter. The issuer is a watcher member and holds the full state (§4.3).

*MetaLeX's plan, §3, "Specify the bundle before building its exporter":* "a scoped URL is not authorization"

</div>

<div class="req" markdown="1">

<a id="I-53"></a>**I-53.** The export keeps raw ABI bytes beside decoded records, states quantities as exact integer strings, and serializes with golden vectors.

*How the design holds it (once built):* The exporter writes raw ABI bytes beside decoded records, with exact integer strings and canonical serialization.

*MetaLeX's plan, §3, "Specify the bundle before building its exporter":* "Preserve raw ABI bytes beside decoded records; represent quantities as exact integer strings."

</div>

<div class="req" markdown="1">

<a id="I-54"></a>**I-54.** The export keeps actual document bytes with their SHA-256 hash, because a CID, URL or hash cannot recreate a lost file.

*How the design holds it (once built):* The exporter includes the actual document bytes with their SHA-256 hashes.

*MetaLeX's plan, §3, "Specify the bundle before building its exporter":* "A CID, URL or hash cannot recreate a lost file."

</div>

<div class="req" markdown="1">

<a id="I-55"></a>**I-55.** The system identifies and derives shared-registry membership, because counts or a Merkle leaf do not prove absence.

*How the design holds it (once built):* Exporter. Logs are proven against `eventsRoot` (§3).

*MetaLeX's plan, §3, "Specify the bundle before building its exporter":* "Counts and a valid Merkle leaf do not prove absence of omitted records."

</div>

<div class="req" markdown="1">

<a id="I-56"></a>**I-56.** The system labels completeness as attested, unless a separately verified state or projection proof shows more.

*How the design holds it (by design):* Records are checked with inclusion and non-inclusion proofs against party-signed L1 roots (§8.2). Without the zkEVM, completeness is attested by the party signature, and is labeled attested (§11).

*MetaLeX's plan, §3, "Specify the bundle before building its exporter":* "Label it attested completeness, unless a separately verified state/projection proof establishes more."

</div>

<div class="req" markdown="1">

<a id="I-57"></a>**I-57.** A signature over a manifest hash does not prove that the manifest is complete or matches the accepted state.

*How the design holds it (by design):* The party signs the state root, `eventsRoot` and the blob hashes (§6.1). The signature binds the data. It does not show that the state follows from the previous root (§11).

*MetaLeX's plan, §3, "P2 recovery is operational; P3 adds enforceable departure":* "does not prove that manifest is complete or corresponds to the accepted EVM state"

</div>

<div class="req" markdown="1">

<a id="I-58"></a>**I-58.** The system does not publish hashes of guessable personal data.

*How the design holds it (by design):* No hashes of unsalted data are published: every leaf and position is salted (§3, §7).

*MetaLeX's plan, §3, "Specify the bundle before building its exporter":* "publishing hashes of guessable personal data can disclose information"

</div>


</details>


### Departure (P3)

An issuer can move to a successor watcher party, and every outstanding item gets exactly one disposition.

<details markdown="1">
<summary>I-59 to I-64 (6 requirements)</summary>

<div class="req" markdown="1">

<a id="I-59"></a>**I-59.** Departure supports only issuers with settled deals, drained escrows, no reserved units and resolved allocations.

*How the design holds it (once built):* The successor's watcher party accepts only an issuer that meets these scope rules (§5.5).

*MetaLeX's plan, §3, "P2 recovery is operational; P3 adds enforceable departure":* "Initially support only issuers with settled deals, drained escrows, no reserved units and resolved allocations."

</div>

<div class="req" markdown="1">

<a id="I-60"></a>**I-60.** The source chain freezes and retires the accepted epoch.

*How the design holds it (by design):* `finalizeDeparture` records the final root and the successor. The RootChain then accepts no further roots (§5.2, §5.5).

*MetaLeX's plan, §3, "P2 recovery is operational; P3 adds enforceable departure":* "The source must freeze and retire the accepted epoch"

</div>

<div class="req" markdown="1">

<a id="I-61"></a>**I-61.** Importer completeness checks and economic checks run before activation.

*How the design holds it (once built):* The successor's watcher party runs its acceptance checks before it takes the state (§5.5).

*MetaLeX's plan, §3, "P2 recovery is operational; P3 adds enforceable departure":* "importer completeness and economic checks must precede activation"

</div>

<div class="req" markdown="1">

<a id="I-62"></a>**I-62.** Every outstanding wrapped scrip and every pending message gets one disposition, including a late burn.

*How the design holds it (once built):* Each wrapped-scrip note, pending lock or return, and channel balance gets one disposition in the successor's state (§5.5).

*MetaLeX's plan, §3, "P2 recovery is operational; P3 adds enforceable departure":* "Outstanding wrappers and every pending message need one disposition, including late burns."

</div>

<div class="req" markdown="1">

<a id="I-63"></a>**I-63.** The system does not release old collateral while it also creates successor backing.

*How the design holds it (once built):* The successor backs the outstanding wrapped-scrip notes. The old RootChain is Retired and unlocks nothing (§5.5, §10).

*MetaLeX's plan, §3, "P2 recovery is operational; P3 adds enforceable departure":* "Old collateral cannot be released while successor backing is also created."

</div>

<div class="req" markdown="1">

<a id="I-64"></a>**I-64.** A new action uses a fresh signature domain, and an old signature remains historical evidence.

*How the design holds it (by design):* A successor has a new `chainid`, so new signature domains (§3).

*MetaLeX's plan, §3, "P2 recovery is operational; P3 adds enforceable departure":* "New actions use fresh signature domains; old signatures remain historical evidence."

</div>


</details>


### Interface parity

Every interface reads and writes through the same authority-scoped party RPC.

<details markdown="1">
<summary>I-65</summary>

<div class="req" markdown="1">

<a id="I-65"></a>**I-65.** An independently hosted interface gets equivalent authorized reads, submission, fees and treatment.

*How the design holds it (by design):* The party RPC is the one authority-scoped interface, for MetaLeX's app and any other (§8.1, §8.5).

*MetaLeX's plan, §5, Table 10:* "Equivalent authorized reads/submission, fees and treatment."

</div>


</details>


## 2. From Armada's design (R-1 to R-46)

### Protocol and execution

The contracts run unchanged in the nimbus EVM inside each watcher member's harness, with no fallback paths.

<details markdown="1">
<summary>R-1, R-12, R-45 (3 requirements)</summary>

<div class="req" markdown="1">

<a id="R-1"></a>**R-1.** Each watcher member runs the Cybercorps contracts in an EVM embedded in its harness. No external node. (§3)

</div>

<div class="req" markdown="1">

<a id="R-12"></a>**R-12.** No fallbacks. Every component is a typed state machine with guarded transitions. (§5)

</div>

<div class="req" markdown="1">

<a id="R-45"></a>**R-45.** The EVM is the nimbus EVM. The server, the browser and the mobile app run one EVM, on the same runtime as Armada's light client. (§3)

</div>


</details>


### Operators and keys

Each issuer has one watcher party with a two-thirds-majority FROST key, and liveness never depends on the MetaLeX hub.

<details markdown="1">
<summary>R-2, R-3, R-6, R-14, R-27, R-34, R-38, R-42 (8 requirements)</summary>

<div class="req" markdown="1">

<a id="R-2"></a>**R-2.** Each issuer has its own watcher party, and the set of watcher parties holds I-46. (§4.1)

</div>

<div class="req" markdown="1">

<a id="R-3"></a>**R-3.** Each party bond is an armada-nitro channel on L1. (§4.4)

</div>

<div class="req" markdown="1">

<a id="R-6"></a>**R-6.** The MetaLeX hub is a watcher member of every party, and the system's liveness never depends on it. Any t watcher members continue the RootChain and the pay-outs without it. (§4.1)

</div>

<div class="req" markdown="1">

<a id="R-14"></a>**R-14.** Each watcher party anchors one issuer RootChain. The issuer state holds only assets that the watcher party issues. All value is armada-nitro assets. (§2, §3, §9)

</div>

<div class="req" markdown="1">

<a id="R-27"></a>**R-27.** The watcher party is an entity. The MetaLeX hub is an entity distinct from MetaLeX, the company. (§2, §4)

</div>

<div class="req" markdown="1">

<a id="R-34"></a>**R-34.** The party custodian signs every channel state with the FROST party key. A FROST signing round takes one network round trip, far less than Ethereum's 12-second block time. (§4.1)

</div>

<div class="req" markdown="1">

<a id="R-38"></a>**R-38.** The party-key threshold is a two-thirds majority, a tunable parameter. Without MetaLeX core, an issuer's RootChain advances only while that issuer's watcher member is up. (§4.1)

</div>

<div class="req" markdown="1">

<a id="R-42"></a>**R-42.** Equivocation is the standard rule: two signed successors of one root. (§4.4)

</div>


</details>


### Authority

Only an asset's holder can start an action against that asset.

<details markdown="1">
<summary>R-11 (1 requirement)</summary>

<div class="req" markdown="1">

<a id="R-11"></a>**R-11.** No party other than an asset holder can start an action against that holder's assets. (§1)

</div>


</details>


### Value and payments

All value stays in armada-nitro, and the issuer state sees USDC only as channel balances.

<details markdown="1">
<summary>R-5, R-13, R-24, R-25, R-26, R-29, R-30, R-35, R-41, R-46 (10 requirements)</summary>

<div class="req" markdown="1">

<a id="R-5"></a>**R-5.** Value moves into and out of an issuer state only through armada-nitro. (§9)

</div>

<div class="req" markdown="1">

<a id="R-13"></a>**R-13.** Funding a channel with the watcher party needs a working hub: the MetaLeX hub or an issuer's hub. (§9.1)

</div>

<div class="req" markdown="1">

<a id="R-24"></a>**R-24.** The ERC20 that the Cybercorps contracts pull is a view of armada-nitro. Each balance is USDC that a holder paid to the watcher party and has not withdrawn. (§9.2)

</div>

<div class="req" markdown="1">

<a id="R-25"></a>**R-25.** A payment to the issuer ends as a note owned by the issuer's identity, and a payout record in the issuer state points to it. (§9.2, §9.4)

</div>

<div class="req" markdown="1">

<a id="R-26"></a>**R-26.** Every signed act of the watcher party is a binding commitment, even if a later root fails to account for it. The zkEVM adds conservation; the signature already commits the watcher party to n shares for y USDC. (§9.2, §11)

</div>

<div class="req" markdown="1">

<a id="R-29"></a>**R-29.** A hub names the wrapped-scrip factory in its asset list, so wrapped-scrip contracts need no per-token listing. (§10)

</div>

<div class="req" markdown="1">

<a id="R-30"></a>**R-30.** Value moves as routed payments, which are atomic. No fronting and no hash lock. (§9.3)

</div>

<div class="req" markdown="1">

<a id="R-35"></a>**R-35.** A buyer's USDC stays in armada-nitro. Paying in moves it to the watcher party's allocation in the buyer's own channel. No app reads an issuer root, because the signatures are binding. (§9.2)

</div>

<div class="req" markdown="1">

<a id="R-41"></a>**R-41.** The adjudicator works as normal, and no channel state is published. (§9.2)

</div>

<div class="req" markdown="1">

<a id="R-46"></a>**R-46.** The issuer state reads armada-nitro through a system contract, the channel-balance contract. The watcher party updates it from co-signed states it receives over Nitro p2p. (§9.2)

</div>


</details>


### Privacy

Confidentiality comes from one state per issuer, blinded blob data and the watcher parties.

<details markdown="1">
<summary>R-4, R-16, R-22, R-36 (4 requirements)</summary>

<div class="req" markdown="1">

<a id="R-4"></a>**R-4.** Confidentiality comes from one state per issuer, blinded blob data and the watcher parties, not from an access gateway. (§3, §7)

</div>

<div class="req" markdown="1">

<a id="R-16"></a>**R-16.** Blob data is blinded tree data, not ciphertext. Breaking it reveals no record. (§7)

</div>

<div class="req" markdown="1">

<a id="R-22"></a>**R-22.** The issuer sees every counterparty's private data. No counterparty sees another counterparty's private data. (§8.4)

</div>

<div class="req" markdown="1">

<a id="R-36"></a>**R-36.** MetaLeX is a plaintext audience through the MetaLeX hub. (§4.1, §8.4)

</div>


</details>


### Holder access and self-help

Each holder reads and proves its own records against roots on Ethereum, with or without an operator.

<details markdown="1">
<summary>R-9, R-10, R-15, R-19, R-20, R-21, R-31, R-33, R-37 (9 requirements)</summary>

<div class="req" markdown="1">

<a id="R-9"></a>**R-9.** Blinded blob data on L1 lets holders use self-help without any watcher member. Plaintext availability rests on t watcher members. (§4.2, §7)

</div>

<div class="req" markdown="1">

<a id="R-10"></a>**R-10.** Each batch commits to an events root. (§3, §8)

</div>

<div class="req" markdown="1">

<a id="R-15"></a>**R-15.** The party RPC gives each holder its records and proofs. If the issuer, MetaLeX core and the recovery operator all fail, blob data on L1 lets the holder use self-help. (§7, §8)

</div>

<div class="req" markdown="1">

<a id="R-19"></a>**R-19.** Delivery to holders is through the party RPC. (§8.1)

</div>

<div class="req" markdown="1">

<a id="R-20"></a>**R-20.** The MetaLeX hub mediates authentication by supplying user IDs to each issuer state. (§8.1)

</div>

<div class="req" markdown="1">

<a id="R-21"></a>**R-21.** A user signs its own requests to the party RPC, and the MetaLeX hub is not in the request path. (§8.1)

</div>

<div class="req" markdown="1">

<a id="R-31"></a>**R-31.** The party RPC's standard endpoint and the browser absorb most changes to MetaLeX's web app. Each watcher party does its own indexing. (§8.6)

</div>

<div class="req" markdown="1">

<a id="R-33"></a>**R-33.** Most users use the website. Users who want to keep their records use Armada's mobile app, which contains the website and a watcher. Browser storage holds the same data, but it is more easily deleted. (§7, §8.3)

</div>

<div class="req" markdown="1">

<a id="R-37"></a>**R-37.** The MetaLeX hub runs watchtowers for website users by default. The recovery operator may run them too. The mobile app runs its own. (§9.1)

</div>


</details>


### Publishing

Once a day, one transaction posts all roots and the blinded data, and an issuer with no changes posts nothing.

<details markdown="1">
<summary>R-8, R-17, R-18, R-43 (4 requirements)</summary>

<div class="req" markdown="1">

<a id="R-8"></a>**R-8.** The tree hash is pluggable, and the documents state why each hash is better or worse. (§13)

</div>

<div class="req" markdown="1">

<a id="R-17"></a>**R-17.** An issuer with no transactions publishes no blob. (§5.2, §6.2)

</div>

<div class="req" markdown="1">

<a id="R-18"></a>**R-18.** Daily aggregation by MetaLeX core is the default. (§6.2)

</div>

<div class="req" markdown="1">

<a id="R-43"></a>**R-43.** Publishing is aggregated: once per day, one root of party roots and the blobs of all parties, packed by size. (§6.2, §7)

</div>


</details>


### Trust without the zkEVM

A zkEVM is not required for a reasonable level of safety, and a public proof shows that every scrip token outside the issuer state has locked scrip behind it.

<details markdown="1">
<summary>R-7, R-23, R-28, R-44 (4 requirements)</summary>

<div class="req" markdown="1">

<a id="R-7"></a>**R-7.** ZisK stays a candidate zkVM. (§11)

</div>

<div class="req" markdown="1">

<a id="R-23"></a>**R-23.** A zkEVM is not required for a reasonable level of safety, so it is out of scope. (§11)

</div>

<div class="req" markdown="1">

<a id="R-28"></a>**R-28.** A conservation proof, verified off chain, shows conservation of wrapped scrip without the zkEVM. (§11)

</div>

<div class="req" markdown="1">

<a id="R-44"></a>**R-44.** Third parties verify the conservation proof independently, with a tool that reads a root and the supply of wrapped scrip from L1. (§11)

</div>


</details>


### Departure

A departure uses the holders' own stores and imports nothing on Ethereum.

<details markdown="1">
<summary>R-39 (1 requirement)</summary>

<div class="req" markdown="1">

<a id="R-39"></a>**R-39.** A departure uses the holders' own stores and the MetaLeX hub's copy of the state. Nothing is imported on L1. (§5.5)

</div>


</details>


### Limits

A refused write has no remedy on Ethereum.

<details markdown="1">
<summary>R-40 (1 requirement)</summary>

<div class="req" markdown="1">

<a id="R-40"></a>**R-40.** A payer whose write is refused gets no asset and has no L1 remedy. Its evidence goes to MetaLeX. (§8.5)

</div>


</details>


### Acceptance

MetaLeX accepts both lists, and each requirement shows where it comes from.

<details markdown="1">
<summary>R-32 (1 requirement)</summary>

<div class="req" markdown="1">

<a id="R-32"></a>**R-32.** MetaLeX accepts the invariants, and the invariants show which come from MetaLeX's plan and which from the operator's design requirements. (`metalex/invariants.md`)

</div>


</details>

