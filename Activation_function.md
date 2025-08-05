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

## Shift Sigmoid
* Shifting a sigmoid function horizontally involves adding or subtracting a constant value to the input (x) of the sigmoid function.
* This shifts the entire curve to the left or right along the x-axis.
* To shift it to the right, subtract a constant from x, and to shift it to the left, add a constant to x. 

Mathematical Explanation:
A standard sigmoid function is represented as: 


```math
f(x) = 1 / (1 + exp(-x))
```

### Sigmoid 함수와 tanh 함수

* Sigmoid : 입력을 받아 0과 1 사이의 값으로 변환하는 함수


```math
σ(x)=\frac{1}{1+e^{−x}}
```


* tanh 함수 : 입력을 받아 −1과 1 사이의 값으로 변환하는 함수입니다.

```math
tanh(x)= \frac{e^x−e^{−x}}{e^x+e^{−x}}
```


* 두함수의 관계

```math
tanh(x)=2σ(2x)−1
```

```math
σ(x)= \frac{1}{2}(tanh(\frac{x}{2})+1)
```


## GEGLU
* Gelu Gated Linear Units [(Paper Link)](https://arxiv.org/pdf/2002.05202v1)

$$ GEGLU(x, W, V, b, c) = GELU(xW + b) \odot (xV + c) $$ 

* Terminology
  * x = input vector
  * W & V = Weight vector
  * b & c = bias vector
  * $\odot$  = elementwise multiplication

* 장점
  * GEGLU는 GLU에 비해 학습 과정에서 부드럽게 기울면서 학습 과정에서 안정적 수렴 유도
  * LLM에서 ReGLU, SwiGLU와 함께 뛰어난 성능
  * 선형성과 비선형성의 결합, 복잡한 패턴 학습에 좋다
  * 발전 과정 GLU > Bilinear GLU > ReGLU > GEGLU

* GLU (Gated Linear Units)

$$ GLU(x, W, V, b, c) = \sigma(xW + b) \odot (xV + c) $$
* $\sigma$ = sigmoid

* Background
  * LSTM이나 GRU(Gated Reccurent Unit)에서 선택적으로 정보를 걸러내기 위해 등장
  * Sigmoid나 Tanh보다 기울기 소실 문제를 완화, 학습 안정화에 도움
  * 복잡한 패턴 학습을 위해 등장
  * 자연어 분야에서 효과

* Bilinear

$$ Bilinear(x, W, V, b, c) = (xW + b) \odot (xV + c) $$

* ReGLU
  
$$ ReGLU(x, W, V, b, c) = max(0, xW + b) \odot (xV + c) $$

* SwiGLU
  
$$ SwiGLU(x, W, V, b, c, \beta) = Swish_\beta(xW + b) \odot (xV + c) $$
$$Swish_\beta (x) = x\sigma(\beta x) $$
$$ SwiGLU = x\sigma(\beta x) \odot y $$

