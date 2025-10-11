# LogUp

## Recommended Reference

[ZK Whiteboard Sessions - S2M3: An Update on Lookups with Ariel Gabizon](https://www.youtube.com/watch?v=KQi3WdZvI6w)

## Background

$\mathcal V$ have two committed vectors $f,t\in \mathbb F^n$ and want to check values of $f$ are contained in $t$ (denoted as $f\subset t$), or 

$$
\forall i\in [n],\exists j\in[n],f_i=t_j
$$

Here a vector, say $f$, can also be seen as a function over a multiplacative group $H\subset \mathbb F$ of size $n$ such that $f(g^i)=f_i$, where $g$ is a generator of $H$.

## Basic LogUp Scheme

**LogUp** is based on the following lemma:

**Lemma.** For vectors $f,t\in\mathbb F^n$, $f\subset t$ iff $\exists m\in\mathbb F^n$ s.t. 

$$
\sum_{i\in [n]}\frac{m_i}{X+t_i}=\sum_{i\in[n]}\frac{1}{X+f_i}
$$

<details>
<summary>Proof</summary>
to be done
</details>

---

The LogUp scheme follows the following paradigm.

**Step 1.** $\mathcal P$ sends the commitment of $m$.

**Step 2.** $\mathcal V$ sends a challenge $\beta\leftarrow\mathbb F$.

**Step 3.** $\mathcal P$ convinces $\mathcal V$ that the Indentity in the above lemma holds at $\beta$. Concretely speaking, $\mathcal P$ sends the commitment of a vector $A\in\mathbb F^n$ where

$$
A_i=\frac{m_i}{\beta+t_i}-\frac{1}{\beta+f_i}
$$

Now $\mathcal V$ needs to check:

1. Correctness of $A$.
2. $\sum_{i\in[n]}A_i=0$.

---

Now we skip the former check and focus on the latter. It can be done with the lemma below:

**Lemma (Aurora Lemma).** $H\subset\mathbb F$ is a multiplicative subgroup of order $n$, and $A\in\mathbb F_{<n}[X]$, then:

$$
\frac1n\sum_{a\in H} A(a)=A(0)
$$

<details>
<summary>Proof</summary>
$A(X)=\sum_{i\in[n]}L_i(X)A_i$ where $L_i$ is the lagrange polynomial over $H$, so $A(0)=\sum_{i\in[n]}L_i(0)A_i$.

As $L_i(X)=\frac{X^n-1}{n}\cdot\frac{g^i}{X-g^i}$, $L_i(0)=\frac1n$, so $A(0)=\frac1n\sum_{i\in[n]}A_i=\frac1n\sum_{a\in H}A(a)$.

Another proof:

$\sum_{a\in H}a^k=\sum_{a\in H}a=0$.
</details>

So $\mathcal P$ only needs to open $A$ at $0$.

## $\mathfrak{cq}$: Cached quotients for fast lookups

When the length of $t$ is much bigger than $f$, say $N=|t|\gg n=|f|$, we have to spend $O(N\log N)$ time to check $f\subset t$. 

$\mathfrak{cq}$ provides an algorithm that reduces the time complexity to quasi-linear with $n$. As $t$ is given previously, we can try to preprocess it with $O(N)$ or more time, and when $f$ comes, check $f\subset t$ in time quasilinear to $n$.

Now we define a multiplicative subgroup $V$ of size $N$, and define polynomial $m$, $A$ over $V$.

Remember that we want to check the correctness of $A_i=\frac{m_i}{\beta+t_i}-\frac{1}{\beta+f_i}$. We will deal with the two parts of $A_i$ separately. Denote $\frac{m_i}{\beta+t_i}$ as $B_i$. Note that the vectors $m$ and $B$ are **sparse**.

$B$ is computed correctly iff there exists $Q(X)$ such that

$$
B(X)(t(X)+\beta)=Z_{V}(X)Q(X)+m(X)
$$

where $Z_V$ is the vanishing polynomial over $V$.

Note that there exists a polynomial $Q_i$ such that

$$
L_i(X) (t(X)+\beta)=L_i(X) (t_i+\beta)+Z_V(X)Q_i(X)
$$

and $Q_i(X)=\frac{L_i(X)(t(X)-t_i)}{Z_V(X)}$ where $L_i$ is the lagrange polynomial over $V$. As $L_i(X)=\frac{Z_V(X)}{Z_V'(g^i)(X-g^i)}$, $Q_i(X)=Z_V'(g^i)^{-1}\cdot\frac{t(X)-t_i}{X-g^i}$. Denote $\frac{t(X)-t_i}{X-g^i}$ as $K_i(X)$, it's the KZG-open of $t$ at $g^i$. The algorithm of Feist and Khovratovich can be used to compute $K_i(\alpha)$ for all $i\in[N]$ in $O(N\log N)$ time. Thus we can compute $Q_i(\alpha)$ for all $i\in[N]$ in $O(N\log N)$ time.

So in the preprocess phase, we compute $Q_i(\alpha)$ for all $i\in[N]$ in $O(N\log N)$ time. They are called cached quotients, which is what $\mathfrak{cq}$ is named after.

When $f$ comes, we need to compute the polynomial $Q(\alpha)$. It can be computed with cached quotients.

$$
\begin{aligned}
L_i(X) (t(X)+\beta)&=\sum_{i\in[N]} B_iL_i(t(X)+\beta)
\\&=\sum_{i\in[N]}B_iL_i(X) (t_i+\beta)+Z_V(X)\sum_{i\in[N]}B_iQ_i(X)
\\&=\sum_{i\in[N]}L_i(X) m_i+Z_V(X)\sum_{i\in[N]}B_iQ_i(X)
\\&=m(X)+Z_V(X)\sum_{i\in[N]}B_iQ_i(X)
\end{aligned}
$$

So $Q(X)=\sum_{i\in[N]}B_iQ_i(X)$, i.e., 

$$
Q(\alpha)=\sum_{i\in[N]}B_iQ_i(\alpha)
$$

## LogUp-GKR

Note that what we want to do in the last step of LogUp is a **fractional sumcheck**, i.e., to check:

$$
\sum_{x\in V}\frac{f(x)}{g(x)}=0
$$

We can employ GKR protocol to check it. For convenience, we suppose $V=\{0,1\}^{\ell}$ to fit GKR protocol. But it can be extended to univariant cases.

So now our problem can be abstracted as: for two committed multilinear polynomial $f,g:\{0,1\}^{\ell}\to \mathbb F$, $\mathcal P$ proves to $\mathcal V$ that

$$
\sum_{x\in\{0,1\}^{\ell}}\frac{f(x)}{g(x)}=0
$$

We will use **projective coordinates**:

$$
(a_0,b_0)\oplus(a_1,b_1)=(a_0b_1+a_1b_0,b_1b_0)
$$

It satisfies associative law, and if

$$
\oplus_i (a_i,b_i)=(c,d)
$$

, $a,b,c,d$ satisfies that

$$
\sum_i\frac{a_i}{b_i}=\frac{c}{d}
$$

We can construct our GKR circuit based on it. The circuit takes $f(x),g(x),x\in\{0,1\}^{\ell}$ as input and output $(f_0,g_0)=\oplus_{x\in\{0,1\}^{\ell}}(f(x),g(x))$. It's a $\ell$-layer circuit. In the $i$-th layer, the circuit computes $(f_i(x), g_i(x)),x\in\{0,1\}^{i}$, where 

$$
(f_i(x), g_i(x))=\oplus_{y\in\{0,1\}^{\ell-i}}(f(x||y),g(x||y))
$$

It can be computed from results of the next layer, i.e, 

$$
(f_i(x),g_i(x))=(f_{i+1}(x||0), g_{i+1}(x||0))\oplus(f_{i+1}(x||1), g_{i+1}(x||1))
$$

---

We do a minor variation of the GKR protocol to serve our design decision for the circuit. When we reduce $0$-layer to $1$-layer, we get four claims:

$$
f_1(0),f_1(1),g_1(0),g_1(1)
$$

The verifier choose a random $r_0\leftarrow\mathbb F$ to combine proofs.

$$
f_1(r_0), g_1(r_0)
$$

When we reduce $i$-layer to $i+1$-layer,

$$
(f_i(X),g_i(X))=\sum_{x\in\{0,1\}^i}\tilde{\mathsf{eq}}(X,x)\left[(f_{i+1}(x||0), g_{i+1}(x||0))\oplus(f_{i+1}(x||1), g_{i+1}(x||1))\right]\\
$$

and we get four claims:

$$
f_{i+1}(\rho_i||0),f_{i+1}(\rho_i||1),g_{i+1}(\rho_i||0),g_{i+1}(\rho_i||1)
$$

Same as before, the verifier choose a random $r_i\leftarrow\mathbb F$ to combine proofs.

$$
f_{i+1}(\rho_i||r_i),g_{i+1}(\rho_i||r_i)
$$

And in the $\ell$-th layer, we get two claims:

$$
f(\rho_{\ell}),g(\rho_{\ell})
$$

which can be checked by $\mathcal V$ himself.