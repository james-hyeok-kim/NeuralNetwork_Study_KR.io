# Activation function (Non-linear)

## GELU
* Gaussian Error Linear Unit
<p align = "center">
<img src = "https://github.com/user-attachments/assets/cf043125-2639-48c3-82de-eba3d327a9ab" width="40%" height="40%">
</p>

$$GELU(x) = xP(X \leq x) = x\sigma(x) = x \cdot  \frac{1}{2} \[1+erf(x/\sqrt{2})\] $$
$$ or $$
$$ GELU(x) = 0.5x(1+tanh[\sqrt{2/\pi}(x+0.044715x^3)])$$
$$or$$
$$ GELU(x) = x \sigma(1.702x) $$


## ERF (Error Function)
* ERF 함수는 값으로 구성된 간격에 대한 가우스 오차 함수의 적분값을 반환합니다.
* $|erf(x)| < 1$

$$erf(x) = \frac{2}{\sqrt{\pi}} \int\limits_0^xe^{-t^2}dt$$


<p align = "center">
<img src = "https://github.com/user-attachments/assets/f8ee8e04-db9c-43b0-8446-10eeb4ef71ec" width="30%" height="30%">
</p>

* 가우스 함수 $(e^{-x^2})$ 역도함수는 오차함수를 상수배한것
* $t=x\mu, dt=xdu$

$$erf(x) = \frac{2x}{\sqrt{\pi}}\int\limits_0^1e^{-x^2u^2}d\mu$$

### 가우스 함수 (Gaussian Function)

$$f(x) = \frac{1}{\sigma\sqrt{2\pi}}e^{-\frac{1}{2}(\frac{x-\mu}{\sigma})^{2}}$$

* $\mu$ 모평균
* $\sigma$ 모표준편차

<p align = "center">
<img src = "https://github.com/user-attachments/assets/513fd365-961b-403f-b1d8-6ab2ebe47b4e" width = "50%" height = "50%">
  
<img src = "https://github.com/user-attachments/assets/27ea31a7-ee97-4994-9772-a13be4621043" width = "50%" height = "50%">
</p>



## SiLU
* Sigmoid Linear Unit
<p align = "center">
<img src = "https://github.com/user-attachments/assets/db4ee154-3178-4aea-bce1-18750b914ebc" width="40%" height="40%">
</p>

$$ y=x \sigma(x) $$
$$ \sigma(x) = sigmoid(x) $$

* Tflite logistic = $\sigma(x)$ ($\sigma(x)$ is the logistic sigmoid)  [link](https://pytorch.org/docs/stable/generated/torch.nn.SiLU.html)
  
## Sigmoid
<p align = "center">
<img src = "https://github.com/user-attachments/assets/17bc80bf-102d-4f23-9d0d-229de3442820" width="40%" height="40%">
</p>

$$ S(x) = \frac{1}{(1+e^{-x})} $$

