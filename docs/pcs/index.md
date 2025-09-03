# Polynomial Commitment Scheme

## What Are Polynomial Commitment Schemes (PCS)?

Imagine you're a magician who wants to prove you know a secret polynomial—a long, complicated mathematical equation—without revealing what it is. You want to convince someone that your polynomial, when evaluated at a specific number, equals a certain value. But there's a catch: you can only send them a tiny, fixed-size proof.

This is the essence of a **Polynomial Commitment Scheme (PCS)**. It's a cryptographic primitive that lets a prover "commit" to a polynomial in a way that is both **succinct** and **secure**.

In simpler terms, a PCS is a powerful tool with three main steps:

1.  **Commit:** You take a polynomial, $P(x)$, and generate a short, cryptographic representation of it. This is the **commitment**. Think of it as a fingerprint for the polynomial. You can share this fingerprint with anyone without revealing the polynomial's coefficients.
2.  **Open:** Later, someone gives you a challenge: "What's the value of your polynomial at point $z$?" You respond with the value, $v = P(z)$, and a very small **proof** $\pi$.
3.  **Verify:** The verifier uses your original commitment, the point $z$, the claimed value $v$, and your proof $\pi$ to check if everything is correct. The amazing part? They can do this in a fraction of a second, without ever seeing the polynomial itself.

### What Makes a Good PCS?

Not all schemes are created equal. A high-quality PCS should have these properties:

* **Succinctness:** The commitment and the proof must be extremely small, regardless of how large the polynomial is. This is what makes it practical.
* **Binding:** Once you commit to a polynomial, you can't change your mind. It's impossible to "open" the commitment to a different polynomial.
* **Hiding:** The commitment should not reveal any information about the polynomial's coefficients.

### Univariant and Multilinear

Polynomials come in different forms, and PCS are often classified by the type they are designed for. For most use cases, we need either a **univariant PCS** or a **multilinear PCS**.

* **Univariant Polynomials:** These are the polynomials involving only a single variable, like $P(x) = 5x^3 + 2x - 1$. Many popular schemes, including **KZG**, are designed for these single-variable polynomials.

* **Multilinear Polynomials:** A multilinear polynomial is a special type of polynomial with multiple variables, where each variable appears with a power of at most 1. They are often used to represent logical circuits or computations. Schemes like the **FRI** are designed to handle these types of polynomials efficiently.

---
## Popular PCS Examples

There are a few widely-used PCS, each with its own trade-offs. The main difference often comes down to their efficiency and whether they need a **trusted setup**. A trusted setup is a one-time cryptographic event that generates public parameters. If this event is compromised, the entire system's security can be at risk.

### **KZG (Kate-Zaverucha-Goldberg) Commitments**

* **Pros:** They are incredibly efficient. Both the proof size and the verification time are constant, meaning they stay the same no matter how large the polynomial is.
* **Cons:** They require a trusted setup. This has led to high-profile ceremonies like the one for the Zcash network, where many people participate to ensure no single party holds the secret.

### **IPA (Inner Product Argument) Commitments**

* **Pros:** They do **not** require a trusted setup, which is a massive security benefit. They rely on more common cryptographic assumptions.
* **Cons:** They are less efficient than KZG. The proof size and verification time grow logarithmically with the size of the polynomial, which is still very fast but not constant.


---

## Comparing

| Scheme Name | Commitment Size | Proof Size | Verification Time | Prover Time | Trusted Setup | Security Assumption | Core Technique | Advantages | Disadvantages |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **KZG** [^1] | $O(1)$ | $O(1)$ | $O(1)$ | $O(n)$ | **Required** | CDH on Pairing-friendly curves | Bilinear Pairings | Small proofs, fast verification | Relies on a trusted setup |
| **FRI** [^3] | | $O(\log n)$ | $O(\log n)$ | $O(n \log n)$ | **Not required** | Collision resistance of hash functions | Iterative low-degree testing | Fully transparent, post-quantum secure | Relatively large proofs, high verification cost |
| **IPA** [^4] | | $O(\log n)$ | $O(\log n)$ | $O(n \log n)$ | **Not required** | Discrete Logarithm (DL) problem | Inner Product Argument, Fiat-Shamir | No trusted setup, logarithmic proof size | Can be slower than KZG for large n |
| **Bulletproofs** [^2] | $O(1)$ | $O(\log n)$ | $O(n)$ | $O(n)$ | **Not required** | Discrete Logarithm (DL) problem | Inner Product Argument | No trusted setup, small proof size | Slower verification than KZG |
| **Dory** | | $O(\log n)$ | $O(\log n)$ | $O(n)$ | **Not required** | | | | |
| **Hyrax** [^5] | | $O(\log n)$ | $O(\log^2 n)$ | $O(n \log n)$ | **Not required** | DL and Strong RSA assumptions | Vector commitments, inner product arguments | No trusted setup, fast verification | Relies on specific assumptions |
| **Brakedown** [^6] [^9] | | $O(1)$ | $O(n)$ | $O(n \log n)$ | **Not required** | DL and higher-order DL assumptions | Batching techniques | Constant-size proofs without trusted setup | Linear verification time |
| **Gemini** [^7] | | $O(1)$ | $O(\log n)$ | $O(n \log n)$ | **Not required** | DL and higher-order DL assumptions | Polynomial combination for batching | Combines KZG and FRI advantages | Relatively complex protocol |
| **Virgo** [^8] | | $O(\log^2 n)$ | $O(\log^2 n)$ | $O(n \log n)$ | **Not required** | Polylogarithmic inner product argument | Vector polynomial delegation | No trusted setup, post-quantum secure | Proof and verification time are not constant |

### Key Metrics Explained

* **Proof Size**: A smaller proof size reduces network transmission overhead, which is crucial in scenarios like blockchains. **KZG** and **Dark/Brakdown** achieve the ideal constant-size proofs, while **FRI** and **Virgo** achieve logarithmic or polylogarithmic growth, which is significantly better than linear.
* **Verification Time**: This is a critical metric for on-chain verification. **KZG** has the fastest, constant-time verification. **Bulletproofs**, **FRI**, and **Gemini** also have very fast logarithmic verification times, while **Dark** and **Virgo** are slightly slower.
* **Prover Time**: The prover time for all major schemes is quasi-linear, at $O(n \log n)$, so there is little significant difference in this metric.
* **Trusted Setup**: Whether a scheme requires a trusted setup is a key differentiator. **KZG** requires a one-time, universal trusted setup, and if this process is compromised, the entire system's security can be at risk. Schemes like **FRI**, **Bulletproofs**, **Hyrax**, and **Virgo** are **transparent** and do not require a trusted setup, giving them a natural security advantage.
* **Security Assumptions**: Different schemes rely on different mathematical problems. **KZG** depends on the difficulty of problems on bilinear pairings. **FRI** and **Virgo** primarily rely on the collision resistance of hash functions, making them generally considered post-quantum secure.

---
## References

[^1]: Kate, A., Zaverucha, G., & Goldberg, I. (2010). *Constant-size commitments to polynomials and their applications*.
[^2]: Bünz, M., Bootle, J., Boneh, D., Poelstra, A., Wuille, P., & Maxwell, G. (2018). *Bulletproofs: Short proofs for confidential transactions and more*.
[^3]: Ben-Sasson, E., Bentov, I., Horesh, Y., & Riabzev, M. (2018). *Scalable, transparent, and post-quantum secure computational integrity*.
[^4]: Groth, J. (2016). *On the size of pairing-based non-interactive arguments*.
[^5]: Wahby, R. S., Tzialla, I., Shelat, A., Thaler, J., & Walfish, M. (2017). *Doubly-efficient zkSNARKs without trusted setup*.
[^6]: Golovnev, A., Lee, J., Setty, S., Thaler, J. & Wahby, R. S. (2021). *Brakedown: Linear-time and field-agnostic SNARKs for R1CS*.
[^7]: Bootle, J., Chiesa, A., Hu, Y., & Orrù, M. (2022). *Gemini: Elastic SNARKs for Diverse Environments*.
[^8]: Zhang, J., Xie, T., Zhang, Y., & Song, D. (2020). *Virgo: Transparent polynomial delegation and its applications to zero-knowledge proof*.
[^9]: Boneh, D., Drake, J., Fisch, B., & Gabizon, A. (2021). *Halo Infinite: Recursive zk-SNARKs from any Additive Polynomial Commitment Scheme*.
