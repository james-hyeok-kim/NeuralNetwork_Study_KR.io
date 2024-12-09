# Normalization Techniques 

## Batch & Channel & Layer Normalization
<P align="center">
<img src="https://github.com/user-attachments/assets/cd06d0d0-44ab-4d6c-b1e3-9fdf046b4e83" width="60%" height="60%">
</P>

$$y = \frac{(x - mean)}{\sqrt{(var + eps)}}$$

## RMS Normalization
* Root Mean Square Layer Normalization

$$y=\frac{x}{\sqrt{(mean(x²)+ε)}}$$


|구분|Layer Normalization|RMSNorm|
|:---:|:---:|:---:|
|정규화 방식|	평균과 분산을 사용하여 정규화|	제곱평균을 사용하여 정규화|
|계산 비용|	평균과 분산 계산,비용이 더 높음|	제곱평균 계산만, 비용이 더 낮음|
|파라미터|	𝛾 (스케일 파라미터), 𝛽 (시프트 파라미터)|	𝛾 (스케일 파라미터)|
|배치| 민감도	민감하지 않음|	민감하지 않음|
|주 사용 사례|	RNN과 같은 순환 신경망	|대규모 신경망, 계산 비용이 낮은 모델 (transformer)|
|장점|	각 샘플의 평균과 분산을 고려, 정규화, 안정적인 학습 가능|	계산 비용이 낮고 간단한 계산 정규화|
|단점|	평균과 분산 계산으로 인해 계산 비용이 높음|	평균을 고려하지 않기 때문에 일부 경우에서 학습 안정성 떨어질 수 있음|

# Standardization Techniques


## Data standardization


## Z-score standardization
