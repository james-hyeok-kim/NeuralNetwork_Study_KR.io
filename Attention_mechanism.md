[Reference](https://velog.io/@sjinu/%EA%B0%9C%EB%85%90%EC%A0%95%EB%A6%AC-Attention-Mechanism)
# Attention Mechanism

## Attention
* 어텐션의 기본 아이디어는 디코더에서 출력 단어를 예측하는 매 시점(step)마다, 인코더의 입력 시퀀스를 다시 참고

<p align="center"> <img src="https://github.com/user-attachments/assets/5add34e2-deea-4c74-94e7-d595e7766d6d"></p>

* 어텐션 함수는 주어진 '쿼리(Query)'에 대해 모든 '키(Key)'의 유사도 
* 이 유사도를 키(Key)와 매핑되어 있는 각각의 '값(Value)'에 반영
* '유사도가 반영된' 값(Value)을 모두 더해서 리턴하고, 어텐션 값(Attention value)를 반환

### Dot-Product Attention
<p align="center"> <img src="https://github.com/user-attachments/assets/66891df0-2d55-489b-a1b0-30559790ccb4" ></p>

* Decoder의 세번째 LSTM Cell에서 출력 단어를 예측할 때 어텐션 메커니즘을 사용하는 예시
  - (왼쪽 오렌지색 encoder, 오른쪽 초록색 decoder)
<p align="center"> <img src="https://github.com/user-attachments/assets/9dccf543-31e7-4d7d-a0f4-fd92789211dd" ></p>

* Attention 메커니즘을 적용함으로써, 세번째 단어를 예측할 때 예측 단어와 encoder의 모든 시퀀스('I', 'am', 'a', 'stduent')의 관계를 파악하게 됩니다.
이 때, 파악하는 방식은 그림 내에도 존재하는 softmax를 이용함으로써 이루어집니다.


* 출력 단어를 예측할 때 softmax를 통과시킨 input sequence(즉, 확률분포; 위 그림의 초록색 삼각형)를 추가적으로 전달
* decoder가 새로운 단어를 예측하는 데 있어서 Recurrent하게 전달된 정보 외에도 input sequence의 정보를 참고할 수 있는 길(path)을 마련

$h_t$ : t 시점에서 encoder의 hidden state (예시에서 4차원)
$s_t$ : t 시점에서 decoder의 hidden state (예시에서 4차원)

* Attention value $a_t$을 구하기 위해서는 크게 아래의 세 과정을 거쳐 얻을 수 있습니다.
  - $h_t, s_t$를 활용해 Attention Score($e_t$)를 구한다.
  - softmax를 활용해 Attention Distribution을 구한다.
  - softmax를 통해 구한 분포를 토대로 인코더에서, 가중치와 hidden state를 가중합하여 Attention Value를 구한다.

#### Step 1. Attention Score($e_t$)
<p align="center"> <img src="https://github.com/user-attachments/assets/ce6e20c1-1da6-450e-aa89-2e1018d5021b" ></p>

$score(s_t ,h_i)=s_t^Th_i$
​이 때 결과 값은 scalar가 된다.

* decoder의 time step은 t인 반면, 참고하는 encoder의 time step은 1부터 N까지
* encoder의 모든 은닉 상태에 대한 decoder의 time step t에서의 Attention score를 계산하면 아래와 같이 나타낼 수 있습니다.

$e_t=[s_t^{T}h_1, ..., s_T^{T}h_N]$

#### Step 2. Attention Distribution
<p align="center"> <img src="https://github.com/user-attachments/assets/6b189a97-3aa8-4abf-8d96-9b9d77b1ad3c" ></p>

 * Attention scores $e_t$에 softmax(소프트맥스)함수를 적용해, 모든 값의 합이 1이 되는 확률 분포 Attention Distribution을 얻습니다.
 * 즉, 위의 그림에 있는 Attention Distribution을 얻기 위해 아래와 같은 식을 사용하면 됩니다.
   - $α_t =softmax(e_t)⋯ a_t$ 가 아닌 $α^t$입니다.
   - 이 때 각각의 값을 Attention Weight(어텐션 가중치)라고 합니다.
  

#### Step 3. Attention Weight + Hidden state 가중 -> Attention Value
<p align="center"> <img src="https://github.com/user-attachments/assets/52e9010a-4752-475b-ab47-7d62e55da4e8" ></p>

이에 대한 식은 아래와 같이 기술할 수 있습니다.

$a_t=\displaystyle\sum_{i=1}^Nα_i^th_i$​
* 이러한 어텐션 값 $a_t$ 는 인코더의 맥락을 포함하고 있기 때문에 Context Vector(맥락 벡터) 라고도 불립니다
* (정확히는, decoder 내 time step t의 context vector)

#### Step 4. Concatenate

<p align="center"> <img src="https://github.com/user-attachments/assets/51f4f949-3f32-4099-8756-b19bd6a5d1e4" ></p>


* Attention value $a_t$를 단순하게 decoder의 t 시점의 hidden state $s_t$ 와 연결(concatenate)해줍니다. 
* 연결한 벡터를 $v_t$라고 가정하면, $v_t$는 기존의 Recurrent하게 얻은 decoder의 hidden state의 정보 외에도 encoder에서의 모든 hidden state를 고려한 정보 또한 포함
* sequence가 길어지더라도 정보를 크게 잃지 않는다.


#### Step 5. 출력층 연산 Input $\tilde{s_t}$ 계산
<p align="center"> <img src="https://github.com/user-attachments/assets/5775c800-d82d-4026-98a8-dc5cde10cafe" ></p>
위
연산에 대한 식은 아래와 같이 간단하게 나타낼 수 있습니다.

$\tilde{s_t}=tanh(W_c)[a_t;s_t]+b_c)$

$W_c$는 학습 가능한 가중치 행렬
$b_c$는 편향
$v_t=[a_t;s_t]의 형태
$;$는 concat을 나타냅니다.

최종 예측 $\hat{y_t}$
$\hat{y_t}=Softmax(W_y\tilde{s_t}+b_y)


<p align="center"> <img src="https://github.com/user-attachments/assets/7af2c05d-4f37-4ad2-89d9-70314bf71f3a" ></p>
