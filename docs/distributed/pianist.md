# Pianist

The Pianist protocol is a distributed zkSNARK system designed to enable multiple machines to collaboratively generate a single, succinct proof. The protocol's core innovation lies in the elegant combination of a **bivariate Plonk** constraint system and a **distributively computable KZG** polynomial commitment scheme.

## Distributed PIOP for Data-Paralell Circuits

## Distributed PIOP for General Circuits

## Distributed KZG

In our setting, we need to commit to some polynomial $f(Y,X):\mathbb F^M\times\mathbb F^T\to\mathbb F$, and machine $i$ holds the evaluations of $f(\langle i\rangle,\cdot)$. We let $f_i(X)$ denote $(\langle i\rangle,X)$.

### **DKZG.KeyGen($1^\lambda, M, T$)**

This is the key generation phase. It generates the public parameters $\mathsf{pp}$ using a security parameter $\lambda$, the number of machines $M$, and a degree bound $T$. The parameters are:

$$
\mathsf{pp} = (g, g^{\tau_X}, g^{\tau_Y}, (U_{i,j})_{0 \le i < M, \ 0 \le j < T}= (g^{R_i(\tau_Y) L_j(\tau_X)})_{0 \le i < M, \ 0 \le j < T})
$$

with trapdoor values $\tau_Y$ and $\tau_X$, which should be discarded.

### **DKZG.Commit($f, \mathsf{pp}$)**

In the commitment phase, each machine $P_i$ computes a commitment, $com_{f_i}$, for its local polynomial $f_i(X)$. This commitment is the product of elements from the public parameters:

$$\mathrm{com}_{f_i} = \prod_{j=0}^{T-1} U_{i,j}^{f_{i,j}}$$

where $f_{i,j}=f_i(\langle j\rangle)$. Each $P_i$ sends its commitment to $P_0$, the master node. The master node then computes the final commitment $\mathrm{com}_f$ by multiplying all the individual commitments:

$$\mathrm{com}_f = \prod_{i=0}^{M-1} \mathrm{com}_{f_i}$$

### **DKZG.Open($f, \beta, \alpha, \mathsf{pp}$)**

This is the opening phase, where the polynomial $f$ is evaluated at a specific point $(\alpha, \beta)$.

1.  Each machine $P_i$ computes its local evaluation $f_i(\alpha)$ and a proof $\pi_0^{(i)}$. $P_i$ first calculates the quotient polynomial:

    $$q_0^{(i)}(X) = \frac{f_i(X) - f_i(\alpha)}{X-\alpha}$$

    $P_i$ then computes the proof element $\pi_0^{(i)} = g^{R_i(\tau_Y) q_0^{(i)}(\tau_X)}$ using the public parameters and sends both $f_i(\alpha)$ and $\pi_0^{(i)}$ to the master node $P_0$.

2.  After receiving all the evaluations and proof elements, $P_0$ computes the final proof element $\pi_0 = \prod_{i=0}^{M-1} \pi_0^{(i)}$. $P_0$ also recovers the polynomial $f(Y,\alpha)$ by summing the local polynomials:

    $$f(Y,\alpha) = \sum_{i=0}^{M-1} R_i(Y)f_i(\alpha)$$

    $P_0$ then computes the final evaluation of the full polynomial, $f(\beta, \alpha)$, and a quotient polynomial $q_1(Y)$ as:

    $$q_1(Y) = \frac{f(Y, \alpha) - f(\beta, \alpha)}{Y-\beta}$$

    $P_0$ computes the final proof element $\pi_1 = g^{q_1(\tau_Y)}$ and sends the evaluation $z = f(\beta, \alpha)$ and the full proof $\pi_f = (\pi_0, \pi_1)$ to a verifier $V$.

### **DKZG.Verify($\mathrm{com}_f, \beta, \alpha, z, \pi_f, \mathsf{pp}$)**

The process is the same as original KZG. It parses the proof $\pi_f = (\pi_0, \pi_1)$ and performs a pairing check:

$$e(com_f/g^z, g^\beta) \stackrel{?}{=} e(\pi_0, g^{\tau_X - \alpha})e(\pi_1, g^{\tau_Y - \beta})$$

If the check passes, the output is $\mathsf{accept}$; otherwise, it is $\mathsf{reject}$.