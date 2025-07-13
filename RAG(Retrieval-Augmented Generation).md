# RAG

RAG는 Retrieval-Augmented Generation의 줄임말로, 외부 지식을 검색(Retrieve)해서 LLM의 생성(Generation)에 보조 정보로 추가하는 방식입니다.

## 왜 필요한가?

기존 LLM (GPT, Claude, PaLM 등)은 고정된 파라미터 안에만 지식을 담고 있어서:

* 최신 정보 반영 불가

* 특정 도메인 지식 부족 (ex. 사내 문서, 기술 문서 등)

* 긴 문서 질의응답 시, context 제한 문제

→ 이런 문제를 해결하기 위해 RAG가 등장했습니다.

---

