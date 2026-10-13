<!-- solve-problem:b60aa209-5658-4965-adf9-be4a711dbd46 -->
# Single-exponential, polynomial-space exact SVP

A [Tangle Solve problem](https://solve.tangle.tools/problems/b60aa209-5658-4965-adf9-be4a711dbd46).

## What is the problem?

The Shortest Vector Problem (SVP): given a basis \(B \in \mathbb{Q}^{n \times n}\) of a full-rank lattice \(\mathcal{L}(B) = \{Bz : z \in \mathbb{Z}^n\}\) in \(\mathbb{R}^n\) with the Euclidean norm, output a nonzero vector \(v \in \mathcal{L}(B)\) achieving \(\|v\| = \lambda_1(\mathcal{L}) := \min_{u \in \mathcal{L}\setminus\{0\}} \|u\|\) (exact, not approximate — output as an integer combination of basis vectors, so exact rational arithmetic is fine; bit-complexity is polynomially related to input size).

**Goal:** an algorithm running in \(2^{O(n)} \cdot \mathrm{poly}(\text{input size})\) time using only \(\mathrm{poly}(n, \text{input size})\) space. Two subtleties: (1) "space" is most naturally Turing machine space (including read-only input tape excluded or included — state which); (2) the algorithm need not be deterministic a priori — a randomized \(2^{O(n)}\)-time, poly-space algorithm with bounded error would also be a major breakthrough, though the deterministic version (matching the Voronoi-cell algorithm's guarantee) is the cleanest target. The problem as usually posed (see Micciancio's SVP open problems page) is about the time–space tradeoff: no known algorithm simultaneously achieves single-exponential time and polynomial space.

## Why does it matter?

SVP is the computational core of lattice-based cryptography: concrete security estimates for NIST-standardized schemes (Kyber, Dilithium, and older ones like the SVP challenges) hinge on the time–space profile of exact/near-exact SVP solvers — the current best practical attacks (via BKZ + enumeration or sieving) need either \(2^{\Theta(n \log n)}\) time (enumeration, low memory) or \(2^{\Theta(n)}\) time with \(2^{\Theta(n)}\) memory (sieving), and memory is the binding practical bottleneck. A positive answer would change cryptanalytic cost models; a lower bound (e.g., time–space product \(\ge 2^{\omega(n)}\) in some model) would justify current memory-heavy attack estimates. It is also the flagship open question in the complexity of lattice algorithms.

## What's already known?

- **Single-exponential time, exponential space:** Micciancio–Voulgaris (2013) give a deterministic \(2^{O(n)}\)-time algorithm for exact SVP (and CVP) via computation of the Voronoi cell of the lattice, represented by its \(2^{O(n)}\) relevant vectors — the space usage is \(2^{O(n)}\). Sieving algorithms (randomized, e.g. AKS is \(2^{O(n)}\) time with large polynomial factors; modern sieves are \(2^{0.29n+o(n)}\)-ish time) also store exponentially many vectors.
- **Polynomial space, super-exponential time:** Fincke–Pohst/Kannan-style enumeration, combined with LLL/BKZ-reduced bases and good bounds on the Gaussian heuristic, solves exact SVP deterministically in \(n^{O(n)}\) time and polynomial space (Kannan 1983 gives \(n^{O(n)}\); Hanrot–Stehlé improved constants). This is the standard practice in cryptanalytic tools (e.g., fplll's enumeration).
- **Middle ground:** Time–space tradeoffs for enumeration and for "sieving with reduced memory" exist, but nothing achieves \(2^{O(n)}\) time at polynomial space. Space-efficient recomputation tricks (e.g., recompute rather than store Voronoi-relevant vectors) are not known to stay single-exponential because the Voronoi cell construction inherently builds and reuses an exponential-size object.
- **Lower bounds:** essentially nothing is known ruling out \(2^{O(n)}\) time and poly space, even under complexity assumptions.

## What would count as solving it?

Any of the following would resolve the census item (in decreasing strength):
1. A deterministic (or bounded-error randomized) algorithm for exact SVP in \(n\)-dimensional lattices running in \(2^{c n}\,\mathrm{poly}(\text{input})\) time and \(n^{O(1)}\,\mathrm{poly}(\text{input})\) space, with explicit constants, plus a correctness proof.
2. A conditional or unconditional lower bound showing, e.g., time–space tradeoff \(T \cdot S \ge 2^{\omega(n)}\) or \(T \ge 2^{\Omega(n \log n)}\) whenever \(S \le n^{O(1)}\), in a defined model (Turing machine, or branching program with the natural lattice-comparison oracle).
3. Partial results: a \(2^{O(n)}\)-time, \(2^{o(n)}\)-space algorithm, or a \(2^{O(n \log n / \log\log n)}\)-time poly-space algorithm (beating Kannan) would be significant progress worth reporting.

## How to start

1. Read Micciancio–Voulgaris (2013), Sections on the Voronoi cell construction (the "Voronoi cell of a lattice via the CVP solver" bootstrapping), to understand exactly where \(2^{O(n)}\) space is used: the list of \(2^{O(n)}\) Voronoi-relevant vectors and the membership oracle over it.
2. Ask whether the relevant-vector list can be generated on the fly, exponentially many times, each time in \(2^{O(n)}\) time but poly space — i.e., an implicit/iterable representation of the Voronoi cell. The main obstacle: the MV algorithm's iterations need *queries* into this list, and each query seems to require solving an implicit CVP.
3. Alternatively, attack the enumeration side: the \(n^{O(n)}\) bound comes from bounding the enumeration tree size using basis quality (pruning via Gaussian-heuristic-style counting, à la Gama–Nguyen–Regev's extreme pruning). Try to prove that some basis-reduction-plus-pruning schedule always runs in \(2^{O(n)}\) expected time for poly-space enumeration; obstacle: worst-case lattice families (e.g., knapsack-type or "hard" random q-ary lattices) are conjectured to force \(n^{\Theta(n)}\) nodes.
4. For the lower-bound direction, look at time–space lower-bound techniques (pebbling-style arguments, branching-program size for explicit functions) and see whether SVP has a natural branching-program formulation; this is high-risk.

## Key references

- D. Micciancio, P. Voulgaris, *A Deterministic Single Exponential Time Algorithm for Most Lattice Problems Based on Voronoi Cell Computations*, STOC 2010 / SIAM J. Comput. 42(3), 2013. Deterministic \(2^{O(n)}\)-time exact SVP/CVP via the Voronoi cell representation; uses \(2^{O(n)}\) space. https://arxiv.org/abs/0909.2213
- D. Micciancio's open problems page on SVP (*"Lattice algorithms and complexity*, open problem list listing the single-exponential/polynomial-space question. (See his UCSD page; exact URL to verify.)
- R. Kannan, *Improved Algorithms for Integer Programming and Related Lattice Problems*, STOC 1983. \(n^{O(n)}\)-time, polynomial-space exact SVP via enumeration with Hermite-Korkine-Zolotarev reduced bases.
- G. Hanrot, D. Stehlé, *Improved Analysis of Kannan's Shortest Lattice Vector Finding Algorithm*, ANTS 2008 / Inf. Process. Lett. 2009. Sharpens enumeration's \(n^{O(n)}\) analysis for poly-space.
- N. Gama, P. Q. Nguyen, O. Regev, *Lattice Enumeration Using Extreme Pruning*, EUROCRYPT 2010. Discrete-Gaussian-based pruning that makes enumeration near-optimal in practice but with heuristic worst-case guarantees. https://arxiv.org/abs/1003.3635
- (verify) For randomized sieving with reduced memory: look at literature on "sieving time-memory tradeoffs" (e.g., work of Ducas, Laarhoven, and others, 2015–2020).

## Warnings

- Don't conflate approximation algorithms: LLL gives polynomial time with exponential approximation factors — irrelevant here; the problem demands the exact shortest vector.
- "Single-exponential" means \(2^{O(n)}\), not \(n^{O(n)}\); a poly-space algorithm at \(2^{O(n \log\log n)}\) would not solve it (though it would be progress).
- Fix the machine model up front (Turing vs. RAM; whether random access to an exponential-space read-only table counts) — sloppy space accounting is the classic error in this area.
- Beware heuristics: pruned enumeration analyses rely on the Gaussian heuristic and fail on adversarial lattices; any claimed worst-case \(2^{O(n)}\) bound must handle all inputs.
- The Voronoi cell has \(2^{\Theta(n)}\) relevant vectors in the worst case, so any approach storing it explicitly cannot be polynomial space.

---
*`ALGO-000205` · unverified*

## Acceptance guidance

Any of the following would resolve the census item (in decreasing strength):
1. A deterministic (or bounded-error randomized) algorithm for exact SVP in \(n\)-dimensional lattices running in \(2^{c n}\,\mathrm{poly}(\text{input})\) time and \(n^{O(1)}\,\mathrm{poly}(\text{input})\) space, with explicit constants, plus a correctness proof.
2. A conditional or unconditional lower bound showing, e.g., time–space tradeoff \(T \cdot S \ge 2^{\omega(n)}\) or \(T \ge 2^{\Omega(n \log n)}\) whenever \(S \le n^{O(1)}\), in a defined model (Turing machine, or branching program with the natural lattice-comparison oracle).
3. Partial results: a \(2^{O(n)}\)-time, \(2^{o(n)}\)-space algorithm, or a \(2^{O(n \log n / \log\log n)}\)-time poly-space algorithm (beating Kannan) would be significant progress worth reporting.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/b60aa209-5658-4965-adf9-be4a711dbd46?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: ALGO-000205.

- [Public source catalog](https://drewstone.github.io/problems/#/p/ALGO-000205)
