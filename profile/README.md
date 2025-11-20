# 🧸 TeDDie - Test Driven Development AI Generator

> **우아한테크코스 프리코스 스타일의 TDD 연습 문제를 AI로 자동 생성하는 시스템**

[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://www.oracle.com/java/)
[![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

---

## 📖 목차

1. [프로젝트 소개](#-프로젝트-소개)
2. [시스템 아키텍처](#-시스템-아키텍처)
3. [주요 기능](#-주요-기능)
4. [기술 스택](#-기술-스택)
5. [설치 및 실행](#-설치-및-실행)
6. [사용 방법](#-사용-방법)
7. [프로젝트 구조](#-프로젝트-구조)
8. [개발 과정](#-개발-과정)

---

## 🎯 프로젝트 소개

### 왜 TeDDie를 만들었나요?

우아한테크코스 프리코스를 진행하면서 TDD의 가치를 체감했습니다. 하지만 우테코 스타일의 연습 문제가 더 필요했고, "그럼 AI로 만들어보자!"라고 생각했습니다.

### 핵심 아이디어

TeDDie는 세 가지 핵심 컴포넌트로 구성된 **완전 자동화된 TDD 문제 생성 시스템**입니다:

1. **실제 우테코 과제를 RAG로 학습** (TeDDie-RagSystem)
2. **비슷한 스타일의 새로운 문제 생성** (LLM + RAG API)
3. **바로 풀 수 있는 프로젝트 자동 생성** (TeDDie Java Application)

### 무엇이 특별한가요?

- ✅ **완전 자동화**: CLI 명령 하나로 프로젝트 생성부터 테스트 코드까지
- ✅ **우테코 스타일 재현**: RAG로 실제 프리코스 과제 학습
- ✅ **로컬 실행**: LM Studio로 비용 없이 무제한 생성
- ✅ **TDD 중심**: 생성된 테스트 케이스가 자동으로 JUnit 코드로 변환
- ✅ **Java 생태계**: 우테코 환경과 동일한 Java 21 + Gradle 프로젝트

---

## 🏗 시스템 아키텍처

TeDDie는 **3-Tier 하이브리드 아키텍처**를 채택했습니다:

```
┌─────────────────────────────────────────────────────────────────┐
│                    TeDDie (Java Application)                    │
│                                                                 │
│  사용자 입력 처리 → 미션 생성 요청 → 프로젝트 자동 생성           │
│                                                                 │
│  • CLI 인터페이스 (--topic, --difficulty)                       │
│  • HTTP Client (Java 21 HttpClient)                            │
│  • Project Generator (Gradle 프로젝트 생성)                     │
│  • Test Code Generator (JUnit 코드 자동 생성)                   │
│                                                                 │
└────────────┬──────────────────────────────┬────────────────────┘
             │                              │
             ▼                              ▼
    ┌────────────────┐            ┌─────────────────┐
    │   LM Studio    │            │  TeDDie-RagAPI  │
    │  (로컬 LLM)     │            │   (FastAPI)     │
    │   Port 1234    │            │   Port 8000     │
    └────────────────┘            └────────┬────────┘
                                           │
                                           ▼
                                  ┌─────────────────┐
                                  │ TeDDie-RagSystem│
                                  │  (Python Core)  │
                                  │                 │
                                  │ • FAISS Index   │
                                  │ • Embeddings    │
                                  │ • Search Engine │
                                  └─────────────────┘
```

### 왜 하이브리드 구조인가?

**초기 고민:**
- Java만으로 RAG를 구현? → FAISS, sentence-transformers가 Python에 더 성숙
- Python만 사용? → 우테코가 Java 중심, TDD 연습도 Java로 하고 싶음

**결정:**
- **Java**: 메인 애플리케이션, TDD 연습, 도메인 로직
- **Python**: RAG 시스템 (벡터 검색, 임베딩)
- **FastAPI**: Java ↔ Python 브릿지

---

## 🎯 주요 기능

### 1. 우테코 스타일 문제 자동 생성

```bash
./gradlew run --args="--topic lotto --difficulty medium"
```

**실행 결과:**
- 바탕화면에 `java-lotto` 프로젝트 자동 생성
- README.md에 미션 내용 작성
- ApplicationTest.java에 테스트 코드 자동 생성
- 바로 실행 가능한 Gradle 프로젝트

### 2. RAG 기반 과제 검색

TeDDie는 우테코 프리코스 과제를 RAG로 학습하여, 주제와 관련된 과제를 자동으로 찾아 프롬프트에 포함합니다.

**검색 가능한 과제:**
- java-racingcar (자동차 경주)
- java-lotto (로또)
- java-baseball (숫자 야구)
- java-christmas (크리스마스 프로모션)
- java-oncall (비상 근무)
- 그 외 10+ 과제

### 3. 자동 테스트 코드 생성

LLM이 생성한 테스트 케이스를 **실제 JUnit 코드로 자동 변환**합니다.

**LLM 응답 (테스트 케이스 섹션):**
```markdown
## 테스트 케이스

### 기능 테스트
- 입력: pobi,woni\n1
- 출력: pobi : -

### 예외 테스트
- 입력: pobi,javaji\n1
```

**자동 생성되는 코드:**
```java
package racingcar;

import camp.nextstep.edu.missionutils.test.NsTest;
import org.junit.jupiter.api.Test;

import static camp.nextstep.edu.missionutils.test.Assertions.assertSimpleTest;
import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.assertThatThrownBy;

class ApplicationTest extends NsTest {
    @Test
    void 기능_테스트() {
        assertSimpleTest(() -> {
            run("pobi,woni\n1");
            assertThat(output()).contains("pobi : -");
        });
    }

    @Test
    void 예외_테스트() {
        assertSimpleTest(() ->
            assertThatThrownBy(() -> runException("pobi,javaji\n1"))
                .isInstanceOf(IllegalArgumentException.class)
        );
    }

    @Override
    public void runMain() {
        Application.main(new String[]{});
    }
}
```

### 4. 완전한 프로젝트 구조 생성

생성되는 프로젝트는 우테코 프리코스와 동일한 구조를 가집니다:

```
java-lotto/
├── build.gradle
├── settings.gradle
├── gradlew
├── gradlew.bat
├── README.md              # 미션 내용
├── src/
│   ├── main/
│   │   └── java/
│   │       └── lotto/
│   │           └── Application.java
│   └── test/
│       └── java/
│           └── lotto/
│               └── ApplicationTest.java  # 자동 생성된 테스트
└── gradle/
    └── wrapper/
```

---

## 🛠 기술 스택

### TeDDie (Java Application)

| 분류 | 기술 | 버전 | 용도 |
|------|------|------|------|
| **Language** | Java | 21 | 메인 애플리케이션 |
| **Build Tool** | Gradle | 8.14 | 빌드 자동화 |
| **Testing** | JUnit 5 | 5.10.0 | 단위 테스트 |
| | AssertJ | 3.26.0 | 테스트 Assertion |
| | Mockito | 4.3.1 | Mock 객체 |
| | MockWebServer | 4.12.0 | HTTP 테스트 |
| **Libraries** | Gson | 2.10.1 | JSON 파싱 |
| | java-dotenv | 5.2.2 | 환경 변수 관리 |

### TeDDie-RagSystem (Python Core)

| 분류 | 기술 | 버전 | 용도 |
|------|------|------|------|
| **Language** | Python | 3.10+ | RAG 시스템 |
| **Vector Search** | FAISS | - | 벡터 유사도 검색 |
| **Embeddings** | sentence-transformers | - | 텍스트 임베딩 |
| | paraphrase-multilingual-MiniLM-L12-v2 | - | 다국어 임베딩 모델 |
| **Testing** | pytest | 9.0.0 | 테스트 프레임워크 |

### TeDDie-RagAPI (FastAPI Server)

| 분류 | 기술 | 버전 | 용도 |
|------|------|------|------|
| **Framework** | FastAPI | 0.104.1 | REST API 서버 |
| **Server** | Uvicorn | 0.24.0 | ASGI 서버 |
| **Validation** | Pydantic | 2.6.4 | 데이터 검증 |
| **Testing** | pytest | 9.0.0 | API 테스트 |
| | httpx | 0.25.2 | HTTP 클라이언트 |
| **Config** | python-dotenv | 1.0.1 | 환경 변수 |

### LM Studio (로컬 LLM)

| 항목 | 내용 |
|------|------|
| **플랫폼** | LM Studio (GUI) |
| **포트** | 1234 |
| **프로토콜** | HTTP/1.1 (OpenAI Compatible) |
| **추천 모델** | Llama 3.1, Qwen, Mistral 계열 |

---

## 🚀 설치 및 실행

### 사전 요구사항

- **Java 21** (JDK 21)
- **Python 3.10+**
- **LM Studio** (로컬 LLM 실행용)
- **Gradle** (프로젝트에 포함)

### 1. 프로젝트 클론

```bash
# 메인 프로젝트
git clone https://github.com/your-username/TeDDie.git
cd TeDDie

# RAG 시스템 (서브모듈 또는 별도 클론)
git clone https://github.com/your-username/TeDDie-RagSystem.git
git clone https://github.com/your-username/TeDDie-RagAPI.git
```

### 2. RAG 시스템 설정

#### 2.1 데이터 크롤링

```bash
cd TeDDie-RagSystem
pip install -r requirements.txt
python -m crawler.CrawlWoowacourseRagDataset
```

**결과:** `woowacourse_rag_dataset.jsonl` 생성

#### 2.2 인덱스 구축

```python
from rag.RagSearch import WoowacourseRAG

rag = WoowacourseRAG()
rag.build_index()
rag.save_index("faiss_index.bin")
```

#### 2.3 RAG API 서버 실행

```bash
cd ../TeDDie-RagAPI
pip install -r requirements.txt
python main.py
```

**접속 확인:** http://localhost:8000/docs

### 3. LM Studio 설정

1. LM Studio 다운로드 및 설치
2. 모델 다운로드 (오픈소스 모델: a.x-4.0-light)
3. Local Server 시작
4. 포트 확인: `http://localhost:1234`

### 4. TeDDie 설정

#### 4.1 환경 변수 설정

프로젝트 루트에 `.env` 파일 생성:

```env
LM_STUDIO_URL=http://localhost:1234/v1/chat/completions
RAG_API_URL=http://localhost:8000/api/search
```

#### 4.2 빌드 및 테스트

```bash
cd TeDDie
./gradlew clean build
./gradlew test
```

---

## 💻 사용 방법

### 기본 사용법

```bash
./gradlew run --args="--topic [주제] --difficulty [난이도]"
```

### 옵션

| 옵션 | 설명 | 필수 | 예시 |
|------|------|:----:|------|
| `--topic` | 생성할 문제의 주제 | ✅ | `lotto`, `string`, `collection` |
| `--difficulty` | 난이도 | ✅ | `easy`, `medium`, `hard` |

### 실행 예시

#### 1. 로또 문제 생성 (중간 난이도)

```bash
./gradlew run --args="--topic lotto --difficulty medium"
```

**결과:**
```
🧸 TeDDie: 문제 생성 완료!
--- (미션 내용) ---
# 🧩 로또 게임

## 기능 요구 사항
- 로또 번호 생성
- 당첨 번호 입력
- 당첨 통계 출력
...
____________________

✅ 프로젝트 생성: ~/Desktop/java-lotto
```

#### 2. 자동차 경주 문제 생성 (쉬움 난이도)

```bash
./gradlew run --args="--topic racing --difficulty easy"
```

#### 3. 문자열 처리 문제 생성 (어려움 난이도)

```bash
./gradlew run --args="--topic string --difficulty hard"
```

### 생성된 프로젝트 실행

```bash
cd ~/Desktop/java-lotto
./gradlew test
```

---

## 📂 프로젝트 구조

### 전체 구조

```
TeDDie-Project/
│
├── TeDDie/                          # Java 메인 애플리케이션
│   ├── src/main/java/teddie/
│   │   ├── Application.java
│   │   ├── api/                     # API 통신
│   │   │   ├── RagClient.java
│   │   │   └── dto/
│   │   ├── common/                  # 공통 설정
│   │   │   ├── config/
│   │   │   └── util/
│   │   ├── controller/              # 흐름 제어
│   │   ├── domain/                  # 도메인 모델
│   │   ├── exception/               # 예외 처리
│   │   ├── generator/               # 프로젝트 생성
│   │   ├── prompt/                  # 프롬프트 관리
│   │   ├── service/                 # 비즈니스 로직
│   │   └── view/                    # 출력 담당
│   ├── src/test/java/teddie/
│   ├── src/main/resources/template/
│   ├── build.gradle
│   └── README.md
│
├── TeDDie-RagSystem/                # Python RAG 코어
│   ├── rag/
│   │   ├── Loader.py
│   │   ├── Embedder.py
│   │   ├── SearchEngine.py
│   │   └── RagSearch.py
│   ├── crawler/
│   │   └── CrawlWoowacourseRagDataset.py
│   ├── test/
│   ├── requirements.txt
│   └── README.md
│
└── TeDDie-RagAPI/                   # FastAPI 서버
    ├── app.py
    ├── main.py
    ├── controller/
    ├── domain/
    ├── infra/
    ├── service/
    ├── util/
    ├── test/
    ├── requirements.txt
    └── README.md
```

### 컴포넌트별 역할

#### TeDDie (Java Application)

**역할:** 사용자 인터페이스, 프로젝트 생성, 테스트 코드 생성

**핵심 클래스:**
- `TeDDieController`: CLI 인터페이스 및 전체 흐름 제어
- `MissionService`: LLM 호출 및 응답 파싱
- `TestGenerator`: JUnit 테스트 코드 자동 생성
- `ProjectGeneratorController`: Gradle 프로젝트 생성

#### TeDDie-RagSystem (Python Core)

**역할:** 우테코 과제 데이터 임베딩 및 유사도 검색

**핵심 모듈:**
- `DocumentLoader`: JSONL 파일 로드
- `Embedder`: 텍스트 → 벡터 변환
- `FaissSearchEngine`: FAISS 인덱스 관리 및 검색
- `WoowacourseRAG`: Facade 패턴 (통합 인터페이스)

#### TeDDie-RagAPI (FastAPI Server)

**역할:** Java ↔ Python 브릿지

**주요 엔드포인트:**
- `POST /api/search`: RAG 검색 요청
- `GET /health`: 서버 상태 확인

---

## 💡 개발 과정

### 주요 기술적 결정

#### 1. HTTP/1.1 명시 (HttpRequestSender)

**문제:** LM Studio가 HTTP/2를 완벽 지원하지 않아 연결 실패

**해결:**
```java
HttpClient.newBuilder()
    .version(HttpClient.Version.HTTP_1_1)  // ✅ 명시
    .build();
```

#### 2. Record 타입 적극 활용

**이유:**
- 불변 객체로 DTO 안전성 보장
- getter/setter 없이 깔끔한 구조
- Java 17+의 현대적 기능 활용

**예시:**
```java
public record ApiRequest(
    String model,
    List<ApiMessage> messages,
    double temperature,
    int max_tokens,
    boolean stream
) {}
```

#### 3. MissionResponse 구조 설계

**고민:** LLM 응답을 어떻게 구조화할까?

**해결:**
```java
public record MissionResponse(
    String mission,           // README용
    List<TestCase> testCases  // ApplicationTest용
)
```

**장점:**
- 한 번 파싱으로 두 가지 결과 획득
- Controller에서 용도별로 깔끔하게 분리

### 테스트 전략

#### TDD 사이클

```
Red → Green → Refactor
```

**테스트 구조:**
1. **단위 테스트**: 각 클래스 메서드별 테스트
2. **통합 테스트**: Controller-Service-View 연동
3. **MockWebServer**: HTTP 통신 시뮬레이션

#### 예시: HttpRequestSender 테스트

```java
@Test
void LM_Studio로_POST_요청_시_응답_본문_반환() {
    // given
    mockWebServer.enqueue(new MockResponse()
            .setResponseCode(200)
            .setBody("{\"response\":\"성공\"}"));
    
    // when
    String result = sender.postToLmStudio("{}");
    
    // then
    assertThat(result).isEqualTo("{\"response\":\"성공\"}");
}
```

### 코딩 컨벤션

- ✅ 한 메서드에 한 단계 들여쓰기
- ✅ else 예약어 사용 금지
- ✅ 원시값과 문자열 포장
- ✅ 3개 이하 인스턴스 변수
- ✅ getter/setter 없이 구현
- ✅ 메서드 인자 3개 이하
- ✅ 코드 한 줄에 점(.) 하나

---

## 🎓 배운 점

### 1. 아키텍처 설계

**하이브리드 구조의 장점:**
- Java: 객체지향 설계, TDD 연습
- Python: ML/AI 생태계 활용
- FastAPI: 효율적인 브릿지

### 2. AI 통합의 어려움

**LLM 출력의 불확실성:**
- 프롬프트로 100% 제어 불가
- 방어적 파싱 필수
- 정규식 + 후처리 조합

**예시:**
```java
private String cleanTestValue(String value) {
    value = value.replaceAll("```[a-z]*\\n?", "");  // 코드 블록 제거
    value = value.replaceAll("\\([^)]+\\)", "");    // 괄호 제거
    return value.split("\n")[0].trim();             // 첫 줄만
}
```

### 3. 테스트의 중요성

**Mock 전략:**
- MockWebServer: HTTP 레이어 테스트
- Mockito: 비즈니스 로직 집중

**결과:**
- 안정적인 리팩토링
- 빠른 버그 발견

---

## 🔮 향후 계획

- [ ] 웹 인터페이스 추가 (React)
- [ ] 사용자 피드백 시스템
- [ ] 생성된 문제 공유 플랫폼
- [ ] 다양한 프로그래밍 언어 지원

---

## 👨‍💻 개발자

**정용태** ([@jyt6640](https://github.com/jyt6640))

---

<div align="center">

**TeDDie와 함께 TDD를 연습하세요! 🧸**

[⬆ 맨 위로 가기](#-teddie---test-driven-development-ai-generator)

</div>
