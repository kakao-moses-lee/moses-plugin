# moses-plugin: 팀 공유 코딩 규약 & 아키텍처

모세스 팀의 코딩 표준을 정의하는 플러그인입니다.
Python/PySpark와 Scala/Spark에서 사용할 **규칙**, **아키텍처**, **테스트 가이드**를 제공합니다.

---

## 🚀 설치 방법 (2가지)

### ⚡ 방법 A: GitHub URL로 직접 설치 (추천 - 가장 간단)

**이 방법이 좋은 이유**: git clone 할 필요 없이, Claude Code에서 바로 플러그인을 다운로드해서 사용합니다.

#### Step 1️⃣: Claude Code 열기
Claude Code 실행 후 하단 설정 (⚙️) → Plugin 클릭

#### Step 2️⃣: 마켓플레이스 URL 추가
"Marketplace" 섹션에서 "+" 버튼 클릭 후, 이 URL을 입력:

```
https://raw.githubusercontent.com/kakao-moses-lee/moses-plugin/main/.claude-plugin/marketplace.json
```

**GUI에서의 입력**:
```
마켓플레이스 추가
┌──────────────────────────────────────────────────────────┐
│ https://raw.githubusercontent.com/kakao-moses-lee/      │
│ moses-plugin/main/.claude-plugin/marketplace.json        │
└──────────────────────────────────────────────────────────┘
```

#### Step 3️⃣: 플러그인 활성화
마켓플레이스 추가 후:
1. "moses-rules" 플러그인 확인
2. 토글 버튼 켜기 (활성화)

✅ **완료! 이제 규칙을 사용할 수 있습니다.**

---

### 📦 방법 B: Git Clone으로 로컬 설치

**이 방법이 좋은 이유**: 규칙 문서를 로컬에 완전히 보관하고 오프라인에서도 사용할 수 있습니다.

#### Step 1️⃣: 저장소 클론
```bash
git clone https://github.com/kakao-moses-lee/moses-plugin.git
cd moses-plugin
```

#### Step 2️⃣: 자신의 경로 확인
클론한 폴더의 전체 경로를 복사합니다. 예시:
- Mac: `/Users/yourname/development/moses-plugin`
- Linux: `/home/yourname/moses-plugin`

#### Step 3️⃣: Claude Code에 마켓플레이스 등록
Claude Code 열기 → 설정 (⚙️) → Plugin 클릭:

**마켓플레이스 경로 추가**:
```
┌──────────────────────────────────────────────────┐
│ /Users/yourname/moses-plugin/.claude-plugin     │
│                            /marketplace.json      │
└──────────────────────────────────────────────────┘
```

**구체 절차**:
1. 좌측 "Plugin" 메뉴 클릭
2. "Marketplace" 섹션에서 "+" 버튼 클릭
3. 텍스트 필드에 경로 입력: `<위에서_복사한경로>/.claude-plugin/marketplace.json`
4. Enter 키 또는 확인 버튼 클릭

#### Step 4️⃣: 플러그인 활성화
마켓플레이스 추가 후:
1. "moses-rules" 플러그인 확인
2. 토글 버튼 켜기 (활성화)

✅ **완료!**

#### 보너스: 최신 규칙 자동 받기
```bash
cd moses-plugin
git pull origin main
```

---

## 🔍 두 방법 비교

| 항목 | 방법 A (GitHub URL) | 방법 B (로컬 Clone) |
|------|-------------------|-------------------|
| **설치 시간** | 2분 | 5분 |
| **필요한 것** | 인터넷 연결 | git 설치 |
| **오프라인 사용** | ❌ | ✅ |
| **규칙 최신화** | 자동 | 수동 (git pull) |
| **추천 대상** | 대부분의 사용자 | 자주 규칙을 참조하는 사용자 |

→ **대부분 팀원은 방법 A (GitHub URL)를 사용하세요!**

---

## 📖 규칙 참조하기 (3가지 방법)

### 방법 1️⃣: Claude Code AI에게 물어보기 (추천)

플러그인이 활성화되면, Claude Code에서 바로 규칙을 물어볼 수 있습니다:

```
Q: Python에서 Type Hints는 어떻게 써야 해?
Q: Scala에서 var 대신 val을 써야 하는 이유는?
Q: 헥사고날 아키텍처의 Domain 계층은 뭐야?
Q: 테스트를 어떻게 구조화해야 해?
```

→ Claude AI가 **자동으로 규칙 문서를 참조**해서 답변합니다. 가장 편하고 빠릅니다!

---

### 방법 2️⃣: GitHub 저장소에서 직접 읽기 (브라우저)

GitHub에서 바로 규칙 문서를 읽을 수 있습니다:

- **Python 규칙**: https://github.com/kakao-moses-lee/moses-plugin/blob/main/static/coding-rules/python-rules.md
- **Scala 규칙**: https://github.com/kakao-moses-lee/moses-plugin/blob/main/static/coding-rules/scala-rules.md
- **아키텍처**: https://github.com/kakao-moses-lee/moses-plugin/blob/main/static/architecture/hexagonal.md
- **테스트**: https://github.com/kakao-moses-lee/moses-plugin/blob/main/static/testing/testing-rules.md

브라우저에서 링크를 클릭하면 바로 읽을 수 있습니다!

---

### 방법 3️⃣: 로컬 폴더에서 직접 읽기 (방법 B Clone한 경우)

방법 B로 clone한 사용자는 로컬 폴더를 IDE/에디터에서 열고 직접 읽습니다:

```
moses-plugin/
├── static/
│   ├── coding-rules/
│   │   ├── python-rules.md      ← Python 규칙
│   │   └── scala-rules.md       ← Scala 규칙
│   ├── architecture/
│   │   └── hexagonal.md         ← 아키텍처
│   └── testing/
│       └── testing-rules.md     ← 테스트 규칙
```

**예**: VSCode에서 `static/coding-rules/python-rules.md` 열어서 읽기

---

### 방법 4️⃣: 터미널에서 검색하기 (로컬 clone 사용자만)

```bash
cd moses-plugin

# Python에서 Type Hints 규칙 찾기
grep -A 10 "## 2. Type Hints" static/coding-rules/python-rules.md

# Scala에서 val/var 규칙 찾기
grep -A 15 "## 1. val 우선" static/coding-rules/scala-rules.md

# 헥사고날 패키지 구조 찾기
grep -A 20 "패키지 구조" static/architecture/hexagonal.md

# 테스트 Given-When-Then 패턴 찾기
grep -A 5 "Given-When-Then" static/testing/testing-rules.md
```

---

## 📊 참조 방법 비교

| 방법 | 편의성 | 속도 | 상황 |
|------|--------|------|------|
| **방법 1: Claude AI** | ⭐⭐⭐⭐⭐ | 빠름 | 구체적인 질문이 있을 때 |
| **방법 2: GitHub 브라우저** | ⭐⭐⭐⭐ | 매우 빠름 | 규칙을 한 번에 전체적으로 읽을 때 |
| **방법 3: 로컬 폴더** | ⭐⭐⭐ | 중간 | 오프라인 또는 자주 참조할 때 |
| **방법 4: 터미널 검색** | ⭐⭐ | 중간 | 특정 규칙을 빠르게 찾을 때 |

→ **추천**: 방법 1 (Claude AI) + 방법 2 (GitHub) 조합!

---

## 📋 포함된 규칙

### 1. 코딩 규칙

#### **Python/PySpark** ([python-rules.md](./static/coding-rules/python-rules.md))
1. 명명 규칙 (snake_case)
2. Type Hints 필수
3. Dataclass/Pydantic 사용
4. Optional[T] 명시
5. 단일 책임 함수 (≤20줄)
6. 구체적 예외 처리
7. f-string 사용
8. Generator/Comprehension
9. PySpark DataFrame 불변 체인
10. select 기반 변환

#### **Scala/Spark** ([scala-rules.md](./static/coding-rules/scala-rules.md))
1. val 우선, var 최소화
2. null 금지 → Option[T]
3. case class 모델링
4. for comprehension
5. Pattern matching
6. implicit 최소화
7. 순수 함수
8. Dataset 타입 안전성

### 2. 아키텍처 ([hexagonal.md](./static/architecture/hexagonal.md))

헥사고날 아키텍처 (Ports & Adapters) 구현:
- **패키지 구조**: atom → tag → aggregation → shared
- **레이어**: Domain, Service, Port (in/out), Adapter
- **의존성 규칙**: 안쪽이 바깥쪽을 모르도록
- **네이밍**: UseCase, Port, Repository, Adapter 등

### 3. 테스트 ([testing-rules.md](./static/testing/testing-rules.md))

- **공통 패턴**: Given-When-Then
- **Python**: pytest + Fixture + Mock
- **Scala**: AnyFunSpec + MockitoSugar
- **환경**: local (sandbox) → CBT → Airflow (prod)

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

## 💡 실전 예시

### 상황 1️⃣: Python 프로젝트 코딩 중

**질문**: "Type Hints는 어떻게 써야 해?"

**방법 A - 직접 읽기**:
```bash
open moses-plugin/static/coding-rules/python-rules.md
# 섹션 "## 2. Type Hints 필수" 읽기
```

**방법 B - Claude Code에서 물어보기**:
```
"Python Type Hints 규칙을 설명해줘"
→ Claude가 자동으로 규칙 문서 참조
```

**결과**: 이렇게 코드 작성
```python
# ✅ 올바른 예
from typing import Optional

def fetch_user(user_id: int) -> Optional[User]:
    """사용자를 조회합니다."""
    pass

@dataclass
class User:
    id: int
    name: str
    email: str
```

---

### 상황 2️⃣: Scala 프로젝트에서 변수 선언

**질문**: "var와 val 뭐를 써야 해?"

**방법 A - 터미널 검색**:
```bash
cd moses-plugin
grep -A 20 "## 1. val 우선" static/coding-rules/scala-rules.md
```

**방법 B - Claude Code 질문**:
```
"Scala에서 val과 var의 차이는?"
→ 규칙 문서 자동 참조
```

**결과**: 이렇게 코드 작성
```scala
// ✅ 올바른 예
val userId: Int = 123              // 불변
val user: User = fetchUser(userId)

// ❌ 피해야 할 예
var userId: Int = 123              // 부필요하게 변경 가능
```

---

### 상황 3️⃣: 새 프로젝트 구조 설계

**질문**: "폴더 구조를 어떻게 만들어야 해?"

**방법**: 규칙 문서 읽기
```bash
open moses-plugin/static/architecture/hexagonal.md
# "패키지 구조" 섹션 읽기
```

**결과**: 이렇게 폴더 생성
```
src/main/scala/com/moses/domain/user/
├── domain/
│   ├── User.scala          # Entity (비즈니스 모델)
│   └── UserId.scala        # Value Object
├── port/
│   ├── in/
│   │   └── CreateUserUseCase.scala  # 진입 인터페이스
│   └── out/
│       └── UserRepository.scala     # 외부 의존성
├── service/
│   └── UserService.scala   # 비즈니스 로직
└── adapter/
    └── out/
        └── JdbcUserRepository.scala # DB 연동
```

---

### 상황 4️⃣: 테스트 작성하기

**질문**: "테스트는 어떻게 구조를 만들어?"

**방법**: Claude Code에서 물어보기
```
"테스트 Given-When-Then 패턴을 보여줘"
→ 규칙 문서에서 pytest/AnyFunSpec 예시 제공
```

**Python 결과**:
```python
class TestUserService:
    def test_create_user_success(self):
        """Given: 유효한 요청
        When: 사용자 생성
        Then: 저장되고 이메일 발송"""

        # Given
        request = CreateUserRequest("Alice", "alice@example.com")
        mock_repo = Mock(spec=UserRepository)
        service = UserService(mock_repo)

        # When
        user = service.create_user(request)

        # Then
        assert user.name == "Alice"
        mock_repo.save.assert_called_once()
```

**Scala 결과**:
```scala
describe("UserService") {
  it("should create user and send email") {
    // Given
    val mockRepo = mock[UserRepository]
    val service = UserService(mockRepo)
    val request = CreateUserRequest("Alice", "alice@example.com")

    // When
    val user = service.createUser(request)

    // Then
    user.name shouldBe "Alice"
    verify(mockRepo).save(any[User])
  }
}
```

---

## 🔄 규칙 업데이트받기

### 방법 A 사용자 (GitHub URL)
✅ **자동 업데이트**! 특별히 할 것이 없습니다.
- Claude Code는 매번 최신 규칙을 GitHub에서 자동으로 읽습니다
- 항상 최신 상태를 유지합니다

### 방법 B 사용자 (로컬 Clone)
```bash
cd moses-plugin
git pull origin main
```
최신 규칙이 자신의 폴더에 적용됩니다!

---

## ❓ 자주 묻는 질문

| 질문 | 답변 |
|------|------|
| **마켓플레이스 경로를 잘못 입력했어요** | 설정 → Plugin → "마켓플레이스 수정" → 다시 입력 |
| **Claude Code에서 규칙이 참조 안 돼요** | 플러그인 활성화됐는지 확인. 안 됐으면 다시 활성화 후 Claude Code 재시작 |
| **규칙을 자주 확인하려면?** | `moses-plugin/static/` 폴더를 즐겨찾기에 추가 |
| **Python만 사용하는데 Scala 규칙도 봐야 해?** | 아니요, 필요한 언어의 규칙만 읽으세요 |
| **규칙에 반박이 있어요** | GitHub에 Issue 생성 또는 팀 슬랙에 의견 올리기 |

---

## 📞 지원

- **규칙 질문**: Claude Code 플러그인 활용 또는 팀 슬랙
- **저장소 문제**: https://github.com/kakao-moses-lee/moses-plugin/issues
- **설정 문제**: 팀 리드에게 직접 문의

---

**마지막 팁**: 규칙 문서를 자신의 IDE에서 **북마크/즐겨찾기**에 추가하면 언제든 빠르게 참조할 수 있습니다! 📌
