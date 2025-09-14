# Bulletproofs

## Argument of Knowledge

1. Let $G$ be an **additive** cyclic group of prime order $p$ over which the Discrete Logarithm relation is hard, with vector of generators $\mathbf{g} = (g_1, \dots, g_n)$.
2. Input is $c_u = \text{Com}(u) = \sum_{i=1}^n u_i g_i$. Prover knows $u$, Verifier only knows $c_u, g_1, \dots, g_n$.
3. If $n = 1$, Prover sends $u$ to the verifier and the verifier checks that $u g_1 = c_u$.
4. Otherwise, write $u = u_L \circ u_R$ and $\mathbf{g} = \mathbf{g}_L \circ \mathbf{g}_R$. Prover sends $v_L, v_R$ claimed to equal $\langle u_L,\mathbf g_R \rangle$ and $\langle u_R, \mathbf g_L \rangle$.
5. Verifier responds with a randomly chosen $\alpha \in \mathbb{F}_p$.
6. Recurse on commitment $c_{u'} = c_u + \alpha^2 v_L + \alpha^{-2} v_R$ to vector $u' = \alpha u_L + \alpha^{-1} u_R$ of length $n/2$, using the vector of group generators $\mathbf{g'} = \alpha^{-1} \mathbf{g}_L + \alpha \mathbf{g}_R$.

## Evaluation Proof

1. Let $\mathbb{G}$ be an **additive** cyclic group of prime order $p$ over which the Discrete Logarithm relation is hard, with vector of generators $\mathbf{g} = (g_1, \dots, g_n)$. Let $\mathbf{y} \in \mathbb{F}_p^n$ be a public vector and public value $v \in \mathbb{F}_p$.
2. Input is $c_u = \text{Com}(u) := \sum_{i=1}^n u_i g_i$. Prover knows $u$, Verifier only knows $c_u, \mathbf{g}, \mathbf{y}$, and $v$.
3. If $n=1$, the prover sends $u$ to the verifier and the verifier checks that $ug_1 = c_u$ and that $uy_1 = v$.
4. Otherwise, write $u = u_L \circ u_R$, $\mathbf{g} = \mathbf{g}_L \circ \mathbf{g}_R$, and $\mathbf{y} = \mathbf{y}_L \circ \mathbf{y}_R$. Prover sends $v_L, v_R$ claimed to equal $\langle u_L, \mathbf{g}_R \rangle$ and $\langle u_R, \mathbf{g}_L \rangle$, as well as $v_L', v_R'$ claimed to equal $\langle u_L, \mathbf{y}_R \rangle$ and $\langle u_R, \mathbf{y}_L \rangle$.
5. Verifier responds with a randomly chosen $\alpha \in \mathbb{F}_p$.
6. Recurse on commitment $c_{u'} := c_u + \alpha^2 v_L + \alpha^{-2} v_R$ to vector $u' = \alpha u_L + \alpha^{-1} u_R$ of length $n/2$, using the vector of group generators $\mathbf{g}' := \alpha^{-1} \mathbf{g}_L + \alpha \mathbf{g}_R$, and using public vector $\mathbf{y}' := \alpha^{-1} \mathbf{y}_L + \alpha \mathbf{y}_R$ and public value $v' := v + \alpha^2 v_L' + \alpha^{-2} v_R'$.

## Performance

| | Commitment Size | Proof Size | Verifier Time | Prover Time |
| :---: | :---: | :---: | :---: | :---: |
| Bulletproofs | $1$ | $O(\log n)$ | $O(n)$ | $O(n)$ |