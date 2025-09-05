# Pianist

The Pianist protocol is a distributed zkSNARK system designed to enable multiple machines to collaboratively generate a single, succinct proof. The protocol's core innovation lies in the elegant combination of a **bivariate Plonk** constraint system and a **distributively computable KZG** polynomial commitment scheme. This allows for the parallelization of the computationally intensive proving process, leading to a linear increase in prover speed as more machines are added. A key architectural benefit is that this scalability is achieved while maintaining a constant, asymptotically optimal communication overhead, proof size, and verifier time, regardless of the complexity of the underlying computation or the number of transactions being processed.   

