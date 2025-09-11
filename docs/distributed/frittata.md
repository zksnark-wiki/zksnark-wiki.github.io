# FRIttata

## Background

**Pianist** compiles a distributed IOP into a SNARK with a distributed PCS based on **KZG commitment**. It depends on a trusted setup and CDH on a pairing-friendly group, thus not transparent and not post-quantum (PQ) secure. \[[XGBK25](#fn:XGBK25)\]<span id="fnref:XGBK25"></span> outlines a new distributed PCS based on **FRI (Fast Reed-Solomon Interactive Oracle Proofs of Proximity)** to achieve **transparency** and **plausibly PQ security**.

## Distributed FRI

DFRI (Distributed FRI) can be used to delegate the FRI process of $M$ functions across $M$ machines.

- Each machine holds a function $f_i:D\to\mathbb F$. They commit to $f_i$ (with Merkle Tree) and send $C_i$ to the master node.
- The master node collects them and sends $(C_i)^{M-1}_{i=0}$ to the verifier. 
- When the verifier wants to open $(f_i)_{i=0}^{M-1}$ at $\alpha$, the master node sends $(z_i)^{M-1}_{i=0}$ which is claimed to be $(f_i(\alpha))_{i=0}^{M-1}$, together with a proof $\pi$. The verifier can run $\mathrm{Verify}_{\mathbf{pp}}((C_i)_{i=0}^{M-1},(z_i)_{i=0}^{M-1},\alpha,\pi)$ to check the evaluations.

### Instantiations of Distributed FRI

A naive instantiation of DFRI is **Parallel FRI**.

\[Che24\] proposed another instantiation, **Distributed Batched FRI**.

**Fold-and-Batch** can be seen as an "interpolation" between Parallel FRI and Distributed Batched FRI.

## Bivariate FRI-based PCS

This section details how to commit to a bivariate polynomial, which is directly applicable to the Pianist framework. 

We commit to a bivariate polynomial $F(X,Y)$ that has a specific structure:

$$F(X,Y)=\sum_{i=0}^{M-1}F_i(X)R_i(Y)$$

where $F_i$ is a univariate polynomial and $R_i$ is the **Lagrange function** over $\mu_M=\{1,\omega,...,\omega^{M-1}\}$.

Using DFRI, we can commit to each of the univariate polynomials $F_0,...,F_{M-1}$ to get the commitments $C_0,...,C_{M-1}$.

When the verifier wants to open $F(\alpha,\beta)=z$: 

- The prover computes $F(\alpha,\beta)=z$, $F_i(\alpha)=z_i$ and $Q_i(X)=\frac{F_i(X)-z_i}{X-\alpha}$. Then he generates a proof $\pi$ for $Q_0,...,Q_{M-1}$ using DFRI and sends $(z,(z_i)_{i=0}^{M-1},\pi)$ to the verifier. 
- The verifier checks the correctness of $(z_i)_{i=0}^{M-1}$ with $\pi$, and use them to check the correctness of $z$. A naive method is to directly check $z\stackrel{?}{=}F(\alpha,\beta)$ with the fact $F(\alpha,\beta)=\sum_{i=0}^{M-1}z_iR_i(\beta)$. But it would take $O(M^2)$ time.

A more efficient, $O(M)$ method is possible. It relies on the polynomial remainder theorem. The claim $F(\alpha,\beta)=z$ is equivalent to the statement that a polynomial $H$ of degree at most $M-2$ exists such that 

$$F(\alpha,Y)-z=H(Y)(Y-\beta)$$

Since $H$ has degree $M-2$, the verifier can construct $H(Y)$ by evaluating it at $M-1$ distinct points. A convenient choice is the roots of unity $\mu_{M-1} = \{1, \omega, \dots, \omega^{M-2}\}$. At these points, the evaluations of $H(Y)$ are given by:

$$h_i = H(\omega^i) = \frac{F(\alpha, \omega^i) - z}{\omega^i - \beta} = \frac{z_i - z}{\omega^i - \beta}$$

Using these evaluations, the verifier can express $H(Y)$ in the Lagrange basis over $\mu_{M-1}$:

$$H(Y) = \sum_{i=0}^{M-2} S_i(Y)h_i$$

What's left is to check that the function $H$ does satisfy $F(\alpha,Y)-z=H(Y)(Y-\beta)$. As they have degree $M-1$, we only need to check $F(\alpha,Y)-z$ and $H(Y)(Y-\beta)$ agree on arbitrary $M-1$ points, which we choose $\mu_M$. $H(Y)$ was constructed to agree with $\frac{F(\alpha,Y)-z}{Y-\beta}$ on $\mu_{M-1}=\mu_{M}\backslash\{\omega^{M-1}\}$, so we only need to check 

$$F(\alpha,\omega^{M-1})-z=H(\omega^{M-1})(\omega^{M-1}-\beta)$$ 

Substituting the known values, we get 

$$z_{M-1}-z=\sum_{i=0}^{M-2}S_i(\omega^{M-1})h_i$$

Note that $S_i(\omega^{M-1})$ and $h_i$ can be precomputed, so the Since the verifier can perform this final check in $O(M)$ time, making the overall process efficient.


## References

[^XGBK25]: \[XGBK25\] Xu, H., Gama, M., Beni, E., & Kang, J. (2025). *FRIttata: Distributed Proof Generation of FRI-based SNARKs*.