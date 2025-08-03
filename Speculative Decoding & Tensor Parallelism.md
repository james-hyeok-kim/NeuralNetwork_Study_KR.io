### Speculative Decoding

#### 1. 모델 사이즈 고정 (3B 파라미터) → Hidden Dimension vs Sequence Length Trade-off

* Sequence Length $\leftrightarrow$ Hidden Dimension은 Trade off관계

* Hidden dim이 크면: 연산량이 증가하지만 한 번에 처리하는 token 수(=sequence length)는 메모리 상 한계로 줄어듦.

* Hidden dim이 작으면: 연산량은 줄지만 sequence length를 길게 가져갈 수 있음 (batching 효과).

#### 2. Tensor Parallelism 관점의 문제점

* Hidden dimension이 너무 작으면: GPU마다 분배할 계산량이 적어 "비효율적 (under-utilization)"이 발생.
* Hidden dimension이 너무 크면: 각 GPU에서 처리하는 tensor가 커져서 latency(한 token당 처리 시간)가 증가.

그리고 중요한 포인트:

* Speculative Decoding에서는 sequence length가 짧아지는 순간 Throughput이 급격히 떨어집니다.
* Sequence parallelism/Batching 효과를 잃기 때문.
* 특히 speculative는 draft에서 많은 token을 한꺼번에 predict하기 때문에 Draft 단계에서는 긴 sequence 길이가 필수적입니다.

#### 3. 결론: Hidden Dim & Sequence Length Scaling Trade-off
* Hidden dimension을 키우면 모델의 표현력(accuracy)은 좋아지지만,
* 그로 인해 sequence length(한번에 처리 가능한 token 수)가 짧아져서 batching 효과가 줄어듭니다.
* 특히 Tensor Parallelism에서는 Hidden dim이 너무 크면 GPU간 통신 병목이 커지고, 너무 작으면 GPU utilization이 떨어져서 속도가 나지 않습니다.

#### 4. Speculative Decoding에서의 추가 고려사항
* Draft 모델은 hidden dim이 작아도 길고 넓은 sequence를 빠르게 예측하는게 목적입니다. (acceptance rate만 충분하면 OK)

* Target 모델은 hidden dim이 커야 결과물 품질이 좋아집니다, 다만 Target은 Draft에서 검증할 때만 사용되기에 latency 영향은 제한적.

* Draft 모델 hidden dim이 커져서 sequence length가 짧아지면 speculative 효과가 줄고, 결국 speculative decoding의 효율이 망가집니다.
