# Binary Neutral Network

<p align = "center">
<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/762ce26c-7a58-405b-bc3a-577a0c38d6e7" />
</p>
  
$$
I * W \approx (I \oplus B)\alpha
$$

* $W$: 실수 값을 가지는 가중치 필터 (real-value weight filter)
    * $\mathbf{W} \in \mathbb{R}^{c \times w \times h}$

* $B$: +1 또는 -1 값을 가지는 이진 필터 (binary filter)
    * $\mathbf{B} \in \{+1, -1\}^{c \times w \times h}$

* $α$: 스케일링 팩터 (scaling factor)
    * $\alpha \in \mathbb{R}^+$ 이며, $\mathbf{W} \approx \alpha\mathbf{B}$를 만족하는 양의 실수입니다.

## 최적의 $\alpha$ 와 $B$ 찾기
우리의 목표는 원래의 실수 가중치 필터 $W$를 이진 필터 $B$와 스케일링 팩터 $\alpha$의 곱($\alpha B$)으로 근사(approximate)하는 것입니다.

가장 좋은 근사는 $W$와 $\alpha B$의 차이가 가장 작을 때이겠죠? 이 차이를 수학적으로 표현한 것이 바로 첫 번째 수식입니다.

$$J(B, \alpha) = \parallel W - \alpha B \parallel ^2$$

- **$W$**: 원래의 full-precision(32-bit float) 가중치 벡터입니다.
- **$B$**: +1 또는 -1의 값만 가지는 이진 가중치 벡터입니다.
- **$\alpha$**: 양의 실수인 스케일링 팩터입니다.
- **$\parallel \cdot \parallel ^2$**: 두 벡터 간의 유클리드 거리(L2-norm)의 제곱입니다. 이 값이 작을수록 두 벡터가 비슷하다는 의미입니다. 우리는 이 $J$ 값을 최소화하는 $\alpha$와 $B$를 찾고 싶습니다.

---

### 1. 수식 전개
벡터의 L2-norm 제곱은 $(W-\alpha B)^T(W-\alpha B)$ 로 계산할 수 있습니다. 

$$J(B, \alpha) = \parallel W - \alpha B \parallel ^2 = (W-\alpha B)^T(W-\alpha) B$$

이걸 전개하면 다음과 같습니다.

$$J(B, \alpha) = W^T W - 2\alpha W^T B + \alpha^2 B^T B$$

여기서 $B$의 모든 요소는 +1 아니면 -1이므로, $B$와 자기 자신을 내적한 $B^T B$는 벡터의 길이 $n$이 됩니다. ($B^T B = \sum(b_i^2) = \sum(1) = n$)
또한, $W$는 주어진 값이므로 $W^T W$는 상수(constant) $c$로 볼 수 있습니다. 따라서 수식은 이렇게 간단해집니다.

$$J(B, \alpha) = \alpha^2 n - 2\alpha W^T B + c$$

#### 전개 과정

##### 1-1. 괄호 안의 전치(Transpose) 풀기

먼저, 첫 번째 괄호인 $(W - \alpha B)^T$ 를 풀어줍니다. 행렬/벡터의 덧셈과 뺄셈에 대한 전치는 각 항에 분배될 수 있습니다.

$$(A - C)^T = A^T - C^T$$

이 규칙을 적용하면 다음과 같이 됩니다.

$$(W - \alpha B)^T = W^T - (\alpha B)^T$$

이제 원래 식에 이걸 다시 넣어보면 식이 이렇게 바뀝니다.

$$J(B, \alpha) = (W^T - \alpha B^T) (W - \alpha B)$$

##### 1-2. 중간 항 정리하기
여기서 중요한 점은 두 번째 항과 세 번째 항이 사실상 같다는 것입니다.

- $W^T B$는 벡터 $W$와 $B$의 내적으로, 결과는 스칼라(숫자 하나) 입니다.
- 마찬가지로 $B^T W$도 벡터 $B$와 $W$의 내적으로, 결과는 같은 스칼라입니다.

벡터 내적은 순서를 바꿔도 결과가 같으므로 ($a \cdot b = b \cdot a$), $W^T B = B^T W$ 가 성립합니다.

따라서, $-\alpha W^T B$ 와 $-\alpha B^T W$ 는 같은 항입니다. 이 둘을 합치면 다음과 같이 됩니다.

$$-\alpha W^T B - \alpha W^T B = -2\alpha W^T B$$

---

### 2. 최적의 $B^*$ 찾기
위 수식에서 $J$를 최소화하려면, 빼주는 항인 $2\alpha W^T B$ 를 최대화해야 합니다. $\alpha$는 양수이므로 $W^T B$ 를 최대로 만들면 됩니다.

$W^T B$ 는 두 벡터의 내적($\sum w_i b_i$)인데, 이 값을 최대로 만들려면 각 $w_i$와 $b_i$의 부호가 같아야 합니다.

- $w_i > 0$ 이면, $b_i = +1$
- $w_i < 0$ 이면, $b_i = -1$

이것이 바로 sign 함수의 정의입니다. 따라서 최적의 $B$는 다음과 같습니다.

$$B^* = \text{sign}(W)$$

---

### 3. 최적의 $\alpha^*$ 찾기
이제 최적의 $B^*$를 알았으니, $J$를 $\alpha$에 대해 미분해서 그 값이 0이 되는 지점을 찾으면 최적의 $\alpha$를 구할 수 있습니다.

$$\frac{\partial J}{\partial \alpha} = 2\alpha n - 2W^T B^* = 0$$

이 식을 $\alpha$에 대해 정리하면,

$$\alpha^* = \frac{W^T B^*}{n}$$

이제 위에서 구한 $B^* = \text{sign}(W)$를 대입하면,

$$\alpha^* = \frac{W^T \text{sign}(W)}{n} = \frac{\sum |W_i|}{n} = \frac{1}{n} \parallel W \parallel_{l1}$$

$W^T \text{sign}(W)$는 각 가중치의 절대값($|w_i|$)을 모두 더한 것과 같으며, 이는 L1-norm ($\parallel W \parallel_{l1}$)으로 표현됩니다.


## XOR net

<img width="400" height="200" alt="image" src="https://github.com/user-attachments/assets/fc6ee1e9-e785-4320-bfc2-0a37bc109cb5" />

### XOR net Improvement
기존 XNOR-Net은 실수 가중치($W$)를 이진 가중치($B$)와 스케일링 팩터($\alpha$)의 곱으로 근사했습니다.

여기서 $\alpha$는 가중치의 절대값 평균($\frac{1}{n} \parallel W \parallel_{l1}$)으로 분석적으로(analytically) 계산되었죠.

하지만 이 방식에는 두 가지 문제가 있었습니다.

1.  비싼 연산 비용: 입력값(activation)에 대한 스케일링 팩터($\beta$)는 새로운 입력이 들어올 때마다 매번 계산해야 해서, 실시간 처리에 부담이 되었습니다.
2.  최적화의 한계: $\alpha$와 $\beta$가 역전파(backpropagation)를 통해 학습되는 값이 아니라, 미리 정해진 공식으로 계산되므로 모델의 성능을 최적으로 끌어올리는 데 한계가 있었습니다.

XNOR-Net++ $\rightarrow$ 이 스케일링 팩터를 역전파로 직접 학습하게 만들면 더 좋지 않을까?

### 핵심 아이디어: 학습 가능한 통합 스케일링 팩터 ($\Gamma$)
XNOR-Net++의 가장 중요한 아이디어는 가중치의 스케일링 팩터($\alpha$)와 입력의 스케일링 팩터($K$ 또는 $\beta$)를 하나로 합쳐, 학습 가능한(trainable) 단일 스케일링 팩터 $\Gamma$ (감마) 로 만든 것입니다.

$$\mathcal{I} * \mathcal{W} \approx (\text{sign}(\mathcal{I}) \circledast \text{sign}(\mathcal{W})) \odot K \alpha$$

XNOR-Net++는 이 식을 훨씬 간단하게 만들었습니다.

$$\mathcal{I} * \mathcal{W} \approx (\text{sign}(\mathcal{I}) \circledast \text{sign}(\mathcal{W})) \odot \Gamma$$

여기서 $\Gamma$는 모델 학습 과정에서 손실 함수(loss function)를 줄이는 방향으로 자동으로 최적화됩니다. 이로 인해 다음과 같은 장점을 얻습니다.

-   **속도 향상**: 입력마다 스케일링 팩터를 계산할 필요가 없어지므로 연산 속도가 더 빨라집니다.
-   **정확도 향상**: 모델이 데이터에 가장 적합한 스케일링 값을 직접 학습하므로, 분석적으로 계산할 때보다 더 높은 정확도를 달성할 수 있습니다.

---

## $\Gamma$를 구성하는 다양한 방법
XNOR-Net++ 논문에서는 최적의 $\Gamma$를 찾기 위해 4가지 구조를 실험했습니다.

-   **Case 1**: 각 출력 채널마다 하나의 스케일링 팩터를 사용합니다. (가장 간단한 방식)
-   **Case 2**: 출력의 각 픽셀마다 스케일링 팩터를 사용합니다. 유연성은 높지만, 파라미터 수가 너무 많아져 과적합(overfitting) 위험이 있습니다.
-   **Case 3**: $\Gamma$를 채널 벡터($\alpha$)와 공간 벡터($\beta$)의 외적(outer product)으로 분해하여 파라미터 수를 줄입니다.
-   **Case 4**: $\Gamma$를 3개의 벡터(채널, 높이, 너비)로 분해합니다. 실험 결과, 이 방식이 **가장 높은 성능**을 보였습니다.

### 한계 : 느린 역전파 (Backward Pass)

* 고정밀도 그래디언트: 모델의 가중치를 업데이트하려면 손실(loss)로부터 그래디언트(gradient)를 계산하여 역전파해야 합니다. Naïve BNN과 XNOR-Net에서는 이 그래디언트를 여전히 고정밀도(예: 32-bit float)로 유지합니다.

* 역전파 병목 현상: 순전파는 저비용의 bitwise 연산으로 빨라졌지만, 역전파는 여전히 비싼 고정밀도 연산에 의존합니다. 이로 인해 전체 학습 시간의 대부분이 역전파 과정에서 소모되는 병목 현상이 발생합니다.

## 해결을 위한 노력: DoReFa-Net
이러한 한계를 극복하기 위해 등장한 것이 바로 DoReFa-Net입니다. DoReFa-Net은 가중치(Weights)와 활성화(Activations)뿐만 아니라, 그래디언트(Gradients)까지 저정밀도로 양자화하는 방법을 제안했습니다.

이를 통해 순전파뿐만 아니라 역전파 과정에서도 bitwise 연산을 활용하여 학습 과정 전체의 속도를 높일 수 있게 되었습니다.

### 핵심 아이디어: 3요소 저정밀도 양자화
DoReFa-Net의 핵심은 각기 다른 특성을 가진 가중치, 활성화, 그래디언트에 맞는 맞춤형 양자화 방법을 제안한 것입니다.

1. 가중치(Weights) 양자화

k-bit 양자화: tanh 함수를 사용해 가중치 값을 [-1, 1] 사이로 먼저 제한한 뒤, k-bit로 양자화합니다. 이는 값의 범위를 예측 가능하게 만들어 안정적인 양자화를 돕습니다.


역전파를 위한 설계: 특히 이진 가중치를 사용할 때, XNOR-Net처럼 채널별 스케일링 팩터를 사용하지 않고 모든 필터에 대해 단일 스케일링 팩터를 사용합니다. 이는 역전파 과정에서 그래디언트와 가중치 간의 연산 또한 bitwise 연산으로 수행할 수 있게 만드는 매우 중요한 설계적 결정입니다.


2. 활성화(Activations) 양자화
활성화 값은 이전 레이어의 활성화 함수(예: ReLU 후 clipping)를 통해 이미 [0, 1] 범위로 제한되어 있다고 가정합니다.

따라서 비교적 간단하게 k-bit 양자화 함수를 바로 적용합니다.

3. 그래디언트(Gradients) 양자화 (가장 큰 혁신 ✨)
그래디언트는 가중치나 활성화와 달리 

값의 범위가 정해져 있지 않아(unbounded) 양자화가 가장 까다롭습니다.

DoReFa-Net은 이를 해결하기 위해 다음과 같은 방법을 사용합니다.


동적 범위 설정: 그래디언트 값의 최대 절댓값을 이용해 양자화 범위를 동적으로 결정합니다.


확률적 양자화 (Stochastic Quantization): 양자화 과정에서 발생할 수 있는 편향(bias)을 줄이고 성능을 높이기 위해, 약간의 균등 노이즈(uniform noise)를 추가합니다. 이는 그래디언트처럼 민감한 값을 저정밀도로 양자화할 때 매우 효과적인 장치입니다.

도, 레, 파에서 따온 이름

Do: 가중치 (Weights), Re: 활성화 (Activations), Fa: 그래디언트 (Gradients)


### DoReFa-Net Forward & Backward

#### Forward Pass (Weight 양자화)
* 1-bit : +1 or -1로 변환
  * $r_i$ 전체 가중치
  * $E(|r_{i}|)$ 스케일 팩터 절대값 평균

$$
r_{o} = \text{sign}(r_{i}) \times E(|r_{i}|)
$$

* K-bit: 양자화를 위해 tanh함수를 적용하여 가중치 \[-1,1\]범위로 제한

$$
r_{o} = 2 \times \text{quantize}_{k}\left( \left( \frac{\tanh(r_{i})}{2 \times \max(|\tanh(r_{i})|)} + 0.5 \right) - 1 \right)
$$

#### Backward Pass (Weight 양자화)

* 역전파 시에는 STE(Stright-Through Estimator)를 사용
* 1-bit

$$
\frac{\partial c}{\partial r_{i}} = \frac{\partial c}{\partial r_{o}}
$$

* K-bit: 체인룰

$$
\frac{\partial c}{\partial r_{i}} = \frac{\partial r_{o}}{\partial r_{i}} \times \frac{\partial c}{\partial r_{o}}
$$

#### Forward Pass (Activations 양자화)
* 활성함수 값은 이전 레이어를 \[0,1\]범위로 제한
* 활성화 값 $r$

$$f_{a}^{k}(r) = \text{quantize}_{k}(r)$$

#### Backward Pass (Activations 양자화)

* Weight와 마찬가지로 계산

#### Forward Pass (Gradients 양자화)

* 순전파 그래디언트 없음으로 입력 = 출력 $r_o = r_i$

#### Backward Pass (Gradients 양자화)

* 그래디언트 값을 \[0, 1\] 범위로 정규화한 후, 균등분포(-0.5 ~ 0.5)를 따르는 노이즈(N(k))를 추가합니다. 이 노이즈는 양자화로 인해 발생할 수 있는 편향을 줄여 성능을 향상

$$ \frac{\partial c}{\partial r_{i}} = f_{\gamma}^{k}\left( \frac{\partial c}{\partial r_{o}} \right) $$











