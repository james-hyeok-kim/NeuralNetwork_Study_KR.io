## FiLM (Feature-wise Linear Modulation)
* [Paper](https://arxiv.org/pdf/1709.07871)
* 신경망 중간 특징을 조건정보를 기반으로 선형 변환(Affine Transformation)
* 신경망 계산의 조건화 방법 (Conditioning)

$$ FiLM(h, \gamma, \beta) = \gamma \odot h + \beta $$
* Terminology
  * $\odot$ = element wise multiplication
  * $\gamma$ = scaling factor
  * $\beta$ = shifting factor
 
* 장점
  * 시각적 추론 : 이미지관련 태스크에서 질문에 따른 변조 가능
  * 이미지 생성 : 특정 스타일에 맞춰 이미지 생성 가능
  * 도메인 적응 : 다른 도메인의 데이터에 모델을 적응시킬때, 도메인 정보를 학습
  * 강화학습 : 에이전트의 행동을 환경상태나 보상함수에 따라 조건화


![image](https://github.com/user-attachments/assets/7633e05d-a3cf-4d6d-9d72-7907c2a98475)
