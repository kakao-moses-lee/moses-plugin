# moses-rules: 팀 코딩 표준

Python/PySpark와 Scala/Spark 개발을 위한 팀 코딩 표준 및 가이드입니다.

---

## 📚 포함된 규칙

### 1. Python/PySpark 코딩 규칙
- **파일**: [coding-rules/python-rules.md](./coding-rules/python-rules.md)
- **내용**: 명명 규칙, Type Hints, Dataclass/Pydantic, Optional[T], 단일 책임 함수, 예외 처리, f-string, Generator/Comprehension, PySpark DataFrame 체인, select 기반 변환

### 2. Scala/Spark 코딩 규칙
- **파일**: [coding-rules/scala-rules.md](./coding-rules/scala-rules.md)
- **내용**: val 우선, Option[T], case class, for comprehension, Pattern matching, implicit 최소화, 순수 함수, Dataset 타입 안전성

### 3. 헥사고날 아키텍처
- **파일**: [architecture/hexagonal.md](./architecture/hexagonal.md)
- **내용**: 패키지 구조 (atom → tag → aggregation), 레이어별 역할 (Domain, Service, Port, Adapter), 의존성 규칙, 명명 규칙

### 4. 테스트 규약
- **파일**: [testing/testing-rules.md](./testing/testing-rules.md)
- **내용**: Given-When-Then 패턴, pytest (Python), AnyFunSpec (Scala), Mock 패턴, PySpark/Spark 테스트, 환경별 구분

---

## 🚀 사용 방법

Claude Code에서 규칙에 대해 물어보세요:

```
"Python Type Hints 규칙을 보여줘"
"Scala val과 var의 차이는?"
"헥사고날 아키텍처의 Domain 계층은?"
"테스트는 Given-When-Then으로?"
```

Claude AI가 자동으로 이 규칙들을 참조해서 답변합니다.

---

## 📖 직접 파일 읽기

각 규칙 파일을 직접 읽어보세요:

- [Python Rules](./coding-rules/python-rules.md)
- [Scala Rules](./coding-rules/scala-rules.md)
- [Hexagonal Architecture](./architecture/hexagonal.md)
- [Testing Rules](./testing/testing-rules.md)
