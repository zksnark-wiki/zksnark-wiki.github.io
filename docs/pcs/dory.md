# Dory

## Pre-processing Procedure

Choose a vector of group generators $\mathbf g^{(0)}\leftarrow\mathbb G^{n}$.

1. $\Gamma^{(1)}_1\leftarrow\mathbb G^{n/2}$, $\Delta^{(1)}_L=\langle \mathbf g^{(0)}_L,\Gamma^{(1)}_1\rangle$, $\Delta^{(1)}_R=\langle \mathbf g^{(0)}_R,\Gamma^{(1)}_1\rangle$.
2. $\Gamma^{(2)}_2\leftarrow\mathbb G^{n/4}$, $\Delta^{(2)}_L=\langle \Gamma^{(1)}_{1,L},\Gamma^{(2)}_2\rangle$, $\Delta^{(2)}_R=\langle \Gamma^{(1)}_{1,R},\Gamma^{(2)}_2\rangle$.
3. ...

The verifier stores all of $\Delta^{(i)}_L,\Delta^{(i)}_R$.

## Proof of Knowledge

1.  The verifier chooses $\alpha_1\leftarrow\mathbb F$. Claims to be proved: 
   
    $$\begin{aligned}c_{u^{(1)}}&=\langle u^{(1)},\mathbf g^{(1)}\rangle\\c_{\mathbf g^{(1)}}&=\langle \mathbf g^{(1)},\Gamma^{(1)}_1\rangle\end{aligned}$$ 
   
    where $u^{(1)}=u^{(0)}_L+\alpha_1 u^{(0)}_R$, $\mathbf g^{(1)}=\mathbf g^{(0)}_L+\alpha_1^{-1}\mathbf g^{(0)}_R$. The verifier computes $c_{\mathbf g^{(1)}}$ by

    $$c_{\mathbf g^{(1)}}=\Delta^{(1)}_L+\alpha_1^{-1}\Delta^{(1)}_R$$ 

    and $c_{u^{(1)}}$ by 

    $$c_{u^{(1)}}=c_{u^{(0)}}+\alpha_1^{-1}\langle u^{(0)}_L,\mathbf g^{(0)}_R\rangle+\alpha_1\langle u^{(0)}_R,\mathbf g^{(0)}_L\rangle$$

    where the cross terms are sent by the prover.

2.  The verifier chooses $\alpha_2\leftarrow\mathbb F$. Claims to be proved: 
   
    $$\begin{aligned}c_{u^{(2)}}&=\langle u^{(2)},\mathbf g^{(2)}\rangle\\c_{\mathbf g^{(2)}}&=\langle \mathbf g^{(2)},\Gamma^{(2)}_1\rangle\\c_{\Gamma^{(2)}_1}&=\langle\Gamma^{(2)}_1,\Gamma^{(2)}_2\rangle\end{aligned}$$ 
   
    where $\Gamma^{(2)}_1=\Gamma^{(1)}_{1,L}+\alpha_2\Gamma^{(1)}_{1,R}$. The verifier computes $c_{\Gamma^{(2)}_1}$ by

    $$c_{\Gamma^{(2)}_1}=\Delta^{(2)}_L+\alpha_2\Delta^{(2)}_R$$ 

    and the other two by the cross terms sent by the prover.

3.  The verifier chooses $\alpha_3\leftarrow\mathbb F$. Claims to be proved:

    $$\begin{aligned}c_{u^{(3)}}&=\langle u^{(3)},\mathbf g^{(3)}\rangle\\c_{\mathbf g^{(3)}}&=\langle \mathbf g^{(3)},\Gamma^{(3)}_1\rangle\\c_{\Gamma^{(3)}_1}&=\langle\Gamma^{(3)}_1,\Gamma^{(3)}_2\rangle\\c_{\Gamma_{2}^{(3)}}&=\langle\Gamma^{(3)}_2,\Gamma^{(3)}_3\rangle\end{aligned}$$

4.  ...