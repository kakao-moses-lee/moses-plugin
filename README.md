# moses-plugin: 팀 코딩 규약 & 아키텍처

모세스 팀의 코딩 표준을 정의하는 Claude Code 플러그인 마켓플레이스입니다.
Python/PySpark와 Scala/Spark에서 사용할 **코딩 규칙**, **아키텍처**, **테스트 가이드**를 제공합니다.

---

## 🚀 3단계 설치

### Step 1️⃣: Claude Code CLI에서 마켓플레이스 추가

1. Claude Code CLI 프롬프트에 입력:
```
/marketplace add
```

2. **탭** 키를 눌러 자동완성 메뉴 열기

3. GUI 메뉴에서 **"Add marketplace"** 선택

4. GitHub 저장소 URL 입력:
```
https://github.com/kakao-moses-lee/moses-plugin.git
```
### Step 2️⃣: 플러그인 활성화

/plugin 에서 **moses-rules** 플러그인을 활성화합니다.

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

## 📋 포함된 플러그인

### moses-rules
Python/PySpark와 Scala/Spark 개발을 위한 팀 코딩 표준:

- **Python 코딩 규칙** ([python-rules.md](./plugins/moses-rules/coding-rules/python-rules.md))
  - 명명 규칙, Type Hints, Dataclass/Pydantic
  - Optional[T], 단일 책임 함수, 예외 처리
  - f-string, Generator/Comprehension
  - PySpark DataFrame 체인, select 기반 변환

- **Scala 코딩 규칙** ([scala-rules.md](./plugins/moses-rules/coding-rules/scala-rules.md))
  - val 우선, Option[T], case class
  - for comprehension, Pattern matching
  - 순수 함수, Dataset 타입 안전성

- **헥사고날 아키텍처** ([hexagonal.md](./plugins/moses-rules/architecture/hexagonal.md))
  - 패키지 구조: atom → tag → aggregation
  - 레이어: Domain, Service, Port (in/out), Adapter
  - 의존성 규칙, 명명 규칙

- **테스트 규약** ([testing-rules.md](./plugins/moses-rules/testing/testing-rules.md))
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
- [Python 규칙](https://github.com/kakao-moses-lee/moses-plugin/blob/main/plugins/moses-rules/coding-rules/python-rules.md)
- [Scala 규칙](https://github.com/kakao-moses-lee/moses-plugin/blob/main/plugins/moses-rules/coding-rules/scala-rules.md)
- [아키텍처](https://github.com/kakao-moses-lee/moses-plugin/blob/main/plugins/moses-rules/architecture/hexagonal.md)
- [테스트](https://github.com/kakao-moses-lee/moses-plugin/blob/main/plugins/moses-rules/testing/testing-rules.md)

---

## 🔧 새 플러그인 추가하기

marketplace에 새 플러그인을 추가하려면:

### Step 1: 플러그인 디렉토리 생성
```bash
mkdir -p plugins/your-plugin/.claude-plugin
```

### Step 2: plugin.json 작성
```json
{
  "name": "your-plugin",
  "version": "1.0.0",
  "description": "플러그인 설명",
  "author": {
    "name": "moses"
  }
}
```

### Step 3: 플러그인 파일 추가
```
plugins/your-plugin/
├── .claude-plugin/
│   └── plugin.json
├── file1.md
├── file2.md
└── folder/
    └── file3.md
```

### Step 4: marketplace.json에 등록
```json
{
  "plugins": [
    {
      "name": "moses-rules",
      "source": "./plugins/moses-rules",
      "description": "..."
    },
    {
      "name": "your-plugin",
      "source": "./plugins/your-plugin",
      "description": "플러그인 설명"
    }
  ]
}
```

### Step 5: Git 커밋 & 푸시
```bash
git add plugins/your-plugin
git commit -m "feat: your-plugin 추가"
git push origin main
```

---

## 🔄 규칙 업데이트

마켓플레이스가 자동으로 최신 규칙을 가져옵니다. 특별히 할 것이 없습니다!

---

## 📁 디렉토리 구조

```
moses-plugin/
├── .claude-plugin/
│   └── marketplace.json                    # 마켓플레이스 정의
├── plugins/
│   ├── moses-rules/                        # 플러그인 1
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   ├── coding-rules/
│   │   │   ├── python-rules.md
│   │   │   └── scala-rules.md
│   │   ├── architecture/
│   │   │   └── hexagonal.md
│   │   └── testing/
│   │       └── testing-rules.md
│   ├── another-plugin/                     # 플러그인 2 (미래)
│   │   └── ...
│   └── yet-another-plugin/                 # 플러그인 3 (미래)
│       └── ...
└── README.md                               # 이 파일
```

---

## ❓ FAQ

| 질문 | 답변 |
|------|------|
| **마켓플레이스 URL을 잘못 입력했어요** | `/marketplace add` 다시 입력하면 덮어씌워집니다 |
| **Claude Code를 재시작해야 해요?** | 아니요, 바로 사용 가능합니다 |
| **규칙에 의견이 있어요** | GitHub Issues에 올리거나 팀 슬랙에 공유 |
| **새 플러그인을 추가할 수 있나요?** | 네, 위 "새 플러그인 추가하기" 섹션 참조 |

---

## 📞 지원

- **규칙 질문**: Claude Code에서 물어보기
- **문제 신고**: https://github.com/kakao-moses-lee/moses-plugin/issues
- **팀 공유**: 팀 슬랙 #규칙 채널
