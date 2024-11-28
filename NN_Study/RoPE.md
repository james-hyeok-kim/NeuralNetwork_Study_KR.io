# RoPE
* Rotary Position Embedding
* Transformer 모델은 본질적으로 순서에 무관, Position embedding통해 위치 정보를 보존
* 회전행렬(Rotation Matrix)를 통해 고유각도 부여
* Absolute Position 인코딩 가능
* Relative Position의 Dependency를 통합

<P align="center"> <img src="https://github.com/user-attachments/assets/b1034452-4d19-4bac-ad8f-7c422b49c577" weight="70%" height="70%"> </P>

## Formulation
### Position-Encoded Query and Key Vectors


<P align="center"> <img src="https://github.com/user-attachments/assets/fbdfb55d-2856-4a70-869c-66db0f3177f3" weight="40%" height="40%"> </P>
