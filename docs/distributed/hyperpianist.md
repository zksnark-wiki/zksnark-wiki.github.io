# HyperPianist

## Distributed Multivariate PIOP Systems

Distributed multivariate PIOP for $\mathcal R_{Sum}$, $\mathcal R_{Zero}$ are trivial. As HyperPlonk utilizes the ProductCheck from Quarks to construct a MultisetCheck PIOP, it can't work in the distributed setting. (really?) But we can make use of distributed ration SumCheck PIOP.

### Distributed Rational SumCheck PIOP

> **Definition (Rational SumCheck Relation).** The relation $\mathcal{R}_{RSum}$ is the set of all tuples 
> 
> $$(\mathbb{x}; \mathbb{w}) = ((v, [[p]], [[q]]); (p, q))$$
>
> , where $p, q \in \mathbb{F}_n^{(\le d)}$, $q(x) \ne 0, \forall x \in \{0, 1\}^n$ and $\sum_{x \in \{0, 1\}^n} \frac{p(x)}{q(x)} = v$.

The prover can construct a polynomial $f$ such that $\forall x\in\{0,1\}^{n}:f(x)\cdot q(x)=1$, then run the ZeroTest PIOP for $f\cdot q-1$ and the SumCheck PIOP for $p\cdot f$. It's obviously distribution-friendly.

### Distributed MultisetCheck PIOP

> **Definition (Multiset Check Relation).** For any $k \ge 1$, the relation $\mathcal{R}_{MSet}^k$ is the set of all tuples
> 
> $$(\mathbb{x}; \mathbb{w}) = (([[f_0]], \dots, [[f_{k-1}]]), ([[g_0]], \dots, [[g_{k-1}]]); (f_0, \dots, f_{k-1}, g_0, \dots, g_{k-1}))$$
>
> where $f_j, g_j \in \mathbb{F}_n^{(\le d)}, \forall j \in [k]$ and the following two multisets of tuples are equal:
>
> $$\left\{\left(f_0(x), \dots, f_{k-1}(x)\right)\right\}_{x \in \{0, 1\}^n} = \left\{\left(g_0(x), \dots, g_{k-1}(x)\right)\right\}_{x \in \{0, 1\}^n}$$

The multiset equality above is equivalent to:

$$\prod_{x\in\{0,1\}^{n}}\left(1+\sum_{i=0}^{k-1}X^{i+1}f_i(x)\right)=\prod_{x\in\{0,1\}^{n}}\left(1+\sum_{i=0}^{k-1}X^{i+1}g_i(x)\right)$$

By Schwatz-Zippel Lemma, the verifier picks $\gamma$ at random and check:

$$\prod_{x\in\{0,1\}^{n}}\left(1+\sum_{i=0}^{k-1}\gamma^{i+1}f_i(x)\right)=\prod_{x\in\{0,1\}^{n}}\left(1+\sum_{i=0}^{k-1}\gamma^{i+1}g_i(x)\right)$$

Let $f'(X)=1+\sum_{i=0}^{k-1}\gamma^{i+1}f_i(X)$ and $g'(X)=1+\sum_{i=0}^{k-1}\gamma^{i+1}g_i(X)$.

> **Theorem (\[15\]).** Let $(a_i)_{i=1}^n$ and $(b_i)_{i=1}^n$ be sequences of a field $\mathbb{F}$ where $|\mathbb{F}| > n$. Then $\prod_{i=1}^n (a_i + X) = \prod_{i=1}^n (b_i + X)$ in the polynomial ring $\mathbb{F}[X]$ iff $\sum_{i=1}^n \frac{1}{a_i + X} = \sum_{i=1}^n \frac{1}{b_i + X}$ in the fractional field $\mathbb{F}(X)$.

So now the verifier only need to check:

$$\sum_{x\in\{0,1\}^{n}}\frac{1}{f'(x)}=\sum_{x\in\{0,1\}^{n}}\frac{1}{g'(x)}$$

or

$$\sum_{x\in\{0,1\}^{n}}\frac{f'(x)-g'(x)}{f'(x)g'(x)}=0$$

Then we can run the rational sumcheck PIOP.

The distributed PermutationCheck PIOP can be derived from MultisetCheck PIOP directly.

## Distributed Multivariate PCS