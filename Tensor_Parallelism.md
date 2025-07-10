[분산 처리 3 - Pipeline Parallelism과 Tensor Parallelism에 관하여](https://yjoonjang.medium.com/%EB%B6%84%EC%82%B0-%EC%B2%98%EB%A6%AC-3-pipeline-parallelism%EA%B3%BC-tensor-parallelism%EC%97%90-%EA%B4%80%ED%95%98%EC%97%AC-7b4420fe0281)

## Tensor Parallelism

<p align="center"><img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/03b4b002-e64b-4883-b878-4492ed40b8ee" />


Tensor Parallelis (TP)에 관해 살펴보자.

TP는 쉽게 말해 모델 자체 (전체 파라미터)를 여러 개의 부분으로 쪼개어 서로 다른 GPU에 올리는 방식이라고 볼 수 있다.

<p align="center"><img width="700" height="304" alt="image" src="https://github.com/user-attachments/assets/5f061b12-e75e-4a64-9c68-f5f26b0b5e15" />


Layer별로 나누어 GPU에 올리는 PP와는 완전히 다른 방식이다. PP는 세로로 자르는 방식이라면 TP는 가로로 자르는 방식..?!

이를 처음 구현한 것은 Nvidia의 Megatron-LM 프레임워크이다.

## **2–1. Column, Row Parallelism**

Megatron-LM에서는 모델의 파라미터를 어떻게 자르는지를 기준으로 parallelism을 적용하는데, 자르는 방법은 column, row의 2가지이다.

**1. Column Parallelism**

<p align="center"><img width="700" height="338" alt="image" src="https://github.com/user-attachments/assets/416074ae-46bb-4fe4-a572-322012d4cf80" />


위의 그림과 같이, 파라미터를 column으로 자르는 경우, 모델 파라미터에 해당하는 tensor를 column 기준으로(세로로) 잘라 각각 다른 GPU에 올리고, 같은 input 를 복사 (broadcast)하여 각각의 GPU마다 올라간 파라미터와 계산한다. 이후, cat (all-gather)을 통해 최종 계산 결과를 얻는다.

**2. Row Parallelism**

<p align="center"><img width="700" height="333" alt="image" src="https://github.com/user-attachments/assets/74163a79-0b93-4f3b-935a-540fa3d9d83f" />


반대로 파라미터를 row로 잘라 계산하는 경우, 각각 분할된 파라미터를 서로 다른 GPU에 올려 두고, input을 scatter할 때 input을 column으로 잘라서 scatter한다. 이후 결과물들의 element-wise 덧셈을 통해 최종 산출물을 얻을 수 있다.

<p align="center"><img width="700" height="569" alt="image" src="https://github.com/user-attachments/assets/96e26631-3e58-4ac0-b071-fdb2fd8d8064" />


X는 input, A는 모델 파라미터이다.

Column Parallelism을 쓰던지, Row Parallelism을 쓰던지, 최종 계산 결과물은 같다.

## **2–2. Transformer block Parallelism**

그럼 이 parallelism을 transformer 아키텍쳐에 대해서는 어떻게 적용할 수 있을까.

<p align="center"><img width="700" height="252" alt="image" src="https://github.com/user-attachments/assets/a31274c6-b9d1-4e2c-914e-e73ff8e235c3" />


Megatron-LM에서는 transformer 구조에서 Layer Norm 레이어를 제외한 다른 모든 레이어들(Attention, MLP)에 Column, Row Parallelism을 적용한다.

(Layer Norm 레이어에 적용하지 않는 이유는 파라미터 수가 적기 때문이다.)

그럼 이 transformer 아키텍쳐에 parallelism을 적용한 방법에 대해서 알아보자.

**2–2–1. MLP Layer**

위 그림의 오른쪽과 같이 ***Linear -> GeLU -> Linear -> Dropout*** 순으로 진행되는 것이 MLP Layer이다.

<p align="center"><img width="700" height="362" alt="image" src="https://github.com/user-attachments/assets/eee5013a-871f-4f9c-ba2b-0504c8f8795c" />


이때 2개의 Linear layer는 각각 column, row parallelism이 적용된다. 즉 앞의 linear layer는 column parallelism, 뒤의 linear layer는 row parallelism이 적용되는 것이다.

<p align="center"><img width="700" height="206" alt="image" src="https://github.com/user-attachments/assets/f016e8d5-933e-41dd-b08f-957c10cbaa97" />


column parallelism -> gather -> scatter -> row parallelism

왜 굳이 이 순서대로 적용이 될까? 이유는 2가지가 있다.

> a. All-gather과 scatter 통신 프로세스의 생략
> 

<p align="center"><img width="700" height="206" alt="image" src="https://github.com/user-attachments/assets/a9cc0480-7a39-4822-80c4-1d1788f7d8e2" />


위의 그림은 2개의 linear layer에 column-row 순서로 parallelism이 적용되었을 때의 시나리오를 보여 준다. 그림을 보면, column parallelism의 출력이 나온 후 (초록색), all-gather하여 하나의 tensor로 합치고 (빨간색), 다시 scatter하는 것 (초록색)을 볼 수 있다. 그럼 의문이 든다. **굳이 왜 합쳤다가 다시 나누지 ??**

column parallelism의 출력이나 (XW), row parallelism의 입력이나 (Y1, Y2) 생긴게 아예 똑같다. 즉, gather 후 다시 scatter하는 것을 하지 않아도 되는 것이다.

*그래서 결론적으로는 이렇게 column-row 순서로 parallelism을 적용한 경우에만 gather, scatter 연산을 생략할 수 있다.*

<p align="center"><img width="700" height="206" alt="image" src="https://github.com/user-attachments/assets/f5c0e8f4-162d-4d4c-be1e-b461844122ce" />


column-row parallelism 적용 시 중간의 gather, scatter 과정을 생략할 수 있다.

> b. GeLU 연산을 고려한 병렬화
> 

앞서 말했듯이, MLP layer에서 벌어지는 상황은

*input -> linear layer -> GeLU -> linear layer -> Dropout* 이다.

지금 우리는 GeLU 좌우에 있는 linear layer에 대해 어떻게 parallelism을 적용할 지 살펴본 것이고, 실제로 재대로 된 시나리오는 아래와 같다.

<p align="center"><img width="700" height="180" alt="image" src="https://github.com/user-attachments/assets/0fb16cea-ad39-4e80-bff1-060cfcc4ff61" />


두 개의 linear layer 사이에 GeLU 함수가 끼어 있는 모습

이렇듯 막무가내로 scatter, gather 연산을 생략할 수는 없고, GeLU 연산도 병렬화되어 제대로 수행될 수 있는지 확인해보아야 한다.

즉 scatter, gather 연산을 제거했을 때도 아래와 같이 되어야 하는 것이다.

<p align="center"><img width="700" height="177" alt="image" src="https://github.com/user-attachments/assets/9739269c-69fc-4063-824b-f52c16998198" />


GeLU도 분할되어 수행되는 모습

앞서 우리는 row, column parallelism에 대해서 알아보았다. 그때 row parallelism은 두 텐서를 element-wise하게 더함으로써, column parallelism은 두 텐서를 concatenate 함으로써 최종 출력물을 얻는다는 것을 알게 되었다. 즉 GeLU를 낀 채로 **row parallelism**이 가능하게끔 하기 위해서는 아래 식이 성립해야 하고,

GeLU(XW1 + XW2) = GeLU(XW1) + GeLU(XW2)

GeLU를 낀 채로 **column parallelism**이 가능하게끔 하기 위해서는 아래 식이 성립해야 한다는 것이다.

*GeLU(XW1 ⊚ XW2) = GeLU(XW1) ⊚ GeLU(XW2)*

그러나, GeLU는 비선형 함수이기 때문에 row parallelism에 해당하는 식은 성립하지 않고, column parallelism에 해당하는 식은 성립한다:

GeLU(XW1 + XW2) ≠ GeLU(XW1) + GeLU(XW2),

GeLU(XW1⊚XW2) = GeLU(XW1)⊚GeLU(XW2)

따라서 column parallelism이 수행된 후에 GeLU 연산이 수행되어야만 reduce, scatter 프로세스를 생략할 수 있고, 이것이 우리가 column-row 순서로 parallelism을 적용해야 하는 이유이다.

**2–2-2. Multi-head Attention Layer (Self-Attention Layer)**

이제 MLP layer에 대해 알아보았으니, multi-head attention layer (논문에서는 self-attention layer라고 한다.)에 parallelism을 어떻게 적용하는지에 대해 알아보자.

<p align="center"><img width="437" height="378" alt="image" src="https://github.com/user-attachments/assets/543751a6-ed3f-41be-a4e0-cc95114b825f" />


Multi-head attention layer에서는 *Input -> Linear1 -> Split heads -> Scaled Dot-Product Attention -> Concat -> Linear2* 로 진행된다.

<p align="center"><img width="700" height="381" alt="image" src="https://github.com/user-attachments/assets/30f415b3-ceb7-4639-9f17-8877e62f5419" />

Multi-head attention에서도 MLP layer와 마찬가지로 두 개의 linear layer에 대해서 parallelism을 적용한다. 이전과 마찬가지로 선순위에 있는 Q, K, V linear projection에는 column parallelism을, 마지막 output projection 부분에는 row parallelism을 적용한다. 이렇게 다시 column-row parallelism을 만들면서 gather, scatter 프로세스를 없앨 수 있다.

**2–2–3. Vocab parallel embedding**

마지막으로 vocab에 대해서도 분산 처리를 진행하는데, 신기하게도 vocab size를 기준으로 반으로 나눈다.

<p align="center"><img width="700" height="396" alt="image" src="https://github.com/user-attachments/assets/0d6956a1-1cb1-427d-b127-be82a7e0d310" />



이렇게 50,000 크기의 vocab이 있다고 했을 때, seq_len이 6인 input이 들어온다고 가정하자. 그럼 보통의 경우 위의 그림처럼 각 단어의 임베딩을 따와 [6, embed_dim] 크기의 tensor로 변환된다.

<p align="center"><img width="700" height="312" alt="image" src="https://github.com/user-attachments/assets/7f883c65-2001-4ef2-a1ba-153e2810db68" />


그러나 vocab parallel embedding을 적용하면 먼저 전체 vocab을 각 GPU에 나눠서 할당한다. 즉, vocab을 GPU의 개수만큼 분할하여, 각 GPU는 전체 vocab 중 일부분만 담당하게 된다.

이후 입력 단어에 대해, 각 GPU는 자신이 담당하는 단어에만 임베딩을 계산하고, 다른 GPU에서 담당하는 단어들은 처리하지 않기 때문에 **masking** 처리가 된다. 이때 scatter 프로세스를 통해 각 GPU가 맡은 단어들만 적절하게 할당된다.

마지막으로, 각 GPU에서 계산된 임베딩을 다시 gather하여 최종 임베딩을 완성한다.

방금까지는 input이 들어와 parallelism으로 처리되는 임베딩에 대해 알아 보았으니, 이제는 output이 어떻게 처리되는지 알아보자.

**2–2–4. Vocab parallel cross entropy**

<p align="center"><img width="700" height="183" alt="image" src="https://github.com/user-attachments/assets/f14b10d1-87b8-408b-8152-3a88c8d5e538" />


분산 처리를 하지 않는 일반적인 상황에서는 위와 같이 (seq_len, vocab_size) 크기의 output tensor가 나오고, 그것을 target과 비교하여 cross-entropy loss를 계산한다.

그러나 앞서 말했듯이, Megatron-LM은 vocab parallel embedding을 사용하기 때문에, vocab_size / GPU 개수의 크기의 vocab들이 GPU에 나누어져 들어가 있다. 즉, 위의 사진처럼 logits가 다 찬 것이 아니고, 서로 다른 GPU에 분포되어 있는 것이다. (아래 사진 참고)

<p align="center"><img width="700" height="468" alt="image" src="https://github.com/user-attachments/assets/967bd231-be72-4e4e-9078-3f8e046b5ab1" />


GPU 개수가 2개인 상황

그럼 이런 경우에는 어떻게 해야할까? 답은 생각보다 간단하다. Masking되어 나온 부분에 대해서는 target도 똑같이 masking 시켜 loss를 계산하지 않고, 병렬적으로 계산한 후 모아주는 것이다. 그림으로 보면 다음과 같다.

<p align="center"><img width="700" height="230" alt="image" src="https://github.com/user-attachments/assets/f2be38e9-295d-47fb-a6c3-f3b0e856dd85" />


이를 **vocab parallel cross entropy** 라고 한다.

이렇게 vocab embedding, vocab에 대한 cross entropy loss까지 분산처리를 함으로써 메모리 최적화를 확실하게 한다.
