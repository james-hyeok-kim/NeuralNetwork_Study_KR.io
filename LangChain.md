# LangChain
## 정의

* LangChain은 **대규모 언어 모델(LLM)**을 실제 애플리케이션에 쉽게 통합하고 구성할 수 있도록 도와주는 Python 기반 오픈소스 프레임워크

* 기존의 언어 모델이 주로 텍스트 생성에 중점을 둔 반면, LangChain은 다양한 외부 데이터 소스와 통합하여 보다 복잡하고 유용한 애플리케이션을 만들 수 있도록 설계

* Chain을 사용하면 여러 구성 요소를 결합하여 하나의 종합적인 애플리케이션을 만들 수 있다. 여러 개의 체인을 결합하거나, 체인을 다른 구성요소와 결합하여 더 복잡한 체인을 만들 수도 있다.


<img width="1579" height="606" alt="image" src="https://github.com/user-attachments/assets/fb46cc54-6fd2-4ef1-a4e0-49d16949da59" />


## EcoSystem

<img width="1246" height="1273" alt="image" src="https://github.com/user-attachments/assets/d8eec0bd-8330-42b2-89bf-eb1a16c7be3e" />

LangChain Framwork는 여러 에코시스템으로 이루어져 있는데, 최근에는 개발과 운영, 그리고 배포까지 지원하는 Framwork로 계속 진화중인 것으로 보인다.

* LangChain 라이브러리 : Python 및 JavaScript 라이브러리. 여러 구성 요소를 체인 및 Agent로 결합하기 위한 기본 런타임, 체인 및 Agent의 기성 구현이 포함
* LangChain 템플릿 : 다양한 작업을 위해 쉽게 배포할 수 있는 참조 아키텍처 모음
* LangServe : LangChain 체인을 REST API 배포를 지원하는 라이브러리
* LangSmith : LLM Framwork에 구축된 체인을 디버그, 테스트, 평가 및 모니터링하고 LangChain과 원활하게 통합할 수 있는 플랫폼

## 언제 유용할까?

* 대화형 에이전트 구축 (챗봇, 고객지원)
* 검색 + 요약 기능
* 문서 기반 질의응답 (RAG)
* LLM으로 워크플로우 자동화
* LLM과 외부 API/DB 연동 등

## 예시
```python
from langchain.agents import initialize_agent
from langchain.agents.tools import Tool
from langchain.llms import OpenAI
from langchain.utilities import SerpAPIWrapper

# 검색 도구 준비
search = SerpAPIWrapper()
tools = [Tool(name="Search", func=search.run, description="웹에서 정보를 검색함")]

# LLM + Agent 구성
llm = OpenAI(temperature=0)
agent = initialize_agent(tools, llm, agent_type="zero-shot-react-description", verbose=True)

# 실행
agent.run("LangChain이 무엇인지 알려줘")

```

---

## LangChain 시작하기

LangChain 설치
Windows에서 비교적 가볍게 LangChain을 시작해 볼 수 있습니다. LangChain을 시작하기 위해서는 Python 설치가 필요합니다. 아래 단계는 Windows PowerShell을 사용하여 진행됩니다.

(1) Python 설치
먼저 Python 공식 웹사이트에서 Python 3.8 이상 버전을 다운로드하여 설치합니다. 설치 시 "Add Python to PATH" 옵션을 선택합니다.

(2) LangChain 설치
Windows PowerShell 명령 프롬프트 창에서 다음 명령어를 입력하여 LangChain을 설치합니다. 이렇게 하면 LangChain의 최소 요구사항이 설치됩니다. 특정 통합을 위해서는 별도로 라이브러리 패키지를 설치해야 합니다.

```python
pip install langchain
```

(3) 서드파티 설치: OpenAI
OpenAI에서 제공하는 LLM을 사용한다고 가정하면, 다음 명령어를 입력하여 OpenAI 라이브러리를 설치합니다. 다른 업체에서 제공하는 라이브러리는 아래 표를 참조해 주세요.

```python
pip install langchain-openai
```


LangChain 서드파티 라이브러리를 보여주는 테이블

|제공업체|라이브러리명|
|:---:|:---:|
|Anthropic LLM|langchain-anthropic|
|AWS Bedrock LLM|langchain-aws|
|Cohere LLM|langchain-cohere|
|Fireworks LLM|langchain-fireworks|
|Google LLM|Google Generative AI : langchain-google-genai|
|          |Vertex AI : langchain-google-vertexai|
|Hugging Face LLM|langchain-huggingface|
|IBM LLM|langchain-ibm|
|Microsoft AzureOpenAI LLM|langchain-openai|
|Ollama LLM|langchain-ollama|
|OpenAI LLM|langchain-openai|


---

## Agent

* 만약 최신 날짜를 기준으로 정보를 알고 싶다면 어떻게 해야할까(최신 기사, 최신 동향 등) 언어 모델(LLM)은 특정한 날짜를 기준으로 훈련을 받음, 이러한 종류의 task에는 최신 데이터가 언어 모델(LLM)의 훈련 셋에 포함되어 있지 않았었기 때문에, 다시 말해 배운적이 없기 때문에 답변이 불가능하다.

* 그렇다면 언어 모델(LLM)이 최신 데이터를 필요로 하는 작업을 사용자는 요청할 수 없는걸까? 답은 그렇지 않다! Agent를 통해 이러한 작업을 요청 가능하게 해줄 수 있다.

<img width="2187" height="1137" alt="image" src="https://github.com/user-attachments/assets/b6157ccd-a102-4555-96e6-8de29f6b5d01" />


주요 개념 Agent 와 동작원리
* Agent는 사용자를 대신해 작업을 수행하고 언어 모델(LLM)과 소통하는 역할
* 과제를 완료하기 위해 큰 작업을 어떤 하위 작업을 세분화하고 수행해야할지 계산
* Chain-of-Thought(CoT) 생각의 사슬, 프롬프트 엔지니어링 테크닉

