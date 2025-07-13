## Paged Attention


KV cache의 특징으로는
* Large: 단적으로 LLaMA-13B를 예로 들었을 때 단일 시퀀스에 대해 최대 1.7GB를 차지함
* Dynamic: 시퀀스 길이에 따라 크기가 달라지며, 가변적이고 예측이 힘듬. 기존 시스템은 fragmentation과 over-reservation으로 인해 60~80%의 메모리를 낭비


<p align="center"> <img width="1280" height="612" alt="image" src="https://github.com/user-attachments/assets/f4f0bb53-7777-48b9-ba3e-14bd4e210e8a"> </p>

---

<p align="center"><img width="1280" height="613" alt="image" src="https://github.com/user-attachments/assets/ba88982f-20c6-4d9a-9fec-2846d3648d90"></p>

---


<p align="center"><img width="1280" height="614" alt="image" src="https://github.com/user-attachments/assets/fd4e4cc7-dbc5-4490-8526-fcb385842f22"></p>

---
<p align="center"><img width="1280" height="609" alt="image" src="https://github.com/user-attachments/assets/c3def813-0f21-49d1-9ce0-f641eaf9d0d4"></p>

---
<p align="center"><img width="1280" height="605" alt="image" src="https://github.com/user-attachments/assets/2a475386-b925-429c-8ffa-859e1bb4a962"></p>

---

PagedAttention에서 메모리 낭비는 시퀀스의 마지막 블록에서만 발생한다. 

그리고 실제 메모리 낭비는 4% 미만으로 거의 최적에 가까운 메모리 사용량을 얻을 수 있다. 

이러한 메모리 효율성 향상은 시스템에서 더 많은 batch 를 한번에 처리할 수 있다.

<img width="1280" height="513" alt="image" src="https://github.com/user-attachments/assets/62098025-54e2-4de8-a6c6-06699e1dfc71" />

