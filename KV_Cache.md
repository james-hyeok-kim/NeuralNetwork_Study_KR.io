## KV Cache

<p align="center">
<img width="1280" height="720" alt="image" src="https://github.com/user-attachments/assets/e5486ae6-d55b-4a68-a2d2-7ad4abb1a309" />
</p>

---

<p align="center">
<img width="1280" height="713" alt="image" src="https://github.com/user-attachments/assets/283c77ea-2a93-45f0-b89c-421d5ffb2829" />
</p>

---

<p align="center">
<img width="1280" height="707" alt="image" src="https://github.com/user-attachments/assets/16379e4b-07eb-41e1-b82c-5713cce119c8" />
</p>

---

<p align="center">
<img width="1280" height="714" alt="image" src="https://github.com/user-attachments/assets/99ca97d4-e270-4350-ac40-76e87cb63ff8" />
</p>

---


KV caching 을 사용하면 행렬곱셈은 훨신 빨라지지만 행렬 state 를 저장하기 때문에 많은 GPU VRAM 이 필요하다는 단점이 있다. 또한 context length 와 batch size 가 증가할수록 KV cache 요구량이 매우 증가한다.

kv cache 의 특징

- Large : 1개 sequence 에 대해 LLaMA-13B 모델에서 최대 1.7GB 를 잡아먹는다.
- Dynamic : sequence length 에 의존하기 때문에 매우 동적이고 예측하기 쉽지 않다. 그러므로 kv cache 를 효율적으로 관리하는 것은 어려운 문제이다.

기존 시스템은 fragmentation 와 over-reservation로 인해 60% – 80% 의 메모리 낭비를 일으키는 것으로 확인되었다.

이 문제를 해결하기 위해 vllm 에서는 PagedAttention을 사용하였다.
