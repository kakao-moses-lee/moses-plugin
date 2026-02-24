# moses-plugin: 팀 코딩 규약 & 아키텍처

모세스 팀의 코딩 표준을 정의하는 Claude Code 플러그인입니다.
Python/PySpark와 Scala/Spark에서 사용할 **코딩 규칙**, **아키텍처**, **테스트 가이드**를 제공합니다.

---

## 🚀 3단계 설치

### Step 1️⃣: Claude Code에서 마켓플레이스 추가

Claude Code 프롬프트에서 다음 명령어 입력:

```
/marketplace add https://raw.githubusercontent.com/kakao-moses-lee/moses-plugin/main/.claude-plugin/marketplace.json
/marketplace add https://github.com/kakao-moses-lee/moses-plugin.git
```

또는 UI를 사용하려면:
- 설정 (⚙️) → Plugin → "+" 버튼 → 위 URL 입력

### Step 2️⃣: 플러그인 활성화

설정 → Plugin에서 **moses-rules** 플러그인을 활성화합니다.

### Step 3️⃣: 규칙 사용하기

Claude Code 프롬프트에서 규칙을 물어보세요:

```
"Python에서 Type Hints는 어떻게 써야 해?"
"Scala에서 null 대신 Option을 쓰는 이유?"
"헥사고날 아키텍처의 Domain 계층은?"
"테스트는 어떻게 구조화해?"
```

Claude AI가 자동으로 규칙 문서를 참조해서 답변합니다! ✅

---

## 📋 포함된 규칙

### 1. Python/PySpark 코딩 규칙 ([python-rules.md](./static/coding-rules/python-rules.md))
- 명명 규칙, Type Hints, Dataclass/Pydantic
- Optional[T], 단일 책임 함수, 예외 처리
- f-string, Generator/Comprehension
- PySpark DataFrame 체인, select 기반 변환

### 2. Scala/Spark 코딩 규칙 ([scala-rules.md](./static/coding-rules/scala-rules.md))
- val 우선, Option[T], case class
- for comprehension, Pattern matching
- 순수 함수, Dataset 타입 안전성

### 3. 헥사고날 아키텍처 ([hexagonal.md](./static/architecture/hexagonal.md))
- 패키지 구조: atom → tag → aggregation
- 레이어: Domain, Service, Port (in/out), Adapter
- 의존성 규칙, 명명 규칙

### 4. 테스트 규약 ([testing-rules.md](./static/testing/testing-rules.md))
- Given-When-Then 패턴
- pytest (Python) + AnyFunSpec (Scala)
- Mock 패턴, PySpark/Spark 테스트

---

## 📖 규칙 읽기

### 방법 1: Claude Code에서 AI 질문 (추천)
```
"Python 규칙 중 Type Hints는?"
```
→ Claude가 자동으로 규칙 문서를 참조합니다.

### 방법 2: GitHub에서 직접 읽기
- [Python 규칙](https://github.com/kakao-moses-lee/moses-plugin/blob/main/static/coding-rules/python-rules.md)
- [Scala 규칙](https://github.com/kakao-moses-lee/moses-plugin/blob/main/static/coding-rules/scala-rules.md)
- [아키텍처](https://github.com/kakao-moses-lee/moses-plugin/blob/main/static/architecture/hexagonal.md)
- [테스트](https://github.com/kakao-moses-lee/moses-plugin/blob/main/static/testing/testing-rules.md)

---

## 🔄 규칙 업데이트

마켓플레이스가 자동으로 최신 규칙을 가져옵니다. 특별히 할 것이 없습니다!

---

## ❓ FAQ

| 질문 | 답변 |
|------|------|
| **마켓플레이스 URL을 잘못 입력했어요** | `/marketplace add` 다시 입력하면 덮어씌워집니다 |
| **Claude Code를 재시작해야 해요?** | 아니요, 바로 사용 가능합니다 |
| **오프라인에서 사용하고 싶어요** | git clone으로 로컬에 다운로드 후 로컬 경로 등록 |
| **규칙에 의견이 있어요** | GitHub Issues에 올리거나 팀 슬랙에 공유 |

---

## 📞 지원

- **규칙 질문**: Claude Code에서 물어보기
- **문제 신고**: https://github.com/kakao-moses-lee/moses-plugin/issues
- **팀 공유**: 팀 슬랙 #규칙 채널
