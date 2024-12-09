from(https://velog.io/@sjinu/%EA%B0%9C%EB%85%90%EC%A0%95%EB%A6%AC-Attention-Mechanism)
# Attention Mechanism

## Attention
* 어텐션의 기본 아이디어는 디코더에서 출력 단어를 예측하는 매 시점(step)마다, 인코더의 입력 시퀀스를 다시 참고

<p align="center">
<img src="https://github.com/user-attachments/assets/5add34e2-deea-4c74-94e7-d595e7766d6d" width="50%" height="50%">
</p>

* 어텐션 함수는 주어진 '쿼리(Query)'에 대해 모든 '키(Key)'의 유사도 
* 이 유사도를 키(Key)와 매핑되어 있는 각각의 '값(Value)'에 반영
* '유사도가 반영된' 값(Value)을 모두 더해서 리턴하고, 어텐션 값(Attention value)를 반환

### Dot-Product Attention
![image](https://github.com/user-attachments/assets/66891df0-2d55-489b-a1b0-30559790ccb4)

* Decoder의 세번째 LSTM Cell에서 출력 단어를 예측할 때 어텐션 메커니즘을 사용하는 예시
  - (왼쪽 오렌지색 encoder, 오른쪽 초록색 decoder)
![image](https://github.com/user-attachments/assets/9dccf543-31e7-4d7d-a0f4-fd92789211dd)

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
![image](https://github.com/user-attachments/assets/ce6e20c1-1da6-450e-aa89-2e1018d5021b)

$score(s_t ,h_i)=s_t^Th_i$
​이 때 결과 값은 scalar가 된다.

* decoder의 time step은 t인 반면, 참고하는 encoder의 time step은 1부터 N까지
* encoder의 모든 은닉 상태에 대한 decoder의 time step t에서의 Attention score를 계산하면 아래와 같이 나타낼 수 있습니다.

$e_t=[s_t^{T}h_1, ..., s_T^{T}h_N]$

#### Step 2. Attention Distribution
![image](https://github.com/user-attachments/assets/6b189a97-3aa8-4abf-8d96-9b9d77b1ad3c)

 * Attention scores $e_t$에 softmax(소프트맥스)함수를 적용해, 모든 값의 합이 1이 되는 확률 분포 Attention Distribution을 얻습니다.
 * 즉, 위의 그림에 있는 Attention Distribution을 얻기 위해 아래와 같은 식을 사용하면 됩니다.
   - $α_t =softmax(e_t)⋯ a_t$ 가 아닌 $α^t$입니다.
   - 이 때 각각의 값을 Attention Weight(어텐션 가중치)라고 합니다.
  
이에 대한 식은 아래와 같이 기술할 수 있습니다.

$a_t=\displaystyle\sum_{i=1}^Nα_i^th_i$
​
이러한 어텐션 값 $a_t$ 는 인코더의 맥락을 포함하고 있기 때문에 Context Vector(맥락 벡터) 라고도 불립니다
(정확히는, decoder 내 time step t의 context vector)

