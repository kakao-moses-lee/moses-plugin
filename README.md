# moses-plugin: 팀 공유 Claude Code 플러그인

모세스 팀이 공유하는 코딩 규약과 아키텍처 표준을 정의하는 Claude Code 플러그인입니다.

Python/PySpark와 Scala/Spark 환경에서 **일관된 코딩 스타일**, **헥사고날 아키텍처**, **테스트 규약**을 제공합니다.

---

## 📋 포함 규칙

### 1. 코딩 규칙
- **[python-rules.md](./static/coding-rules/python-rules.md)**: Python/PySpark 코딩 규약 (10가지)
  - 명명 규칙, Type Hints, Dataclass/Pydantic, Exception Handling, f-string, Generator, PySpark DataFrame 체인 등

- **[scala-rules.md](./static/coding-rules/scala-rules.md)**: Scala/Spark 코딩 규약
  - val 우선, Option[T], case class, Pattern Matching, 함수형 스타일, Dataset 타입 안전성 등

### 2. 아키텍처
- **[hexagonal.md](./static/architecture/hexagonal.md)**: 헥사고날 아키텍처 (Ports & Adapters)
  - 패키지 구조 (atom → tag → aggregation)
  - 레이어별 역할 (Domain, Service, Port, Adapter, Spark App, Config)
  - 의존성 방향 규칙
  - 명명 규칙

### 3. 테스트
- **[testing-rules.md](./static/testing/testing-rules.md)**: 테스트 규약
  - Given-When-Then 패턴
  - Python: pytest 기반 규약
  - Scala: AnyFunSpec 기반 규약
  - Mock 패턴
  - PySpark/Spark 테스트
  - CI/CD 통합

---

## 🚀 설치 및 사용

### 1. 마켓플레이스 추가

`.claude-plugin/marketplace.json`을 Claude Code에 등록합니다:

```bash
# 마켓플레이스 설정 (한 번만)
cat .claude-plugin/marketplace.json
```

**마켓플레이스 경로 추가 절차**:
1. Claude Code 열기
2. 설정 (⚙️) → Plugin 설정
3. "마켓플레이스 추가" 클릭
4. 경로 입력: `/Users/moses/dev/moses-plugin/.claude-plugin/marketplace.json`
5. 확인

### 2. 플러그인 로드

마켓플레이스 추가 후, **moses-rules** 플러그인을 활성화합니다.

---

## 📖 규칙 참조 방법

### 방법 1: 직접 읽기 (추천)
각 규칙 파일을 직접 읽고 참조하세요:

```bash
# Python 규칙
cat static/coding-rules/python-rules.md

# Scala 규칙
cat static/coding-rules/scala-rules.md

# 아키텍처
cat static/architecture/hexagonal.md

# 테스트
cat static/testing/testing-rules.md
```

### 방법 2: Claude Code에서 참조
플러그인 활성화 후 Claude Code에서:
1. 코드 작성 중에 Claude AI에 질문
2. "Python 규약의 Type Hints 규칙은?"
3. "Scala에서 null 대신 Option을 써야 하는 이유는?"
4. Claude가 플러그인 문서를 참조하여 답변

### 방법 3: 특정 규칙 검색
```bash
# Python에서 Generator 규칙
grep -A 20 "## 8. Generator" static/coding-rules/python-rules.md

# Scala에서 val/var 규칙
grep -A 20 "## 1. val 우선" static/coding-rules/scala-rules.md

# 헥사고날 구조
grep -A 10 "패키지 구조" static/architecture/hexagonal.md
```

---

## 🏗️ 플러그인 구조

```
moses-plugin/
├── .claude-plugin/
│   └── marketplace.json              # 마켓플레이스 메타데이터
├── static/                           # 규칙 문서 (팀원 직접 참조)
│   ├── coding-rules/
│   │   ├── python-rules.md           # Python/PySpark 규약
│   │   └── scala-rules.md            # Scala/Spark 규약
│   ├── architecture/
│   │   └── hexagonal.md              # 헥사고날 아키텍처
│   └── testing/
│       └── testing-rules.md          # 테스트 규약
├── agents/
│   └── claude/
│       └── rules/
│           └── .claude-plugin/
│               └── plugin.json       # 플러그인 메타데이터
└── README.md                         # 이 파일
```

### 디렉토리 역할

- **`static/`**: 실제 규칙 문서
  - 언어/도구 중립적
  - 팀원들이 직접 읽고 참조
  - IDE에서 검색 가능

- **`agents/claude/`**: Claude Code 플러그인 메타데이터
  - `plugin.json`: 플러그인 이름, 버전, 설명
  - `static/`의 문서를 참조하는 역할

---

## 📝 사용 예시

### Python 프로젝트

```python
# ✅ 규칙을 따르는 예시

# 1. Type Hints 필수
def fetch_user(user_id: int) -> Optional[User]:
    pass

# 2. Dataclass로 도메인 모델
from dataclasses import dataclass

@dataclass
class User:
    id: int
    name: str
    email: str

# 3. f-string 사용
name = "Alice"
print(f"User: {name}")

# 4. Generator 활용 (메모리 효율)
def read_large_file(filepath: str):
    with open(filepath, 'r') as f:
        yield f.readline()

# 5. PySpark DataFrame 체인
df_result = (
    df_raw
    .filter(col("age") > 18)
    .select("id", "name")
)
```

참조: [python-rules.md](./static/coding-rules/python-rules.md)

### Scala 프로젝트

```scala
// ✅ 규칙을 따르는 예시

// 1. val 우선
val userId: Int = 123
val user: User = fetchUser(userId)

// 2. Option[T] 사용
def findUser(userId: Int): Option[User] = {
  if (userId > 0) Some(User(userId, "John"))
  else None
}

// 3. case class로 도메인 모델
case class User(id: Int, name: String, email: String)

// 4. for comprehension
val result: Option[String] = for {
  user <- findUser(123)
  email <- parseEmail(user.email)
} yield email

// 5. Pattern Matching
val message = user match {
  case Some(User(_, name, _)) => s"Hello, $name!"
  case None => "User not found"
}
```

참조: [scala-rules.md](./static/coding-rules/scala-rules.md)

### 헥사고날 아키텍처 적용

```scala
// src/main/scala/com/moses/domain/user/
// ├── domain/
// │   └── User.scala           # Entity
// ├── port/
// │   ├── in/
// │   │   └── CreateUserUseCase.scala
// │   └── out/
// │       └── UserRepository.scala
// ├── service/
// │   └── UserService.scala    # Service (port 의존)
// └── adapter/
//     └── out/
//         └── JdbcUserRepository.scala

class UserService(repository: UserRepository) {
  def createUser(request: CreateUserRequest): User = {
    val user = User(...)
    repository.save(user)  // Port 호출
    user
  }
}
```

참조: [hexagonal.md](./static/architecture/hexagonal.md)

### 테스트 작성

```python
# Python: pytest

class TestUserService:
    def test_create_user(self):
        """Given: 유효한 요청
        When: 사용자 생성
        Then: 저장되고 이메일 발송"""
        # Given
        request = CreateUserRequest("Alice", "alice@example.com")

        # When
        user = service.create_user(request)

        # Then
        assert user.name == "Alice"
        repository.save.assert_called_once()
```

```scala
// Scala: AnyFunSpec

describe("UserService") {
  it("should create user and send email") {
    // Given
    val request = CreateUserRequest("Alice", "alice@example.com")

    // When
    val user = service.createUser(request)

    // Then
    user.name shouldBe "Alice"
    verify(repository).save(any[User])
  }
}
```

참조: [testing-rules.md](./static/testing/testing-rules.md)

---

## 🔄 규칙 업데이트

규칙이 변경되면 해당 문서를 업데이트합니다:

```bash
# 예: Python 규칙 추가
vim static/coding-rules/python-rules.md

# 커밋
git add static/
git commit -m "docs: Python 규칙에 async/await 추가"
```

모든 팀원은 최신 규칙을 자동으로 받습니다.

---

## ❓ FAQ

**Q: 규칙을 어기면 어떻게 되나요?**
- 코드 리뷰 시 피드백 대상입니다.
- 신규 프로젝트부터는 필수입니다.

**Q: 언어별로 다른 규칙을 따르나요?**
- 네, Python과 Scala는 각각의 문법에 맞춰 규칙이 다릅니다.
- 아키텍처와 테스트 원칙은 동일합니다.

**Q: 헥사고날 아키텍처가 필수인가요?**
- 신규 프로젝트: 필수
- 기존 프로젝트: 점진적 적용

**Q: Claude Code 없이도 규칙을 볼 수 있나요?**
- 네, `static/` 디렉토리의 마크다운 문서는 IDE나 브라우저에서 직접 읽을 수 있습니다.

---

## 📞 문의

규칙에 대한 질문이나 개선 사항은 팀 리드에게 문의하세요.

- 규칙 추가/변경: Pull Request 제안
- 질문: 팀 슬랙 #규칙 채널

---

## 라이선스

내부 전용 플러그인입니다.
