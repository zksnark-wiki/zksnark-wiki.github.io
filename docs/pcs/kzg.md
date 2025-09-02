# KZG Commitment

## Preliminaries

1.  **A "Toxic Waste" Trusted Setup**: This is the most controversial part of KZG. A one-time setup ceremony is required to generate a set of public parameters, known as the Structured Reference String (SRS). A trusted party (or, more commonly, a multiparty computation or MPC) generates a random secret number, say $\tau$, and then computes a set of points on an elliptic curve corresponding to the powers of $\tau$, without ever revealing $\tau$ itself. These points are made public, and the secret $\tau$ is "ceremonially destroyed" (hence, "toxic waste"). As long as at least one participant in the MPC ceremony was honest and deleted their share of the secret, the security of the entire system holds.

2.  **Elliptic Curve Pairings**: This is the mathematical engine of KZG. A bilinear pairing is a special function, let's call it $e$, that takes two points on an elliptic curve and maps them to a third group. The key property is that it allows for a form of multiplication across different groups. Formally, for a pairing-friendly elliptic curve, the pairing $e(P, Q)$ has the property that $e(aP, bQ) = e(P, Q)^{ab}$, where $P, Q$ are points on the curve and $a, b$ are scalars. This is the cryptographic primitive that allows for verification without revealing the secret $\tau$.

## KZG Commitment for Univariant Polynomial

The process of a KZG commitment can be broken down into four distinct phases:

### 1. Trusted Setup

As mentioned, this is a one-time ceremony that generates the public parameters (the SRS) which are then used by everyone. The SRS consists of points on a pairing-friendly elliptic curve, such as $[1]_\mathbb{G_1}, [\tau]_\mathbb{G_1}, [\tau^2]_\mathbb{G_1}, \dots, [\tau^d]_\mathbb{G_1}$ where $d$ is the maximum degree of any polynomial to be committed to, and $[x]_\mathbb{G_1}$ denotes the point $x \cdot G_1$, with $G_1$ being the generator of the group.

### 2. Commitment

The prover, who has a polynomial $P(x)$ of degree at most $d$, wants to commit to it. They compute the commitment $C$ by "evaluating" the polynomial at the secret point $\tau$ in the exponent. Since they don't know $\tau$, they use the public SRS:

$C = [P(\tau)]_{\mathbb{G_1}} = [\sum_{i=0}^d p_i \tau^i]_{\mathbb{G_1}} = \sum_{i=0}^d p_i [\tau^i]_{\mathbb{G_1}}$

The result, $C$, is a single point on the elliptic curve—a constant-sized commitment regardless of the polynomial's degree. The prover sends this commitment to the verifier. Because of the cryptographic properties, the commitment is **binding**: once the prover has generated $C$, they cannot "open" it to a different polynomial.

### 3. Proving an Evaluation

Now, the prover wants to convince the verifier that the polynomial $P(x)$ evaluates to a specific value $y$ at a point $a$. That is, they want to prove $P(a) = y$.

The prover constructs a new polynomial, the "quotient polynomial" $Q(x)$:

$Q(x) = \frac{P(x) - y}{x - a}$

The key insight here is from the Remainder Theorem in algebra: $P(a) - y = 0$ if and only if $(x-a)$ is a factor of $P(x) - y$. If the evaluation is correct ($P(a) = y$), then $Q(x)$ will be a valid polynomial. The prover then computes a commitment to this quotient polynomial, which will serve as the proof $\pi$:

$\pi = [Q(\tau)]_{\mathbb{G_1}}$

The prover sends the claimed evaluation $(a, y)$ and the proof $\pi$ to the verifier.

### 4. Verification

The verifier receives the commitment $C$, the claimed evaluation $(a, y)$, and the proof $\pi$. They want to check if the equation $P(x) - y = (x-a)Q(x)$ holds at the secret point $\tau$. Using the bilinear pairing, they can check this equality in the exponent without ever knowing $\tau$.

The verifier checks if the following pairing equation holds:

$e(\pi, [\tau - a]_{\mathbb{G_2}}) = e(C - [y]_{\mathbb{G_1}}, G_2)$

Let's unpack this with the pairing property:
-   Left side: $e(\pi, [\tau - a]_{\mathbb{G_2}}) = e([Q(\tau)]_{\mathbb{G_1}}, [\tau - a]_{\mathbb{G_2}}) = e(G_1, G_2)^{Q(\tau)(\tau-a)}$
-   Right side: $e(C - [y]_{\mathbb{G_1}}, G_2) = e([P(\tau)]_{\mathbb{G_1}} - [y]_{\mathbb{G_1}}, G_2) = e([P(\tau)-y]_{\mathbb{G_1}}, G_2) = e(G_1, G_2)^{P(\tau)-y}$

The equation holds if and only if $P(\tau) - y = Q(\tau)(\tau - a)$, which, because of the Schwartz-Zippel Lemma, proves with a high probability that $P(x) - y = (x-a)Q(x)$ and therefore that $P(a) = y$.

The proof is verified, and the verifier is convinced the prover's claim is correct, all with constant-sized data and a small amount of computation.
