https://g3lu.tistory.com/38
https://mari970.tistory.com/49
# RoPE
* [PDF](https://arxiv.org/pdf/2104.09864)
* Rotary Position Embedding
* Transformer 모델은 본질적으로 순서에 무관, Position embedding통해 위치 정보를 보존
* 회전행렬(Rotation Matrix)를 통해 고유각도 부여
* Absolute Position 인코딩 가능
* Relative Position의 Dependency를 통합

<P align="center"> <img src="https://github.com/user-attachments/assets/b1034452-4d19-4bac-ad8f-7c422b49c577" > </P>


<P align="center"> <img src="https://github.com/user-attachments/assets/fbdfb55d-2856-4a70-869c-66db0f3177f3" > </P>

* 복소 평면상에서 회전을 토큰위치정보를 통해 벡터에 표현. 
* Query m
* Key n

## Formulation
### Position-Encoded Query and Key Vectors
$$ f_q(x_m,m) = (W_qx_m)e^{im\theta} $$
$$ f_k(x_n,n) = (W_kx_n)e^{in\theta} $$

* $f_q(x_m,m)$ : Query Vector (Self-attention)
* $W_q$ : Weight Vector of Query
* $x_m$ : embedding vector. 고정된 크기의 실수 벡터로 변환
* $e^{im\theta}$ : rotation(회전). 복소수 평며에서 $m\theta$ 만큼 회전시키는 연산.

### Function $g$

$$ g(x_m,x_n,m-n)=Re[(W_qx_m)(W_kx_n)*e^{i(m-n)\theta}]$$
* $g$ : Query Vector($x_m$)와 Key Vector($x_n$)간의 내적을 복소수 곱셈을 통해 계산
* Query복소수 곱셈 Key복소수 한 결과의 실수 부분만 취함
* 실수값만 취하는 이유는 Softmax를 통해 중요도를 결정할때 실수값이 필요하기 때문

### 2D General Form
$$ f_{\{q,k\}}(x_m,m) = R_{\Theta,m}^{d}W_{\{q,k\}}x_m $$

$$ R_{\Theta,m}^{d} =  
\begin{pmatrix}
  cos(m\theta_{1}) & -sin(m\theta_{1}) & 0 & 0 & \cdots & 0 & 0 \\
  sin(m\theta_{1}) & cos(m\theta_{1}) &  0 & 0 & \cdots & 0 & 0 \\
  0 & 0 & cos(m\theta_{2}) & -sin(m\theta_{2}) & \cdots & 0 & 0 \\
  0 & 0 & sin(m\theta_{2}) & cos(m\theta_{2}) &  \cdots & 0 & 0 \\
  \vdots  & \vdots  & \vdots  & \vdots  & \ddots & \vdots  &\vdots \\
  0 & 0 & 0 & 0 & \cdots & cos(m\theta_{d/2}) & -sin(m\theta_{d/2}) \\
  0 & 0 & 0 & 0 & \cdots & sin(m\theta_{d/2}) & cos(m\theta_{d/2}) \\
 \end{pmatrix}$$

$$ \Theta = \theta_i = 10000^{-2(i-1)/d}, i\in[1,2,\ldots, d/2] $$


#### Computational efficient realization of rotary matrix multiplication

$$ R_{\Theta,m}^{d}x = 
\begin{pmatrix} 
  x_1 \\
  x_2 \\ 
  x_3 \\ 
  x_4 \\ 
  \vdots \\ 
  x_{d-1} \\ 
  x_d \\
\end{pmatrix}
\otimes
\begin{pmatrix}
  cos_{m\theta_1} \\
  cos_{m\theta_1} \\
  cos_{m\theta_2} \\
  cos_{m\theta_2} \\
  \vdots \\
  cos_{m\theta_{d/2}} \\
  cos_{m\theta_{d/2}} \\
\end{pmatrix}
+
\begin{pmatrix} 
  -x_2 \\
  x_1 \\ 
  -x_4 \\ 
  x_3 \\ 
  \vdots \\ 
  -x_{d} \\ 
  x_{d-1} \\
\end{pmatrix}
\otimes
\begin{pmatrix}
  sin_{m\theta_1} \\
  sin_{m\theta_1} \\
  sin_{m\theta_2} \\
  sin_{m\theta_2} \\
  \vdots \\
  sin_{m\theta_{d/2}} \\
  sin_{m\theta_{d/2}} \\
\end{pmatrix}
$$

* $\otimes = tensor\ product $

#### Derivation
(1) Query, Key $\rightarrow$ 회전 행렬 변환

$$ q_m = R_d^{\Theta,m}W_qx_m $$
$$ k_n = R_d^{\Theta,n}W_kx_n $$

(2) 내적 계산

$$ q_m^{\top}k_n = (R_d^{\Theta,m}W_qX_m)^\top (R_d^{\Theta,n}W_kx_n) $$

(3) 전치 행렬 사용

$$ (R_d^{\Theta,m}W_qx_m)^{\top} = x_{m}^{\top}W_{q}^{\top}(R_{d}^{\Theta,m})^{\top} $$

(4) 회전 행렬의 성질

회전 행렬 $(R_{d}^{\Theta,m}W_qx_m)^{\top}, (R_{d}^{\Theta,m})^{\top}$ 는 직교 행렬로 전치행렬은 역행렬과 같다

$$ (R_d^{\Theta,m}W_qx_m)^{\top} = (R_d^{\Theta,m}W_qx_m)^{-1} $$

* $(R_{d}^{\Theta,m}W_qx_m)^{\top}, (R_{d}^{\Theta,m})^{\top}$ 직교 행렬인 이유
   - 행,열 이 서로 직교하고 모두 단위벡터 이기 때문
   - 내적은 0이고 각 벡터의 크기는 1이기 때문

$$ R_2(\theta) = 
\begin{pmatrix}
  cos\theta & -sin\theta \\
  sin\theta & cos\theta \\
\end{pmatrix}
$$
 
(5) 상대적 회전

* n과 m의 차이에 따른 회전 행렬

$$ (R_d^{\Theta,m})^{\top}R_d^{\Theta,n} = R_d^{\Theta, n-m} $$

(6) 최종 결과

$$ q_m^{\top}k_n = x_m^{\top}W_q^{\top}R_d^{\Theta,n-m}W_kx_n $$

## Long term Decay
* 멀어질수록 내적 값이 작아져야 한다

$$ \theta_i = 10000^{-2i/d} $$
* $d$: 모델 차원의 수
* $i$: 차원 인덱스
* $\theta_i$: 차원이 커질수록 작은 값, 고차원 인코딩 더 느리게 변화, 유사도 감소


# RoPE with Linear Attention (Linear Transformer)
* Linear Attention 계산 복잡도를 줄이기 위해 고안

기존 attention

$$ Attention(Q,K,V) = softmax( \frac{QK^{\top}}{\sqrt{d_k}} )V $$

* 계산 복잡도 $O(N^2d_k)$

유사도를 나타내는 함수 정의 $sim(Q,K)$

$$ Attention(Q,K,V) = \frac{\displaystyle\sum_{n=1}^{N}\phi(Q)^{\top} \phi(K)\ V}{\displaystyle\sum_{n=1}^{N}\phi(Q)^{\top} \phi(K)} $$

* $\phi(x)$: 비음수 함수(non-negative function)
* 대부분 $\phi(x) = elu(x) + 1 $을 사용
* 계산 복잡도 $O(Nd_k)$


