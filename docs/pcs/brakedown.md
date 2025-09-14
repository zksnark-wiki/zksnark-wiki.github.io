# Brakedown/Ligero

## Commitment Phase

We want to commit to a polynomial $q(X)=\sum_{i=0}^{n-1}u_iX^i$. Rearrange the coefficients $u$ to get an $m \times m$ matrix $U$.

The prover sends a commitment to matrix $E\in\mathbb F^{m\times\rho^{-1}m}$, claimed to equal $\mathrm{Enc}(U)$. Here to commit to a matrix, we commit every colume with Pedersen commitment and commit to these colume commitments with Merkle Tree commitment.

The verifier has to check that $E$ is well-formed, i.e., every row is a valid codeword.

1. The verifier chooses a random vector $r \leftarrow \mathbb{F}_p^m$
2. The prover responds with $w := r^TU$
3. For a random column subset $\mathcal I$ of constant size $t = \Theta(\lambda)$, the verifier checks entries of $\mathrm{Enc}(w)$ at positions in $\mathcal I$ by opening the entire column $c_i$ from the commitment of $E$ and computing $\langle c_i,r\rangle$.

## Evaluation Phase

$q(z)=b^T \cdot u \cdot a$ for $a := (1, z, z^2, \dots, z^{m-1})$ and $b := (1, z^m, z^{2m}, \dots, z^{m(m-1)})$.

We can just replace the random vector $r$ in the commitment phase with $b$.

## Performance

| | Commitment Size | Proof Size | Verifier Time | Prover Time |
| :---: | :---: | :---: | :---: | :---: |
| Ligero | $1$ | $O(\sqrt n)$ | $O(\sqrt n)$ | $O(n\log n)$ |
| Brakedown | $1$ | $O(\sqrt n)$ | $O(\sqrt n)$ | $O(n)$ |