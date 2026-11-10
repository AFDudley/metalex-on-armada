# Cost comparison: MetaLeX's plan and this design

Status: draft for review, 2026-11-09. Nothing in this design is built, so no figure for it is measured.

This document compares the monthly cost of two ways to run MetaLeX's Cybercorps contracts privately:

- **Prividium:** MetaLeX's own plan (`mlx`). One shared private chain on ZKsync Prividium and the ZK Stack, with a GPU prover.
- **This design:** issuer states run by watcher parties and anchored to L1 (`system-design.md`). No zkEVM and no GPU.

Section numbers refer to `system-design.md`. Terms follow `glossary.md`.

## 1. Basis

The comparison prices both designs on the same basis, so that a line differs only where the architecture differs.

| Rule | Value |
|---|---|
| Load | 10 issuers, 1,000 holders, 10,000 transactions per month, about 0.0038 transactions per second (`mlx` §1, line 322) |
| Operators | MetaLeX and the recovery operator in both designs. In Prividium, issuers run nothing: "Issuers receive scoped access and issuer-specific continuity bundles, not full-state nodes or backups" (`mlx` line 176). In this design, each issuer also runs a watcher member (§4.1), so this design has one more line, paid by the issuers. |
| Machines | `mlx` Table 14's DigitalOcean rates, machine sizes and machine counts, checked by MetaLeX on 7 November 2026 (`mlx` lines 301-315). The counts are for availability. |
| Reserve | 15% on infrastructure, as `mlx` line 314 |
| L1 | Daily posting, $3,000/ETH, 1 gwei and 10 gwei, as `mlx` line 324 |

**Check of the method.** The Prividium columns below reproduce `mlx` Table 15 (lines 318-321). The full-warm total differs by one cent, from rounding.

## 2. Monthly cost

| Monthly cost, USD | Prividium: scheduled proving | Prividium: warm CPU, on-demand recovery GPU | Prividium: full warm | This design |
|---|---:|---:|---:|---:|
| Sequencer and standbys, 3 × $252. This design: MetaLeX core's harness and its standby, and the recovery operator's harness. | 756.00 | 756.00 | 756.00 | 756.00 |
| State DBs, 3 × $336. This design: none, because each harness keeps its own state (§4.3). | 1,008.00 | 1,008.00 | 1,008.00 | 0.00 |
| RPC/permissions, 3 × $126. This design: party RPC endpoints (§8.1). | 378.00 | 378.00 | 378.00 | 378.00 |
| Index/export/verifier, 2 × $126. This design: also runs the conservation prover (§11). | 252.00 | 252.00 | 252.00 | 252.00 |
| MetaLeX hub: channel node and watchtower, active and standby, 2 × $126 (§9) | — | — | — | 252.00 |
| Monitoring, administrative access, load balancers | 174.00 | 174.00 | 174.00 | 174.00 |
| Block storage, 4,500 GiB | 450.00 | 450.00 | 450.00 | 450.00 |
| Object stores. This design: the blob archive. | 40.00 | 40.00 | 40.00 | 40.00 |
| Key service. This design: protection of the FROST shares. | 20.30 | 20.30 | 20.30 | 20.30 |
| Logs, data transfer, Ethereum RPC | 225.00 | 225.00 | 225.00 | 225.00 |
| Issuer watcher members, paid by the issuers, 10 × $126 (§4.1) | — | — | — | 1,260.00 |
| GPU provers at $4.41 per hour | 635.04 (144 h) | 3,325.14 (754 h) | 6,438.60 (1,460 h) | 0.00 |
| Subtotal | 3,938.34 | 6,628.44 | 9,741.90 | 3,807.30 |
| 15% reserve | 590.75 | 994.27 | 1,461.29 | 571.10 |
| **Infrastructure** | **4,529.09** | **7,622.71** | **11,203.19** | **4,378.40** |
| L1 at 1 gwei | 91.25 | 91.25 | 91.25 | 16.52–52.40 |
| **Total at 1 gwei** | **4,620.34** | **7,713.96** | **11,294.44** | **4,394.92–4,430.80** |
| L1 at 10 gwei | 912.50 | 912.50 | 912.50 | 165.24–524.03 |

**Prividium's L1 figure.** `mlx` line 324 uses 1,000,000 gas per daily aggregate as an illustrative input, not a measurement. Prividium is a validium, so it posts no blob data.

**This design's L1 figure** `[INFERENCE]`:
- **Transaction:** one blob transaction per day, about 50,000 gas. It calls the aggregator with one root of all party roots and the blob hashes (§6.2). A RootChain reads the aggregator only when an L1 action needs it.
- **Blob data:** each slot that changed since the previous root, once, as a 64-byte blinded position and leaf, with no interior nodes (§7). The upper bound is 330 transactions per day × 20 slots × 64 bytes = about 420 KB. That is 1–4 blobs per day, at 131,072 blob gas each.
- **Blob price:** blob gas has its own fee market. Pricing it at 1 gwei follows `mlx`'s execution-gas input and probably overstates it.

**What the table shows.**
- This design's infrastructure costs less than Prividium's cheapest scenario. It has no GPU prover and no separate state-DB tier. It adds the MetaLeX hub and the issuers' watcher members.
- Of this design's total, MetaLeX and the recovery operator pay $2,929.39 with the reserve. The issuers pay $1,449.00 with the reserve, about $145 each.
- The issuer line keeps `mlx`'s smallest machine class. A 1 vCPU / 1 GB machine at $6 (https://www.digitalocean.com/pricing/droplets) makes it $60, or $69 with the reserve. An issuer can run its watcher member in Armada's mobile app and pay no server cost. Its watcher member is then up only while the app runs (§4.1).
- This design's L1 cost is lower than Prividium's. It posts once per day, with leaf-only blob data.
- `mlx` marks its cheapest scenario as unproven: "measure startup, full proof pipeline, aggregation and acceptable finality" (line 319).

**Not in the table:**
- People. `mlx` gives $5k–16k per month for MetaLeX's maintenance and $1.2k–4k for the recovery operator, as unmeasured references (line 327).
- Public bridge fees, in both designs (`mlx` line 325).
- Working capital that only this design needs: the MetaLeX hub's channel capacity, sized to peak payment value (§9.3), and the party bonds (§4.4).

## 3. Operational complexity

| | Prividium | This design |
|---|---|---|
| Services | Sequencer with a fenced standby, primary and replica DB, RPC/permissions, index/export worker, GPU prover. The recovery operator runs its own fenced copy (`mlx` line 299). | One harness process per server: execution, state tree, storage and party RPC. The MetaLeX hub. The RootChain, aggregator and adjudicator are L1 contracts, with no service to operate. |
| Vendor release coupling | The chain, prover, verifier and settlement releases must match. P0 exists to establish that (`mlx` Table 01). | One pinned commit of Armada's nimbus-eth1 fork, the same code as Armada's light client, and one hardfork spec per issuer state (§3) |
| Proving hardware | Airbender needs at least 32 GB of VRAM (`mlx` line 33) | None |
| Recovery | A warm standby operator. "DNS failover is insufficient", and promotion must fence the old sequencer (`mlx` line 169). | Any t watcher members continue (§4.1). L1 accepts one successor per root, so no fencing step is needed (§6.1). |
| If every operator fails | Data and state are off chain, so holders have no path on L1 | Each holder proves its own records from its own store, the blobs and L1 (§8.3) |

## 4. What nothing measured

| Figure | Who states it is unmeasured |
|---|---|
| Prividium's machine counts and GPU hours | `mlx`: "Low-moderate" confidence, pending "representative proof, memory, storage, burst and recovery benchmarks" (line 279) |
| Prividium's settlement gas | `mlx`: "neither measured gas nor current market prices" (line 324) |
| This design's machine sizes | This comparison keeps `mlx`'s sizes. At 0.0038 transactions per second, both designs need much smaller machines `[INFERENCE]`. |
| This design's blob volume and daily gas | Estimates in §2 |
| The conservation prover's load | Its construction is not chosen (§11) |
| Channel capacity and bond size | Neither is estimated |
