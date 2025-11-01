### 5 Steps for Speculative Decoding
1. Guessing
  * $M_q$(Draft Model) sequentially generates tokens (𝜸)
2. Parallel Verification (1 + 𝜸 → prefix + 𝜸 )
  * $M_p(prefix) = p_1(x)$
  * $M_p(prefix + x_1) = p_2(x)$
  * …
  * $Mp(prefix + x_1 + x_2 + … + x_5) = p_6(x)$
3. Acceptance / Rejection
  * $q_i(x_i): M_q(Target model)$ probability
  * Accept: $(p_i(x) > q_i(x))$, Target model agree to accept
  * Reject : $(1 - \frac{p_i(x)}{q_i(x))})$
  * If the token is rejected, all subsequent gesses are discarded. 
4. Correction & Sampling
  * All guesses are accepted (n = 𝜸)
    * $\gamma + 1$ tokens are genearted at once
  * k-th token is rejected (n = k - 1)
    * Append only the accepted tokens $(x_1 ... , x_{k-1})$
    * k-th token must be corrected 
5. iteration 
