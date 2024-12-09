# Activation function (Non-linear)

## GELU
* Gaussian Error Linear Unit
<p align = "center">
<img src = "https://github.com/user-attachments/assets/cf043125-2639-48c3-82de-eba3d327a9ab" width="40%" height="40%">
</p>

$$ GELU(x) = 0.5x(1+tanh[\sqrt{2/\pi}(x+0.044715x^3)])$$

$$or$$

$$ GELU(x) = x \sigma(1.702x) $$

## SiLU
* Sigmoid Linear Unit
<p align = "center">
<img src = "https://github.com/user-attachments/assets/db4ee154-3178-4aea-bce1-18750b914ebc" width="40%" height="40%">
</p>

$$ y=x \sigma(x) $$
$$ \sigma(x) = sigmoid(x) $$
  
## Sigmoid
<p align = "center">
<img src = "https://github.com/user-attachments/assets/17bc80bf-102d-4f23-9d0d-229de3442820" width="40%" height="40%">
</p>

$$ S(x) = \frac{1}{(1+e^{-x})} $$
