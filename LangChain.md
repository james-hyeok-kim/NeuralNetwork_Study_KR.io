# LangChain
## 정의

* LangChain은 **대규모 언어 모델(LLM)**을 실제 애플리케이션에 쉽게 통합하고 구성할 수 있도록 도와주는 Python 기반 오픈소스 프레임워크

* 기존의 언어 모델이 주로 텍스트 생성에 중점을 둔 반면, LangChain은 다양한 외부 데이터 소스와 통합하여 보다 복잡하고 유용한 애플리케이션을 만들 수 있도록 설계

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
