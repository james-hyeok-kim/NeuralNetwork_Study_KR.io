# Continuous Batching

## Batch
ML에서 Batch는 일반적으로 훈련 데이터를 나누는 단위

그러나, Inference 단계에서도 Batch라는 용어가 사용

모델이 동시에 처리 하는 입력 데이터의 묶음을 나타냄

여러 입력에 대한 예측을 동시에 수행

## LLM인퍼런스에서 Batching의 중요성

질문이 프롬프트에 들어오면 prefil phase에서 전체 attetnion matrix 계산

이후 답변을 위한 토큰 생성을 decode라고 하고 이때는 새로운 토큰에 대해서만 attention 계산

prefill phase는 입력값이 독립적이라 GPU의 병렬컴퓨팅 효율적으로 사용

### Static Batching

LLM에서 사용하는 전통적인 방식을 Static Batching이라고 명명

<img width="890" height="245" alt="image" src="https://github.com/user-attachments/assets/abe320e7-6f06-41ec-87bd-b5928ae4ef23" />

노란 프롬프트 토크들에서 하나의 파란 토큰을 생성

왼쪽 그림은 첫번째 이터레이션을 나타낸다.

여러 이터레인션이후 완료된 시퀀스들은 각기 다른 크기를 가진다.

요청이 배치처리에서 더 일찍 완료될 수 있지만 다른 시퀀스들에게 새 요청을 추가하기가 까다롭니다.

### 이를 해소하기 위해 Continuous Batching

<img width="905" height="252" alt="image" src="https://github.com/user-attachments/assets/3a90a651-4ac0-4c9b-a233-c0a0ec595645" />


### Continuouse Batching 지원하는 Framework
* vLLM, TGI(Text Generation Inference), Tensor-RT-LLM, Deepspeed-fastgen

