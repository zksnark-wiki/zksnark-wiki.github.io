# Plookup

## Background

We have a commited function $f\in \mathbb F_{<n}[X]$ and a multiplacative group $H\subset \mathbb F$ of size $n$. We want to check values of $f$ over $H$ are contained in a table $t\in\mathbb F^d$, or 

$$\forall i\in [n],\exists j\in[d],f(g^i)=t_j$$

## Plookup Scheme

For convenience, we suppose $d=n+1$. From now on, we will freely **intermix the representations of vectors and functions**, say $f$ as a vector $(f(g^1),f(g^2),...,f(g^{n}))$ or $t$ as a function $t(g^1)=t_1,t(g^2)=t_2,...,t(g^{d})=t_{d}$.

Let $s\in\mathbb F^{2n+1}$ be $(f, t)$ sorted by $t$, that is, insert elements of $f$ next to the same elements in $t$. For example, when $f=(1,1,2,4,4)$ and $t=(1,2,3,4,5,6)$, $s$ will be $(1,1,1,2,2,3,4,4,4,5,6)$. 

---

Now, given $t \in \mathbb{F}^d$, $f \in \mathbb{F}^n$, $s \in \mathbb{F}^{n+d}$, define bi-variate polynomials $F, G$ as

$$F(X, Y) := (1+X)^n \cdot \prod_{i \in [n]} (Y + f_i) \prod_{i \in [d-1]} (Y(1+X) + t_i + X t_{i+1})$$

$$G(X, Y) := \prod_{i \in [n+d-1]} (Y(1+X) + s_i + X s_{i+1})$$

we have

**Claim.** $F \equiv G$ if and only if

1. $f \subset t$, and
2. $s$ is $(f, t)$ sorted by $t$.

---

The full protocol is as below:

1. $\mathcal P$ computes $h_1=s[:n+2],h_2=[n+1:]$ and sends their commitment. In the context of the above example, $h_1=(1,1,1,2,2,3)$ and $h_2=(3,4,4,4,5,6)$.
2. $\mathcal V$ chooses random $\beta,\gamma\in\mathbb F$ and sends them to $\mathcal P$.
3. $\mathcal P$ computes a polynomial $Z\in\mathbb F_{<n+1}[X]$ defined as below:
      1. $Z(g^1)=1$
      2. For $2\leq i\leq n$,
      
          $$
          Z(g^i) = \frac{(1+\beta)^{i-1} \prod_{j<i} (\gamma + f_j) \cdot \prod_{1\leq j \leq i} (\gamma(1+\beta) + t_j + \beta t_{j+1})}{\prod_{1\leq j \leq i} (\gamma(1+\beta) + s_j + \beta s_{j+1}) (\gamma(1+\beta) + s_{n+j} + \beta s_{n+j+1})}
          $$

      3. $Z(g^{n+1})=\frac{F(\alpha,\beta)}{G(\alpha,\beta)}=1$

4. $\mathcal P$ send $Z$'s commitment.
5. $\mathcal V$ checks that $Z$ is indeed of the form described above, and that $Z(g^{n+1}) = 1$. More precisely, $\mathcal V$ checks the following identities for all $x \in H$.
      1. $L_1(x)(Z(x) - 1) = 0$.
      2. 
        
          $$
          \begin{aligned}
          &(x - g^{n+1})Z(x)(1 + \beta) \cdot (\gamma + f(x))(\gamma(1+\beta) + t(x) + \beta t(g \cdot x))\\\\
          =&(x - g^{n+1})Z(g \cdot x)(\gamma(1+\beta) + h_1(x) + \beta h_1(g \cdot x))(\gamma(1+\beta) + h_2(x) + \beta h_2(g \cdot x))
          \end{aligned}
          $$

      3. $L_{n+1}(x)(h_1(x) - h_2(g \cdot x)) = 0$.
      4. $L_{n+1}(x)(Z(x) - 1) = 0$.

and outputs $\mathsf{accept}$ iff all checks hold.