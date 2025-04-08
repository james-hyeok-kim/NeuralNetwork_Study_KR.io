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


# Adaptive Layer Normalization (AdaLN)
## DiT Block with AdaLN-Zero 
$$ y = (\gamma * x) >> \beta $$

## AdaNorm $\Phi(y)$
1. $\Phi(y)$ 미분 가능
2. 평균 scaling wiehgt 고정(fixed)
3. z 평균 bounded(특정 범위)

$$ \Phi(y_i) = C(1-\frac{y_i}{10}) $$

$$ z=C(1-ky)\odot y, y=\frac{x-\mu}{\sigma}, \mu = \frac{1}{H} \displaystyle\sum^H_{i=1}x_i, \sigma = \sqrt{\frac{1}{H}\displaystyle\sum_{i=1}^{H}(x_i - \mu)^2}$$ 

* $C = hyper parameter, k = 1/10$


# Adaptive Instance Normalization
Content Input $x$, Style Input $y$
* Simply align the channel-wise mean and variance of x to match those of y
* AdaIN has no learnable affine parameters.

$$ AdaIN(x,y) = \sigma(y)\left(\frac{x-\mu(x)}{\sigma(x)}\right) + \mu(y) $$


# Normalization

* Training Parameter

$$ \hat{x_i} = \frac{x_i - \mu}{\sqrt{\sigma^2 + \epsilon}} $$ 
$$ y_i = \gamma\hat{x} + \beta $$
$$ \gamma = scale, \beta = shift, Both \ training \ parameter $$

* Variance

$$ \sigma^2 = \frac{1}{m}\displaystyle\sum_{i=1}^{m}(x_i-\mu)^2 $$  
$$ \sigma^2 = \frac{\sum(x^2)}{N} - \left(\frac{\sum(x)}{N}\right)^2 $$
