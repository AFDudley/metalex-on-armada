# Summary

Status: draft, 2026-11-09. Not built.

## The proposal

MetaLeX wants to run its Cybercorps contracts privately, with an independent operator who can recover every issuer's records. MetaLeX's own plan does this with one shared private chain on ZKsync Prividium. This proposal does it differently, with the same contracts and no changes to them.

Each issuer gets its own EVM state. Three operators run it together: MetaLeX, an independent recovery operator, and the issuer. This group is the issuer's *watcher party*. Once a day, the watcher party posts a fingerprint of the state, its *root*, to a contract on Ethereum. Any two of the three operators can sign a root, so no single operator can stop an issuer or change its records alone.

Money does not live in the private state. It stays on Ethereum in armada-nitro, Armada's payment system: shielded notes, and payment channels funded from notes. A buyer's USDC stays in the buyer's own channel until the deal settles.

## What MetaLeX and its users get

- **Privacy between issuers.** Each issuer's records are in a separate state. No issuer can read another issuer's data through any interface.
- **Recovery without MetaLeX.** The recovery operator and the issuer hold the full state, and either can continue with the other.
- **Self-help for holders.** Each holder keeps proofs of its own records. If every operator fails, a holder can still prove what it owns, from its own records, public blob data and Ethereum.
- **Safe money.** No operator can take a user's funds. A user can always exit a channel on Ethereum with its latest signed state.
- **Departure.** An issuer can leave for a new set of operators. Its holders prove their records to the new operators.

## How it differs from Prividium

Prividium proves every batch with a zkEVM on GPU hardware. This proposal does not, because those provers cost the most to operate. It relies on three things instead:

- every root is a signed commitment that the operators cannot deny;
- each holder checks its own records against every root;
- a published conservation proof shows that no payment token or scrip exists without backing. Anyone can check it.

So a dishonest majority of operators could sign a wrong root, and Ethereum would accept it. But every holder would detect the change, and the signed root is evidence against those operators. Prividium's zkEVM would reject such a root. A zkEVM can be added later if MetaLeX wants that protection.

Prividium keeps transaction data and state off chain, in the operators' databases. So a Prividium holder needs an operator to read or prove its records. In this proposal, a holder does not.

## What it costs

On the same basis as MetaLeX's own cost tables, this design costs about $4,400 per month. That includes $1,449 that the issuers pay for their own watcher members. Prividium's three scenarios cost $4,600, $7,700 and $11,300 per month. The cost comparison gives every line.

## What MetaLeX must trust

- **The operators,** for correct records. A wrong record is detectable and provable, but Ethereum does not block it.
- **The sequencer,** to accept writes. A refused write has no remedy on Ethereum. The user's evidence goes to MetaLeX.
- **A watchtower,** to protect an offline user's channel. The MetaLeX hub runs one for website users.

## What MetaLeX must decide

1. Which companies share one issuer state.
2. Whether to accept the requirements: the 65 from MetaLeX's plan, and the 45 from this design.
