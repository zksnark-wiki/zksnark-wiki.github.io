# KZG Commitment

## Preliminaries

- DLP (Discrete Logarithm Problem), DDH (Decision Diffie-Hellman assumption), CDH (Computational Diffie-Hellman assumption).
- Pairing and pairing-friendly groups.
- Schwarz-Zippel Lemma.

## KZG Commitment for Univariant Polynomials

The process of a KZG commitment can be broken down into four distinct phases:

### 1. Trusted Setup

This is a one-time ceremony that generates the public parameters (the SRS) which are then used by everyone (both the prover and the verifier). 

The SRS consists of elements on a pairing-friendly group $\mathbb G$, that is, $(g,g^{\tau},g^{\tau^2},...,g^{\tau^d})$ where $d$ is the maximum degree of any polynomial to be committed to, and $g$ is a generator of the group. The value $\tau$ is chosen from $\{0,1,...,|\mathbb G|-1\}$ at random, and as a "toxic waste", it should be discarded once the SRS has been generated. So $\tau$ can't be known by anyone.

### 2. Commitment

The prover, who has a polynomial $q(x)$ of degree at most $d$, wants to commit to it. He computes the commitment $C$ by "evaluating" $g^{q(\tau)}$. Since he doesn't know $\tau$, he uses the public SRS:

$$C = g^{q(\tau)} = g^{\sum_{i=0}^d c_i \tau^i} = \prod_{i=0}^d (g^{\tau^i})^{c_i}$$

The result, $C$, is an single element of $\mathbb G$—a constant-sized commitment regardless of the polynomial's degree. The prover sends this commitment to the verifier. Because of the cryptographic properties, the commitment is **binding**: once the prover has generated $C$, they cannot "open" it to a different polynomial.

### 3. Proving an Evaluation

Now, the prover wants to convince the verifier that the polynomial $q(X)$ evaluates to a specific value $v$ at a point $z$. That is, they want to prove $q(z) = v$.

The prover constructs a new polynomial, the "quotient polynomial" $w(X)$:

$$w(X) = \frac{q(X) - v}{X - z}$$

The key insight here is from the Remainder Theorem in algebra: $q(z) - v = 0$ if and only if $(X-z)$ is a factor of $q(X) - v$. If the evaluation is correct ($q(z) = v$), then $w(z)$ will be a valid polynomial. The prover then computes a commitment to this quotient polynomial, which will serve as the proof $\pi$:

$$\pi = g^{w(\tau)}$$

The prover sends the claimed evaluation $(z, v)$ and the proof $\pi$ to the verifier.

### 4. Verification

The verifier receives the commitment $C$, the claimed evaluation $(z, v)$, and the proof $\pi$. He wants to check if the equation $q(X)-v=(X-z)w(X)$ holds. Due to the Schwartz-Zippel Lemma, he only needs to check whether it holds at a random point, that is, $\tau$. As $\tau$ is unknown to the prover, it's safe to take it as a random number.

So now the verifier wants to check $q(\tau)-v=(\tau-z)w(\tau)$. Using the bilinear pairing, they can check this equality in the exponent without ever knowing $\tau$.

The verifier checks if the following pairing equation holds:

$$e(C / g^{v}, g) = e(g^{\tau - z}, \pi)$$

The equation holds if and only if $q(\tau) - v = (\tau - z)w(\tau)$, which, because of the Schwartz-Zippel Lemma, proves with a high probability that $q(X) - v = (X-z)w(X)$ and therefore that $q(z) = v$.

### Analysis

The commitment size and the proof size are $O(1)$ as the they contain only one group element respectively. It takes the prover $O(d)$ time to compute the commitment $C$ and the proof $\pi=g^{\frac{q(X)-v}{X-z}}$. The verifier time is $O(1)$.

## For Multilinear Polynomials

It's based on the following fact.

**Fact** (Papamanthou, Shi, and Tamassia \[[PST13](#fn:PST13)\]). For any fixed $z = (z_1, \dots, z_\ell) \in \mathbb{F}_p^\ell$ and any multilinear polynomial $q$, $q(z) = v$ if and only if there is a unique set of $\ell$ multilinear polynomials $w_1, \dots, w_\ell$ such that
$$
q(X) - v = \sum_{i=1}^\ell (X_i - z_i)w_i(X).
$$

### 1. Proving an Evaluation

Suppose a prover wants to prove that a multilinear polynomial $q(X)$ evaluates to a specific value $v$ at a point $z = (z_1, \dots, z_\ell)$.

The prover's task is to provide proof for the relationship in the above fact. The most straightforward approach is for the prover to compute a commitment for each of the $\ell$ quotient polynomials. The proof, $\pi$, would be a tuple of these commitments:

$$\pi = (g^{w_1(\tau)}, g^{w_2(\tau)}, \dots, g^{w_\ell(\tau)})$$

The prover sends the claimed evaluation $(z, v)$ along with the proof $\pi$ to the verifier.

### 2. Verification

The verifier receives the commitment $C = g^{q(\tau)}$, the claimed evaluation $(z, v)$, and the proof $\pi$. The verifier's goal is to check if the polynomial identity holds in the exponent. This can be verified with $\ell$ separate pairing checks:

$$e(C/g^v, g) = \prod_{i=1}^\ell e(g^{w_i(\tau)}, g^{\tau_i-z_i})$$

This equation holds if and only if the original polynomial identity is correct, which proves with high probability that $q(z) = v$. While this is a correct verification method, it is important to note that the verifier's time scales with the number of variables, $\ell$, which can be a limiting factor.

### Analysis

In this extended scheme for multilinear polynomials, the commitment size remains constant at **$O(1)$** (one group element). However, the proof size is **$O(\ell)$** because the proof consists of commitments to each of the $\ell$ quotient polynomials. Consequently, the verifier's time is also **$O(\ell)$**, as it must perform a pairing check for each polynomial. As for the prover time, \[[ZGK+18](#fn:ZGK+18)\] shows that the prover can compute $w_1,...,w_{\ell}$ and evaluate $g^{w_1(r)},...,g^{w_{\ell}(r)}$ with $O(2^{\ell})$ time.

## References

[^PST13]: \[PST13\] Papamanthou, C., Shi, E., & Tamassia, R. (2013). [*Signatures of Correct Computation*](https://eprint.iacr.org/2011/587.pdf).
[^Tha23]: \[Tha23\] Thaler, J. (2023). [*Proofs, Arguments, and Zero-Knowledge*](https://people.cs.georgetown.edu/jthaler/ProofsArgsAndZK.pdf).
[^ZGK+18]: \[ZGK+18\] Zhang, Y., Genkin, D., Katz, J., Papadopoulos, D., & Papamanthou, C. (2018). [*vRAM: Faster Verifiable RAM With Program-Independent Preprocessing*](https://faculty.cc.gatech.edu/~genkin/papers/vram.pdf).