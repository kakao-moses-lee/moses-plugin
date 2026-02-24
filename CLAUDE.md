# Claude Code 설정 - moses-plugin

이 파일은 Claude Code가 이 프로젝트에서 자동으로 로드할 설정입니다.

## 📋 자동 로드 규칙

Claude Code에서 다음 키워드로 질문할 때, 자동으로 해당 규칙 파일을 참조합니다:

### Python/PySpark 질문
**키워드**: `python`, `pyspark`, `type hints`, `dataclass`, `f-string`, `generator`, `comprehension`
**규칙 파일**: `plugins/moses-rules/coding-rules/python-rules.md`

```
예: "Python에서 Type Hints는?"
→ python-rules.md 자동 로드
```

### Scala/Spark 질문
**키워드**: `scala`, `spark`, `val`, `var`, `option`, `case class`, `pattern matching`, `dataset`
**규칙 파일**: `plugins/moses-rules/coding-rules/scala-rules.md`

```
예: "Scala에서 val과 var의 차이는?"
→ scala-rules.md 자동 로드
```

### 아키텍처 질문
**키워드**: `architecture`, `hexagonal`, `domain`, `service`, `port`, `adapter`, `package`
**규칙 파일**: `plugins/moses-rules/architecture/hexagonal.md`

```
예: "헥사고날 아키텍처의 Domain 계층은?"
→ hexagonal.md 자동 로드
```

### 테스트 질문
**키워드**: `test`, `testing`, `pytest`, `anyunspec`, `given-when-then`, `mock`
**규칙 파일**: `plugins/moses-rules/testing/testing-rules.md`

```
예: "테스트는 Given-When-Then으로?"
→ testing-rules.md 자동 로드
```

## 🔧 설정 파일

- `.claude/config.json` - Claude Code 기본 설정
- `.claude/rules.json` - 규칙 파일 메타데이터

## 📖 모든 규칙 보기

```
규칙을 모두 보여줘
```

이렇게 하면 4가지 규칙 파일이 모두 로드됩니다.

## 💡 팁

- 규칙 파일을 직접 읽으려면: `cat plugins/moses-rules/coding-rules/python-rules.md`
- 특정 섹션만 보려면: `grep -A 20 "## 2. Type Hints" plugins/moses-rules/coding-rules/python-rules.md`
