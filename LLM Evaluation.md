### LLM 평가
* OpenAI Eval 라이브러리
    * HellaSwag, TrughfulQA, MMLU
* Open-Ko-LLM 리더보드
    * ARC, HellaSwag, MMLU, TruthfulQA, Text Generation

#### 1. Coding Task
1-1. HumanEval : LLM Benchmark for Code Generation
* OpenAI가 공개한 HumanEval Data Set에는 signature, docstring, body, several unit test들이 포함된 164개의 프로그래밍 문제가 포함되어 있습니다.
* 코드 생성 모델의 traning set에 포함되지 않게 하기 위해 handwritten되었습니다.

1-2. MBPP(Mostly Basic Python Progamming)
* MBPP 벤치마크는 프로그래밍의 기초, 표준 라이브러리 기능 등을 다루는 보급형 프로그래머가 해결할 수 있도록 설계된 약 1,000개의 클라우드 소스 파이썬 프로그래밍 문제로 구성되어 있습니다.
* 각 문제는 작업 설명, 코드 솔루션 및 3개의 자동화 된 테스트 사례로 구성됩니다.

#### 2. Chatbot Assistance
2-1. Chatbot Arena
* LMSYS 챗봇 아레나는 LLM Evals를 위한 클라우드소싱 오픈 플랫폼입니다.
* Bradley-Terry model로 LLM의 순위를 정하고 모델 등급을 Elo-scale로 표시하기 위해 800,000개 이상의 human pairwise 비교를 수집했습니다.

<img width="1512" height="1237" alt="image" src="https://github.com/user-attachments/assets/880e760c-539f-4942-bf66-b75de9c8a5a1" />

2-2. MT Bench
* MT Bench 데이터 셋트에는 80개의 질문에 대한 응답으로 6개 모델이 생성한 응답에 대한 3.3k 전문가 수준의 인간 선호도가 포함되어 있습니다.
* 6개 모델은 GPT-4, GPT-3.5, Claud-V1, Vicuna-13B, Alpaca-13B 및 LLaMA-13B입니다.
<img width="1663" height="1613" alt="image" src="https://github.com/user-attachments/assets/7cb24c85-91fa-4d7c-ad85-ae1384be7342" />

#### 3. Reasoning(추론)
3.1 ARC Benchmark : Evaluation LLM's Reasoning Abilities 
* ARC(AI2 Ressoning Challenge)는 강력한 지식과 추론을 요구하는 테스트이며, 질문 세트에서 Challenge set과 esay set으로 구분해 볼 수 있습니다.
* Challenge set은 검색 기반 알고리즘(retrieval-based algorithm)과 단어 동시 발생 알고리즘(word-co-occurrence algorithm)에 의해 잘못 답변된 질문만 포함하고 있습니다.
* ARC 데이터 셋은 3~9학년 수준의 과학 문항 7,787개로 구성되어 있으며, 다양한 유형(공간, 실험, 대수, 프로세스, 사실, 구조, 정의 및 목적 : spatial, experimental, algebraic, process, factual, structural, definition, purpose)을 포함하고 있습니다.

 <img width="586" height="438" alt="image" src="https://github.com/user-attachments/assets/34c39454-bebd-45e9-86cc-98a1c7b35bec" />


3.2 HellaSwag : Understanding the LLM Bechmark for Commonsence Reasoning
* 미완성된 구절을 LLM으로 하여금 완성하게 합니다. 문맥에 따라 테스트를 이해하고 예측하는 능력을 테스트함으로써 상식적인 추론을 평가합니다.
* 데이터 세트를 생성하고 복잡성을 높이는 AF(Adversarial Filtering)을 통해 데이터 세트를 구성하며, AF를 사용하여 벤치마키 데이터 세트의 편향 및 아티팩트 문제를 해결하였습니다.
<img width="762" height="282" alt="image" src="https://github.com/user-attachments/assets/b0cc0aba-faf1-4d81-96ae-be3b8ef65852" />


3.3 MMLU(Measuring Massive Multitask Language Understanding) : Better Benchmarking for LLM Language Understanding
* 모델의 멀티태스킹 정확도를 측정하는 테스트이며, 초등 수학, 미국 역사, 컴퓨터 과학, 법학 등을 포함한 57개 과제를 초등부터 고급 전문가 수준까지 다양한 수준에서 수행합니다.
* 이 테스트에서 높은 정확도를 얻으려면 모델은 광범위한 세계 지식과 문제 해결 능력을 보유하고 있습니다.

<img width="1224" height="584" alt="image" src="https://github.com/user-attachments/assets/074cbfe1-7f81-4bf7-85f6-65cecb3534a1" />

<img width="842" height="332" alt="image" src="https://github.com/user-attachments/assets/f5e64997-2f99-4706-8a27-67df8780afb3" />

3.4 TriviaQA
* 언어 모델이 질문에 대한 답변을 생성하는데 진실한지 여부를 측정합니다. 데이터 세트의 질문은 인간이 잘못된 믿음이나 오해를 가지고 있기 때문에 오답을 줄 수 있으며, 오답 생성을 피하도록 구성해야 합니다.
* 이 벤치마크는 건강, 법률, 금융 및 정치를 포함한 38개 항목에 걸쳐 817개의 질문으로 구성되어 있습니다.

<img width="550" height="508" alt="image" src="https://github.com/user-attachments/assets/25cafea5-e34c-495e-9728-7f5fca79a0c9" />


3.5 WinoGrande
* 자연어 처리를 기반으로 문맥을 올바르게 파악하는 LLM의 능력을 테스트하기 위해 두 개의 가능한 답이 있는 거의 동일한 문장 쌍으로 구성이 되어 있습니다.
* 44K의 대규모 문제로 구성하여 데이터셋을 개선하였으며, 문장이 주어졌을 때, 뒤를 완성하는 방식으로 상식적 추론을 테스트 합니다.

<img width="906" height="242" alt="image" src="https://github.com/user-attachments/assets/53530a15-3ca9-446f-9e80-47957ef96635" />

3.6 GSM8k

* GSM8k는 기본적인 수학 연산을 사용하여 다단계 수학 문제를 해결하는 능력을 테스트합니다.
* 2단계부터 8단계까지 풀어야 하는 초등학교 수준의 수학 문제를 통해 모델의 수학적 추론 및 문제 해결 능력을 측정 합니다.

<img width="848" height="450" alt="image" src="https://github.com/user-attachments/assets/49365031-e222-4dd3-8039-dd6ecef7c5d4" />


### LLM 기반 시스템 평가
<img width="1315" height="623" alt="image" src="https://github.com/user-attachments/assets/0ed2ba75-7807-4ad3-a30c-5aa43ebfe8ef" />

* LLM 시스템 평가는 시스템에서 제어할 수 있는 구성 요소들을 각각 평가하는 것을 의미합니다.
* 그림에서 보여지 듯 하나의 모델에 입력 프롬프트 및 프롬프트 템플릿 등을 변경해 가면서 시스템의 성능을 평가하는 것입니다.
* LLM 시스템 평가 지표로는 Extracting structured information, Question answering, Retrieval Augmented Generation 등을 사용합니다.

|Type	|Description	|Example Metrics|
|---|------|------|
|Diversity|Examines the versatility of foundation models in responding to different types of queries|Fluency, Perplexity, ROUGE scores|
|User Feedback	|Goes beyond accuracy to look at response quality in terms of coherence and usefulness	|Coherence, Quality, Relevance|
|Ground Truth-Based Metrics|	Compares a RAG system's responses to a set of predefined, correct answers	|Accuracy, F1 score, Precision, Recall|
|Answer Relevance	|How relevant the LLM's response is to a given user's query	|Binary classification (Relevant/Irrelevant)|
|QA Correctness	|Based on retrieved data, is an answer to a question correct?	|Binary classification (Correct/Incorrect)|
|Hallucinations	|Looking at LLM hallucinations with regard to retrieved context	|Binary classification (Factual/Hallucinated)|
|Toxicity	|Are responses racist, biased or toxic?	|Disparaity Analysis, Fairness Scoring, Binary classification (Non-Toxic/Toxic)|
