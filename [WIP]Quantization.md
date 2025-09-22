## Quantization (양자화)

* 데이터를 큰 비트(fp32)에서 작은 비트(int8, fp8)로 표현하여 계산비용을 낮추는 것

<p align="center">
<img width="600" height="300" alt="image" src="https://github.com/user-attachments/assets/3e628d1a-6006-456e-97ec-ea12506c3edf" />
</p>

### Symmetric vs Asymmetric

<p align="center">
<img width="600" height="300" alt="image" src="https://github.com/user-attachments/assets/c5ccfc9d-25be-4241-9f3b-9ff3b4ee55e2" />
</p>

#### Example

$$
f_q(x, s, z) = \text{Clip}(\text{round}(s \cdot x) + z) \quad (1)
$$

* $f_q(x,s,z)$: 양자화(quantization)가 적용된 최종 값입니다.

* x: 양자화하려는 원래의 실수(real value) 값입니다 (예: float32).

* s: 스케일(scale) 값으로, 실수를 정수 범위에 맞게 조절하는 역할을 합니다.

* z: 제로 포인트(zero-point)로, 0.0f 실수가 어떤 정수 값에 매핑되는지를 나타냅니다.

* round(): 반올림 함수입니다.

* Clip(): 계산된 값을 미리 정해진 특정 범위(예: 8비트 정수라면 0 ~ 255) 안에 있도록 잘라내는 함수입니다.


$$
s = \frac{\beta - \alpha}{\beta_q - \alpha_q} \quad (2)
$$

$\alpha,\beta$:min/max values of filter/layer

$\alpha_q, \beta_q$ :min/max values of quantized value

위 표에서 최댓값은 4.67 이고 최솟값은 -4.75 이므로, α=−4.75, β=4.67 가 됨. 

FP32를 INT8(Unsigned) 로 변경한다고 가정하면, $\alpha_q=0, \beta_q=255$가 됨.

$$
\begin{align*}
s &= \frac{\beta_q - \alpha_q}{\beta - \alpha} \\ &= \frac{255 - 0}{4.67 - (-4.75)} \\ &= \frac{255}{9.42} \\ &\approx 27.07
\end{align*}
$$

$$
z = \text{round}\left(\frac{\beta \alpha_q - \alpha \beta_q}{\beta - \alpha}\right)
$$

$$
\begin{align\*}
z &= \text{round}\left(\frac{4.67 \cdot 0 - (-4.75) \cdot 255}{4.67 - (-4.75)}\right) \\ &= \text{round}\left(\frac{1211.25}{9.42}\right) \\ &= \text{round}(128.58) \\ &= 129
\end{align\*}
$$

구한 s,z 를 이용해 x=−3.57 을 식(1)을 통해 양자화를 진행하면 아래와 같다

$$
\begin{align*}
q &= \text{round}(s \cdot x) + z \\
&= \text{round}(27.07 \cdot (-3.57)) + 129 \\
&= \text{round}(-96.64) + 129 \\
&= -97 + 129 \\
&= 32
\end{align*}
$$

clipping 은 범위를 초과하는 값을 범위안에 가지도록함

$$
\text{clip}(x, \alpha_q, \beta_q) =
\begin{cases} 
\alpha_q & \text{if } x < \alpha_q \\
x & \text{if } \alpha_q \le x \le \beta_q \\
\beta_q & \text{if } x > \beta_q 
\end{cases}
$$
​
 
이와 같은 quantization 기법을 uniform quantization이라고 부르며, 결과로 얻어지는 quantized values는 균등하게 분포된다.

### Quantized Matrix Multiplication
<p align ="center">
<img width="600" height="250" alt="image" src="https://github.com/user-attachments/assets/ebbae30d-6c04-4e26-8b50-84ac8b262d9f" />
</p>

$$
Y = XW + b \text{, where } X \in \mathbb{R}^{m \times p}, W \in \mathbb{R}^{p \times n}, b \in \mathbb{R}^{n}, Y \in \mathbb{R}^{m \times n}
$$

$$
Y_{i,j} = b_j + \sum_{k=1}^{p} X_{i,k} W_{k,j} \quad (1)
$$


$$
\begin{align*}
Y_{i,j} &= b_j + \sum_{k=1}^{p} X_{i,k} W_{k,j} \\
&= s_b(b_{q,j} - z_b) + \sum_{k=1}^{p} s_X(X_{q,i,k} - z_X) s_W(W_{q,k,j} - z_W) \\
&= s_b(b_{q,j} - z_b) + s_X s_W \sum_{k=1}^{p} (X_{q,i,k} - z_X)(W_{q,k,j} - z_W) \\
&= s_b(b_{q,j} - z_b) + s_X s_W \left[ \left(\sum_{k=1}^{p} X_{q,i,k} W_{q,k,j}\right) - \left(z_W \sum_{k=1}^{p} X_{q,i,k}\right) - \left(z_X \sum_{k=1}^{p} W_{q,k,j}\right) + p z_X z_W \right] \\
&= s_Y(Y_{q,i,j} - z_Y)
\end{align*}
$$

*위 식에서 X_q,W_q,b_q,Y_q는 X,W,b,Y 각각의 양자화(Quantization)가 적용된 행렬입니다. s_X,s_W,s_b,s_Y는 각각의 **스케일(scale)*이고, z_X,z_W,z_b,z_Y는 각각의 제로 포인트(zero point) 입니다

$$
Y_{q,i,j} = z_Y + \frac{s_b}{s_Y}(b_{q,j} - z_b) + \frac{s_X s_W}{s_Y} \left[ \left(\sum_{k=1}^{p} X_{q,i,k} W_{q,k,j}\right) - \left(z_W \sum_{k=1}^{p} X_{q,i,k}\right) - \left(z_X \sum_{k=1}^{p} W_{q,k,j}\right) + p z_X z_W \right]
$$


미리 구할수 있는 것들

* $z_Y$
* $\frac{s_b}{s_Y}(b_{q,j} - z_b)$
* $\left(z_X \sum_{k=1}^{p} W_{q,k,j}\right)$
* $p z_X z_W$

### Static vs. Dynamic Quantization
Clipping range [α,β]를 결정하는 여러 가지 calibration 방법이 있지만 weights에 대해서 정적(static)으로 계산될 수 있다. 그러나 activation map은 각 입력 샘플에 따라 다르다

### PTQ vs QAT
<p align="center">
<img width="600" height="200" alt="image" src="https://github.com/user-attachments/assets/59c1987c-1663-4698-90d9-bd4dc21b1b3f" />
</p>
