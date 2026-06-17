---
layout: page
title: Past Talks | Jakub Smolik
---

# Past Talks

### 2026/6/18: Defense of my bachelor's thesis

Materials: [thesis](../bc-thesis.pdf) and [slides](./2026-6_bachelor_thesis_defense.pdf)

<details>
<summary>Abstract</summary>

This thesis studies well-quasi-orderings and better-quasi-orderings of graphs under various containment relations. We give a direct proof that out-trees are wqo by the homomorphism relation via transfinite induction on a hierarchy of trees that we introduce, and we investigate extensions of this approach to leaf-labeled trees. For graphs, we analyze structural parameters that yield natural wqo classes. In particular, we extend a theorem of Ding by proving that every class of (finite or infinite) graphs with bounded tree-depth is wqo by the induced subgraph relation, and we show that graph classes with bounded independence number are wqo by the subgraph relation. Lastly, we consider generalizations of well-quasi-orderings motivated by the large cardinal axiom known as Vopěnka's principle.

</details>

### 2026/5/22: Introduction to WQO theory

Given at [Seminar on Combinatorics](https://kam.mff.cuni.cz/~ksemweb/)

Materials: [notes](./2026-5_wqo.pdf)

<details>
<summary>Abstract</summary>

We define well-quasi-orderings and prove several equivalent characterizations. We then explain how wqos allow monotone properties to be characterized by a finite set of minimal obstructions, as exemplified by the celebrated graph minor theorem of Neil Robertson and Paul Seymour. Finally, we prove Higman's lemma, Kruskal's tree theorem, and Ding's theorem. If time permits, we will also discuss how wqo theory extends to infinite structures through the notion of better-quasi-orderings.

</details>

### 2026/5/20: An elementary proof of Stirling’s formula

Given at [Optimization Seminar](https://kam.mff.cuni.cz/~hladik/OS/)

Materials: [notes in Czech](https://raw.githack.com/Couleslaw/my-papers/main/cs/Stirlings_Formula_CS.pdf) and [partial notes in English](https://raw.githack.com/Couleslaw/my-papers/main/en/Stirlings_Formula_EN.pdf)

<details>
<summary>Abstract</summary>

In the first part of the talk, we will prove Stirling's approximation of the factorial using elementary methods such as limits and Taylor series.
<br>
<br>
The second part of the talk will be about real numbers. We consider a graph <a href="https://doi.org/10.1016/S0097-3165(03)00102-X">proposed by Shelah and Soifer</a> $G=(\mathbb R,\,E)$, where $x$ and $y$ are adjacent if $|x-y|=q+\sqrt 2$ for some rational number $q$. We show that in $\mathsf{ZFC}$, the chromatic number is $\chi(G)=2$. In contrast, under the assumption that every subset of $\mathbb R$ is Lebesgue measurable (which is consistent relative the existence of an inaccessible cardinal), the chromatic number becomes uncountable.
<br>
<br>
Both the axiom of choice $\mathsf{AC}$ and the axiom of measurability $\mathsf{LM}$ are consistent and both have different paradoxical consequences. $\mathsf{AC}$ allows us to construct non-measurable sets, resulting in the Banach-Tarski paradox. This is fixed when we assume $\mathsf{LM}$, but at the cost of losing objects like non-principle ultrafilters on $\mathbb N$.

</details>

### 2026/4/13: A Proof of the 3/4-conjecture for the total domination game

Given at [Spring School of Combinatorics](https://kam.mff.cuni.cz/~spring/2026/)

Materials: [handouts](./2026-4_3-over-4-conjecture_handout.pdf), [slides](./2026-4_3-over-4-conjecture_slides.pdf) and [notes](./2026-4_3-over-4-conjecture_notes.pdf)

<details>
<summary>Abstract</summary>

Let $G=(V,\,E)$ be a graph without isolated vertices. A vertex $v$ is <i>totally dominated</i> by a set $A\subseteq V$ if it has a neighbour in $A$. The total domination game on $G$ is played by $2$ players, Dominator and Staller, who alternate in selecting vertices such that each newly selected vertex increases the number of vertices that are totally dominated by the set of selected vertices $A$. The game stops when $A$ totally dominates every vertex of $G$. Dominator's aim is to minimize $|A|$, while Staller wants to maximize it. The <i>game total domination number</i> $\gamma_{tg}(G)$ is the number of vertices in the resulting set when Dominator starts the game and both players play optimally. Henning, Klavžar, and Rall proved that if $G$ has no isolated vertices or edges and $|V|=n$, then $\gamma_{tg}(G)\le \frac{4}{5}n$, and they conjectured that in fact $\gamma_{tg}(G)\le \frac{3}{4}n$. Portier and Versteegen <a href="https://doi.org/10.1137/23M1551584">recently confirmed this conjecture</a>.

</details>

### 2026/3/27: Geometric graphs with exponential chromatic number and arbitrary girth

Given at: [Seminar on Combinatorics](https://kam.mff.cuni.cz/~ksemweb/)

Materials: [notes](./2026-3_geometric-graphs_notes.pdf)

<details>
<summary>Abstract</summary>

The Hadwiger–Nelson problem asks for the chromatic number of the plane — the minimum number of colors needed to color $\mathbb{R}^2$ so that no two points at distance $1$ receive the same color. While unit-distance graphs with chromatic number $4$ are easy to construct, Erdős (1975) asked whether such graphs can avoid triangles, i.e., have girth (the length of a shortest cycle) at least $4$. This was answered affirmatively by subsequent constructions, culminating in O’Donnell’s 1999 result showing the existence of $4$-chromatic unit-distance graphs with arbitrary girth.
<br>
<br>
This connects to Erdős’s classical 1958 theorem that graphs with arbitrarily large girth and chromatic number exist. A natural question is whether this persists for unit-distance graphs: graphs whose vertices can be embedded into $\mathbb R^d$ so that the endpoints of each edge are at distance $1$. The best known lower bound on the chromatic number in this setting is due to Raigorodskii (2000), who showed that $\chi(G)\ge (1.239+o(1))^d$ is achievable. Kupavskii (2012) proved that for every $g$ there exists $c_g>1$ such that there exist unit-distance graphs in $\mathbb{R}^d$ with girth at least $g$ and $\chi(G)\ge (c_g+o(1))^d$, although known estimates of $c_g$ tend to 1 as $g$ grows.
<br>
<br>
In this talk I present a <a href="https://doi.org/10.1080/00029890.2025.2541528">recent paper by Matija Bucić and James Davies</a> in which they prove the existence of unit-distance graphs in $\mathbb R^d$ with chromatic number at least $(1.074+o(1))^d$ that have arbitrarily large girth. The same construction also yields graphs with large chromatic number and high girth in other geometric settings, namely diameter graphs and orthogonality graphs.

</details>

### 2026/3/18: A short proof of the Halpern–Läuchli theorem

Given at: [Seminar on Reckoning](https://www.math.cas.cz/index.php/events/seminar/15)

Materials: [handouts](./2026-3_Halpern-Lauchli_sheet.pdf) and [notes](./2026-3_Halpern-Lauchli_notes.pdf)

<details>
<summary>Abstract</summary>

A classical product Ramsey theorem states that for every $n$ there exists $N$ such that every $2$-coloring of the edges of $K_{N,N}$ contains a monochromatic copy of $K_{n,n}$. The most direct infinite analogue fails: one can easily construct a $2$-coloring of the product $\omega\times\omega$ that contains no monochromatic copy of $\omega\times\omega$. This raises the question whether a meaningful infinite product Ramsey theorem exists. The Halpern–Läuchli theorem provides such a result for level products of finitely branching trees of height $\omega$ with no leaves. It guarantees the existence of monochromatic strong subtrees (subtrees preserving part of the branching structure of the original trees) of height $\omega$.
<br>
<br>
In this talk I will present a proof of the theorem due to Stevo Todorčević, following a <a href="https://www.math.toronto.edu/sunger/halpern-lauchli.pdf">recent exposition by Spencer Unger</a>, which was explained to me by Jan Hubička. If time permits, I will also show how the Halpern–Läuchli theorem implies Milliken's tree theorem, a Ramsey-style theorem in which Halpern–Läuchli plays the role of the pigeonhole principle.

</details>
