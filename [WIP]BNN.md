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



