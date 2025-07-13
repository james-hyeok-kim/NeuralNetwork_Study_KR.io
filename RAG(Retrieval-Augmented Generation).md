# RAG

* RAG는 Retrieval-Augmented Generation의 줄임말로, 외부 지식을 검색(Retrieve)해서 LLM의 생성(Generation)에 보조 정보로 추가하는 방식입니다.

* RAG는 LLM이 학습하지 않은 정보에 대해 답변을 잘 못하는 문제를 해결하기 위해, 외부 데이터 소스를 활용하여 답변의 정확도를 높이는 기술

## 왜 필요한가?

기존 LLM (GPT, Claude, PaLM 등)은 고정된 파라미터 안에만 지식을 담고 있어서:

* 최신 정보 반영 불가

* 특정 도메인 지식 부족 (ex. 사내 문서, 기술 문서 등)

* 긴 문서 질의응답 시, context 제한 문제

→ 이런 문제를 해결하기 위해 RAG가 등장했습니다.

---

## 이점

### 비용 효율적인 구현
조직 또는 도메인별 정보를 위해 파운데이션 모델(FM)을 재교육하는 데 드는 계산 및 재정적 비용이 많이 듭니다.

### 최신 정보
RAG를 사용하여 LLM을 라이브 소셜 미디어 피드, 뉴스 사이트 또는 기타 자주 업데이트되는 정보 소스에 직접 연결할 수 있습니다.

### 사용자 신뢰 강화
RAG은 LLM은 소스의 저작자 표시를 통해 정확한 정보를 제공할 수 있습니다. 

### 개발자 제어 강화
개발자는 민감한 정보 검색을 다양한 인증 수준으로 제한하고 LLM이 적절한 응답을 생성하도록 할 수 있습니다.


## 작동 원리


<img width="898" height="532" alt="image" src="https://github.com/user-attachments/assets/6cba5aaf-c96f-47cd-90ba-fc9e230b5683" />

1. 외부 데이터 생성
* 생성형 AI 모델이 이해할 수 있는 지식 라이브러리를 생성
  
2. 관련 정보 검색
3. LLM 프롬프트 확장
4. 외부 데이터 업데이트


---

## RAG의 핵심 알고리즘 설명

### Dense Passage Retrieval (DPR)
* Dual Encoder 구조: DPR은 질문과 문서를 각각 독립적으로 인코딩하는 두 개의 BERT 모델을 사용합니다.
* 유사도 계산: 질문 벡터와 문서 벡터 간의 dot product를 계산하여 유사도를 측정합니다.
* 탑-K 문서 선택: 유사도 계산 결과를 바탕으로, 가장 관련성이 높은 상위 K개의 문서를 선택합니다.

### Sequence-to-Sequence 생성 모델
* BART/T5 모델: RAG는 BART 또는 T5와 같은 sequence-to-sequence 생성 모델을 사용합니다. 이 모델들은 입력 시퀀스를 받아 출력 시퀀스를 생성합니다.
* 문서 결합: 선택된 문서들을 입력으로 받아, 질문과 결합하여 답변을 생성합니다.


RAG 논문: Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks

[PDF](https://arxiv.org/abs/2005.11401)



## RAG 기술을 적용한 상용 서비스 사례
2024.05.07

* Microsoft Bing Search

2023년 2월, Microsoft는 Bing 검색 엔진에 RAG 기술을 적용한 대화형 AI 기능을 추가했습니다.

* Anthropic's Constitutional AI (CAI)

Anthropic사는 RAG 기술을 활용한 대화형 AI 모델인 CAI를 개발했습니다.

CAI는 대화 과정에서 외부 지식을 활용하여 사용자의 질문에 답변을 생성합니다.

생성된 응답의 근거가 되는 출처를 명시하여 신뢰성을 높였습니다.

* Perplexity AI

Perplexity AI는 RAG 기반의 질의응답 서비스를 제공하는 스타트업입니다.

* OpenAI's WebGPT (in development)

OpenAI는 GPT 모델에 RAG 기술을 적용한 WebGPT를 개발 중입니다.

WebGPT는 웹 검색을 통해 획득한 지식을 활용하여 사용자의 질의에 대한 응답을 생성할 것으로 예상됩니다.

아직 공개된 서비스는 아니지만, 향후 RAG 기술의 발전 방향을 보여주는 사례로 주목받고 있습니다.
