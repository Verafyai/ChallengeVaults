# Challenge Vaults

**Bounties on unsolved math, paid out by proof.**

Challenge Vaults puts open Erdős problems into a chain. Each problem is stated in Lean and locked in a vault. Anyone, human or AI, can submit a proof or a disproof. The first submission the Lean kernel accepts opens the vault, mints the block, and records permanent credit for whoever wrote it.

This repo contains an interactive, single-file demo of the idea (called *Proof Chain* on the page) and the design behind it.

![Provers racing to prove the Erdős–Turán problem while the kernel rejects submissions](docs/images/mining.png)

> **This is a simulation.** The ten problems are real and were open when this was built; check [erdosproblems.com](https://www.erdosproblems.com) for current status. Every submission, proof and result in the demo is simulated, and every minted block is labeled "simulated result". The Lean statements are simplified sketches. Real formalizations of many Erdős problems live in Google DeepMind's [Formal Conjectures](https://github.com/google-deepmind/formal-conjectures) library.

## Quick start

No build step and no dependencies beyond Google Fonts.

```bash
git clone https://github.com/Verafyai/ChallengeVaults.git
cd ChallengeVaults
open index.html          # macOS; or double-click it, or:
python3 -m http.server 8000   # then visit http://localhost:8000
```

Then press **Start mining**. The full walkthrough is in [docs/how-to.md](docs/how-to.md).

## How it works

Each block is one open problem. A submission passes only if all four kernel checks pass:

1. **Statement matches the block's hash.** The Lean statement is hashed and locked into the vault, so nobody can claim it by proving an easier version.
2. **No `sorry`.** Lean's placeholder for a missing step is rejected.
3. **Only standard axioms:** `propext`, `Classical.choice`, `Quot.sound`. Declaring your result as an axiom proves nothing.
4. **The kernel type-checks the proof term.**

| An accepted proof | Three rejected shortcuts |
|---|---|
| ![All four kernel checks passing](docs/images/accepted.png) | ![sorry rejected](docs/images/shortcut-sorry.png) |

When a proof passes, the block is minted, linked to the previous block's hash, and credited to its prover:

![All ten blocks minted, with the credit leaderboard](docs/images/chain-and-credit.png)

## The problems

| Block | Problem |
|---|---|
| 0 | Erdős–Straus conjecture |
| 1 | Erdős–Moser equation |
| 2 | Erdős–Gyárfás conjecture |
| 3 | Erdős–Turán on additive bases |
| 4 | Erdős conjecture on arithmetic progressions |
| 5 | Odd covering problem |
| 6 | Erdős–Ulam problem |
| 7 | Happy ending problem |
| 8 | Unit distance problem |
| 9 | Erdős–Hajnal conjecture |

## Why vaults, not proof of work

Lean makes verification cheap, exact and objective, which most "useful work" schemes lack. But open problems can't serve as proof-of-work puzzles: their difficulty can't be scheduled, proof search rewards insight rather than random trials, and a published proof can be copied instantly. So consensus stays on an existing chain and the vaults sit on top, with commit-reveal submissions and a challenge window. See [docs/design.md](docs/design.md).

## Docs

- [How to use the demo](docs/how-to.md): every control, what to try, and how to change the problems
- [Design](docs/design.md): the vault model, its threat model, and what a real deployment needs

## Related

- [Verafyai/PromptMining](https://github.com/Verafyai/PromptMining): the generative-blockchain demos this grew out of

## License

MIT. See [LICENSE](LICENSE).
