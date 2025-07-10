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
