https://g3lu.tistory.com/38
https://mari970.tistory.com/49
# RoPE
* Rotary Position Embedding
* Transformer 모델은 본질적으로 순서에 무관, Position embedding통해 위치 정보를 보존
* 회전행렬(Rotation Matrix)를 통해 고유각도 부여
* Absolute Position 인코딩 가능
* Relative Position의 Dependency를 통합

<P align="center"> <img src="https://github.com/user-attachments/assets/b1034452-4d19-4bac-ad8f-7c422b49c577" width="70%" height="70%"> </P>


<P align="center"> <img src="https://github.com/user-attachments/assets/fbdfb55d-2856-4a70-869c-66db0f3177f3" width="30%" height="30%"> </P>

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
\begin{pmatrix} x_1 \\ x_2 \\ x_3 \\ x_4 \\ \vdots \\ x_{d-1] \\ x_d \end{pmatrix}

\begin{pmatrix} 


#### Selft-Attention


