## In-Flight Batching

Adaptive batching의 경우, 아래 이미지와 같이 batch 하나에 대해서 longest 기준에 맞춰 padding을 해서 inference를 하게 됩니다.

<img width="1440" height="246" alt="image" src="https://github.com/user-attachments/assets/49a03bf1-3b2a-4a19-8d93-26e423ab2f00" />


Transformer의 encoder 기반 모델들은 한 번에 추론을 하기 때문에 저런 방식이 적합하지만, decoder 기반의 생성형 모델들의 경우에는 하나씩 생성하기 때문에 위의 방식이 비효율적이게 됩니다.

---


3개를 생성하면 input2에 대해서는 더 이상 생성할 필요가 없고, 5개를 생성하면 input3에 대해서는 생성할 필요가 없는 데 계속 의미 없이 inference를 하게 되는 것입니다.

<img width="1434" height="228" alt="image" src="https://github.com/user-attachments/assets/44adac1a-9919-4467-a35e-952af07f6189" />

---

이런 상황에서 의미 없는 inference 대신에, 새로운 input에 대한 생성을 할 수 있도록 구현한 기법이 바로 In-flight batching 입니다.

<img width="1446" height="234" alt="image" src="https://github.com/user-attachments/assets/2650d65f-8323-423c-8be2-b6fa77befdeb" />

---

위 그림과 같이, input2에 대한 inference가 끝나면 그 뒤에는 새로운 input4에 대한 생성을 시작하고, input3에 대한 inference가 끝나면 그 다음 새로운 input5에 대한 생성을 시작하여 GPU의 효율을 최대한으로 높이는 방식입니다.


---

## In-Flight Batching vs Continuous Batching

### ✅ 1. In-flight Batching

##### 정의:

여러 요청(request)이 동시에 들어오면, 

이미 실행 중인(in-flight) 상태에서도 가능한 요청들을 동적으로 모아서(batch) 처리하는 방식입니다.

##### 특징:

서버는 각 요청을 기다리지 않고, 가능한 시점에 도착한 요청들을 모아 연산합니다.

실행 도중에도(batch 중에도) 요청을 추가로 받아들일 수 있는 구조여야 합니다.

##### 예: 

vLLM의 speculative decoding과 함께 쓰이는 in-flight batching.

### ✅ 2. Continuous Batching

##### 정의:

하나의 요청 처리 후 다음 요청을 기다리는 게 아니라, 

서버가 지속적으로(batch window 없이) 들어오는 요청을 바로바로 모아 연산을 이어가는 구조입니다.

##### 특징:

기존 static batching과 달리, batching 시점이 고정되어 있지 않고, 연속적으로 입력되는 요청을 처리합니다.

대기 시간(latency)을 줄이면서도 throughput을 높이기 위한 전략입니다.

#### 🔄 공통점과 차이점 요약
|항목	|In-flight Batching|Continuous Batching|
|:---:|:---:|:---:|
|동작 시점|	실행 중에도 batch 확장 가능|	요청을 연속적으로 batch 처리|
|목적	|GPU 활용도 극대화, latency 감소|	latency, throughput 동시 최적화|
|대표 시스템	|vLLM, TRT-LLM|	vLLM, TensorRT-LLM 등|
|차이점	실행| "중간에" 요청 추가 가능	실행| "사이사이"마다 batching 지속|

#### ✅ 결론
in-flight batching은 continuous batching의 한 형태라고 볼 수 있습니다.

다만, in-flight batching은 특히 실행 중 요청 추가 처리에 초점을 둔 기술이고,

continuous batching은 일반적인 연속적인 batching 전략을 포괄하는 개념입니다.
