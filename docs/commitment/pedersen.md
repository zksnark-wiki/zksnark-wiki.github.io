# Pedersen Commitment

Pedersen Commitment can be derived from Schnorr's $\Sigma$-protocol for the Discrete Logarithm Relation.

At the start of the protocol, the prover generates two generators of the group $\mathbb G$, we denote them with $g, h$. To commit to $m\in\mathbb Z_q$, the prover chooses $z\in\mathbb Z_q$ randomly and sends $C_m=g^m\cdot h^z$ to the verifier. To open it, the prover just sends $(m,z)$ to the verifier, and verifier checks that $C_m\stackrel{?}{=}g^m\cdot h^z$.

The correctness, computational binding and perfect hiding is directly guarenteed by the properties of $\Sigma$-protocol and Damgård's construction.

Moreover, Pedersen Commitment is additively homomorphic.

## Establishing Product Relationship

## Pedersen Vector Commitment

$C_m=g_0^{m_0}g_1^{m_1}\cdots g_{n-1}^{m_{n-1}}h^{z}$