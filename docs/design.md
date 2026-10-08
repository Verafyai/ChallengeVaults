# Design

The demo simulates a chain mined by proofs. This page describes what a real Challenge Vaults system would need, and why it is built as vaults on an existing chain rather than as its own proof-of-work chain.

## The vault

A vault holds:

| Field | Purpose |
|---|---|
| `statement` | The Lean statement of the problem, with its imports and Mathlib version pinned |
| `statement_hash` | Hash of the above; a submission must prove exactly this |
| `bounty` | Funds locked until the vault opens (prize money, sponsor pools) |
| `status` | Open, challenged, or settled |
| `credit` | The prover's identity and the proof's hash, once settled |

A submission is accepted when a verifier, running the pinned Lean toolchain, confirms:

1. The proved theorem's statement hashes to `statement_hash`.
2. The proof contains no `sorry`.
3. `#print axioms` reports only `propext`, `Classical.choice` and `Quot.sound`.
4. The kernel type-checks the proof.

A disproof (a proof of the negated statement) settles the vault too.

## Why it is not proof of work

Lean solves the hardest part of any "useful work" scheme: who decides the work is correct? But open problems lack the properties proof of work depends on.

- **No difficulty adjustment.** Bitcoin retunes difficulty to hold steady block times. An open problem's difficulty is unknown and unbounded; a chain could stall for decades on one block.
- **Not a lottery.** Each hash attempt is an independent coin flip, so hash power wins proportionally. Proof search rewards insight and accumulated work, so the best-funded lab would win almost everything.
- **Doesn't secure history.** Proof of work protects a ledger because rewriting it means redoing the work. A published proof can be copied and re-verified instantly, so forking costs nothing.
- **Finite supply.** There are roughly a thousand Erdős problems, and no way to generate fresh ones of known difficulty.

Primecoin (2013) shows what useful-work proof of work actually requires: an endless, parameterized family of puzzles with tunable difficulty. Open problems are the opposite.

So consensus belongs to an existing chain, and the vaults are contracts on top of it.

## Threats and mitigations

| Threat | Mitigation |
|---|---|
| **Front-running.** Someone copies a broadcast proof and submits it first. | **Commit-reveal.** Submit a hash of the proof plus the prover's address first; reveal the proof after the commitment is final. The earliest valid commitment wins. |
| **Misformalization.** A subtly wrong statement may be trivially provable, letting someone drain a vault. | **Challenge window** before payout, during which anyone can show the statement doesn't match the intended problem. Use reviewed statements, such as those in Formal Conjectures, and require a review period before a vault opens. |
| **Verifier dishonesty.** One verifier lies about a check. | Several independent verifiers re-run the check with the pinned toolchain; disagreement triggers a rehearing. Verification is deterministic, so honest verifiers always agree. |
| **Toolchain drift.** A proof breaks or changes meaning under a new Lean or Mathlib version. | Pin the exact Lean and Mathlib versions in each vault. |

## A difficulty ladder

Rare breakthroughs don't give contributors a steady flow of rewards. Pairing vaults with smaller, tractable goals does:

- Lemmas on the path to an open problem
- Known results that haven't been formalized in Mathlib
- Special cases and bounds that a problem's literature already suggests

Credit for an eventual solution can then trace back through every verified lemma it used, rewarding contributors who aren't the one landing the final step. This is a natural credit layer for a volunteer proving swarm, the "Folding@home for math" idea, where idle agents contribute lemmas and the kernel verifies each one.

## What the demo simplifies

- Lean statements are hand-written sketches, not checked formalizations, and some reference definitions (`GeneralPosition`, `unitPairs`) that are not defined in the file.
- Submissions, errors and outcomes are randomly generated.
- Hashes use a fast non-cryptographic function, not SHA-256.
- There is no commit-reveal step, challenge window or bounty.
