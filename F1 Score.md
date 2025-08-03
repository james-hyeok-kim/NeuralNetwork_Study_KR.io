### F1 Score
* 분류 모델의 precision 과 Recall성능을 동시에 고려
* Precision과 Recall의 조화평균, 0~1사이
<img width="326" height="88" alt="image" src="https://github.com/user-attachments/assets/f70e3d23-2b55-42ec-8a63-dd6eea96d2bd" />

#### Accuracy (정확도)
<img width="721" height="471" alt="image" src="https://github.com/user-attachments/assets/92b8d914-c85a-43ef-ad8e-fad1cb30e313" />

#### Precision (정밀도)
<img width="720" height="466" alt="image" src="https://github.com/user-attachments/assets/f3c90957-9f6a-481c-8cfb-09657069a256" />

#### Recall (재현율)
<img width="714" height="472" alt="image" src="https://github.com/user-attachments/assets/9bb82251-8ed4-4962-a7b4-1e232b7c85f1" />

#### Precision-Recall Curve
* Precision과 Recall은 Trade-off관계
* Decision Threshod를 통해 조절

<img width="504" height="194" alt="image" src="https://github.com/user-attachments/assets/df335ef9-a008-4583-a7a4-c27acc8ca0a8" />

<img width="1280" height="478" alt="image" src="https://github.com/user-attachments/assets/d47b946d-28a2-4af2-8a2a-387f58783f44" />

#### AP (Average Precision)
* Precision-Recall Curve에서 그래프 아래쪽 면적
<img width="206" height="97" alt="image" src="https://github.com/user-attachments/assets/f557ff82-10ba-4e14-83e0-06e9f2658f1a" />

##### TNR (True Negative Rate)
* 실제 Negative 샘플 중 분류 모델이 Negative로 판정한 비율
<img width="139" height="50" alt="image" src="https://github.com/user-attachments/assets/a749c705-1a1a-43d6-a5bf-6181f1cb6c14" />

##### FPR (False Positive Rate)
* 실제 Negtaive 샘플 중 분류 모델이 Positive로 판정한 비율

<img width="240" height="37" alt="image" src="https://github.com/user-attachments/assets/0c803564-69d2-4585-9149-fc8338f32adf" />

##### ROC Curve
* ROC (Receiver Operating Characteristic) Curve
* Threshold에 따른 TPR(Recall)과 FPR(Fall Out)을 나타낸 그래프
* 좌성단으로 붙어있을 수록 좋은 분류 성능
<img width="1024" height="1024" alt="image" src="https://github.com/user-attachments/assets/c3d48318-d955-4467-9b7e-c10e2be05999" />

##### ROC vs PRC
* ROC curve는 불균형한 데이터에서 모델 성능 저항을 반영하기 힘듦
* ROC는 데이터셋 고려하지 않는 일반적은 상황에서 유리
* Precision-Recall Curve는 불균현 심한 데이터 셋에서 성능 분석할때 유리
