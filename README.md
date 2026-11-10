# MetaLeX on Armada

Armada's proposal to run MetaLeX's Cybercorps contracts on Armada. Status: draft for review, 2026-11-09.

[`index.html`](index.html) holds all of the documents below as one self-contained page. Open it in a browser.

| File | Content |
|---|---|
| [`summary.md`](summary.md) | The proposal on one page: what MetaLeX gets, how it differs from Prividium, what it costs, what MetaLeX must trust and decide. Read this first. |
| [`guarantees.md`](guarantees.md) | For each party: what it can check, what it must trust, and what happens to it when another party fails |
| [`system-design.md`](system-design.md) | How MetaLeX's unchanged Cybercorps contracts run on Armada. Watcher parties run the issuer states and anchor them to Ethereum L1. Payments are armada-nitro Nitro payments, and scrip is armada-nitro notes. It also covers holder proofs, self-help, and what holds without a zkEVM. |
| [`cost-comparison.md`](cost-comparison.md) | The monthly cost of MetaLeX's Prividium plan and of this design, on the basis of MetaLeX's own cost tables. It also compares their operational complexity. |
| [`invariants.md`](invariants.md) | The requirements of the system for MetaLeX to accept. 65 come from MetaLeX's plan, each with its quotation. 46 come from Armada's design, each with the mechanism that holds it. |
| [`invariant-review.md`](invariant-review.md) | Armada's review of MetaLeX's private-chain plan: no contradictions among its requirements, three open points |
| [`glossary.md`](glossary.md) | The terms these documents use |

## Fonts

`index.html` embeds two fonts:

| Font | License |
|---|---|
| [Figtree](https://github.com/erikdkennedy/figtree), weights 400 and 600, as static instances | SIL Open Font License 1.1. The license text is in [`OFL-Figtree.txt`](OFL-Figtree.txt). |
| [Pixel Operator Mono](https://www.dafont.com/pixel-operator.font), by Jayvee Enaguas | Public domain |
