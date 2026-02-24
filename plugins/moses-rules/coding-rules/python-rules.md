# Python 코딩 규약 (PySpark 포함)

Python 및 PySpark 환경에서 팀 전체가 따를 코딩 표준입니다. PEP 8을 기반으로 하며, 실무 효율성을 위해 확장한 규약입니다.

## 1. 명명 규칙 (Naming Convention)

**규칙**: PEP 8 준수 - snake_case 사용

```python
# ✅ 올바른 예
def calculate_total_revenue():
    pass

class CustomerDataProcessor:
    pass

MAX_RETRIES = 5
user_id = 123

# ❌ 피해야 할 예
def calculateTotalRevenue():  # camelCase
    pass

class customer_data_processor:  # snake_case for class
    pass

max_retries = 5  # constant는 대문자
```

**적용범위**:
- 변수: snake_case
- 함수: snake_case
- 클래스: PascalCase
- 상수: UPPER_SNAKE_CASE

---

## 2. Type Hints 필수 (Type Annotations)

**규칙**: 모든 함수 시그니처에 타입 힌트 필수

```python
# ✅ 올바른 예
from typing import Optional, List, Dict

def fetch_user_data(user_id: int) -> Dict[str, any]:
    """사용자 데이터를 조회합니다."""
    pass

def process_records(records: List[str], batch_size: int = 100) -> None:
    """레코드를 배치로 처리합니다."""
    pass

# ❌ 피해야 할 예
def fetch_user_data(user_id):  # 타입 정보 없음
    pass
```

**적용범위**:
- 함수 파라미터 모두
- 반환 타입 필수
- 클래스 속성은 타입 힌트 권장

---

## 3. 도메인 모델은 Dataclass/Pydantic으로 표현 (Domain Models)

**규칙**: case class 역할을 하는 도메인 모델은 Dataclass 또는 Pydantic으로 정의

```python
from dataclasses import dataclass
from typing import Optional
from pydantic import BaseModel, Field

# ✅ Dataclass 예시
@dataclass
class User:
    """사용자 정보를 나타냅니다."""
    id: int
    name: str
    email: str
    age: Optional[int] = None

# ✅ Pydantic 예시 (검증 필요 시)
class Order(BaseModel):
    id: int
    user_id: int
    total_amount: float = Field(gt=0)
    status: str = Field(default="pending")

# ❌ 피해야 할 예
class User:
    """일반 클래스 - 도메인 모델은 불변이어야 함"""
    def __init__(self, id, name, email, age=None):
        self.id = id
        self.name = name
        self.email = email
        self.age = age
```

**선택 기준**:
- **Dataclass**: 간단한 데이터 컨테이너, 기본 검증
- **Pydantic**: 복잡한 검증, JSON 직렬화/역직렬화 필요

---

## 4. None 명시적 표현 (Optional & Union)

**규칙**: None을 반환할 가능성이 있으면 `Optional[T]` 또는 `Union` 명시

```python
from typing import Optional, Union

# ✅ 올바른 예
def find_user(user_id: int) -> Optional[User]:
    """사용자를 찾습니다. 없으면 None"""
    if user_id < 0:
        return None
    return User(user_id, "John")

def parse_value(value: str) -> Union[int, str, None]:
    """값을 파싱합니다."""
    try:
        return int(value)
    except ValueError:
        return value if value else None

# ❌ 피해야 할 예
def find_user(user_id: int):  # 반환 타입 모호
    if user_id < 0:
        return None
    return User(user_id, "John")
```

---

## 5. 단일 책임 함수 (Single Responsibility)

**규칙**: 함수는 하나의 책임만 가지며, 20줄 이하를 지향

```python
# ✅ 올바른 예
def validate_email(email: str) -> bool:
    """이메일 형식을 검증합니다."""
    import re
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return bool(re.match(pattern, email))

def save_user(user: User) -> bool:
    """사용자를 저장합니다."""
    if not validate_email(user.email):
        raise ValueError("Invalid email")
    # 저장 로직
    return True

# ❌ 피해야 할 예
def process_user_registration(email, name, age, address, phone):
    """여러 책임을 한 함수에서 처리"""
    # 이메일 검증
    if not validate_email(email):
        return False

    # 나이 검증
    if age < 18:
        return False

    # 주소 검증
    if not address:
        return False

    # 전화 검증
    if not phone:
        return False

    # 사용자 저장
    # ... 저장 로직
    return True
```

---

## 6. 예외 처리 구체화 (Exception Handling)

**규칙**: bare except 금지, 구체적인 예외 타입 명시

```python
# ✅ 올바른 예
def fetch_data(url: str) -> dict:
    """API에서 데이터를 조회합니다."""
    try:
        import requests
        response = requests.get(url, timeout=5)
        response.raise_for_status()
        return response.json()
    except requests.exceptions.Timeout:
        raise TimeoutError(f"Request timeout for {url}")
    except requests.exceptions.HTTPError as e:
        raise ValueError(f"HTTP error: {e.response.status_code}")
    except ValueError as e:
        raise ValueError(f"Invalid JSON response: {e}")

# ❌ 피해야 할 예
def fetch_data(url: str) -> dict:
    try:
        import requests
        response = requests.get(url)
        return response.json()
    except:  # 모든 예외를 잡음
        return {}
```

---

## 7. f-string 사용 (String Formatting)

**규칙**: f-string 사용, % 포맷과 .format() 지양

```python
# ✅ 올바른 예
name = "Alice"
age = 30
score = 95.5

message = f"사용자 {name}의 나이는 {age}이고, 점수는 {score:.1f}입니다."

# 표현식도 가능
result = f"합계: {10 + 20}"

# ❌ 피해야 할 예
message = "사용자 %s의 나이는 %d이고, 점수는 %.1f입니다." % (name, age, score)
message = "사용자 {}의 나이는 {}이고, 점수는 {:.1f}입니다.".format(name, age, score)
```

---

## 8. Generator와 Comprehension 활용 (Memory Efficiency)

**규칙**: 대용량 데이터 처리 시 Generator/List Comprehension으로 메모리 효율성 확보

```python
# ✅ 올바른 예 - Generator
def read_large_file(filepath: str):
    """대용량 파일을 한 줄씩 읽습니다."""
    with open(filepath, 'r') as f:
        for line in f:
            yield line.strip()

# ✅ 올바른 예 - List Comprehension
squares = [x**2 for x in range(1000) if x % 2 == 0]

# ✅ 올바른 예 - Dict Comprehension
user_ids = {user.id: user.name for user in users}

# ❌ 피해야 할 예 - 전체 파일을 메모리에 로드
def read_large_file(filepath: str):
    with open(filepath, 'r') as f:
        return [line.strip() for line in f]  # 메모리 낭비

# ❌ 피해야 할 예 - 반복문으로 구성
squares = []
for x in range(1000):
    if x % 2 == 0:
        squares.append(x**2)
```

---

## 9. PySpark: DataFrame 불변 체인 스타일 (DataFrame Immutability)

**규칙**: DataFrame은 불변으로 취급하고 체인 방식으로 변환

```python
# ✅ 올바른 예
df_result = (
    df_raw
    .filter(col("age") > 18)
    .select("id", "name", "email")
    .groupby("region").agg(count("*").alias("count"))
    .orderBy(desc("count"))
)

# ❌ 피해야 할 예
df = df_raw
df = df.filter(col("age") > 18)
df = df.select("id", "name", "email")
df = df.groupby("region").agg(count("*").alias("count"))
df = df.orderBy(desc("count"))
```

---

## 10. PySpark: withColumn 남용 금지 (Select-based Transformation)

**규칙**: 여러 컬럼 변환은 `select`와 표현식으로 일괄 처리, `withColumn` 연쇄 금지

```python
from pyspark.sql.functions import col, when, lit

# ✅ 올바른 예 - select와 표현식 사용
df_result = df.select(
    col("id"),
    col("name"),
    when(col("age") > 18, "adult").otherwise("minor").alias("age_group"),
    (col("salary") * 1.1).alias("salary_after_raise"),
    lit("2024").alias("year")
)

# ❌ 피해야 할 예 - withColumn 남용
df = df.withColumn("age_group", when(col("age") > 18, "adult").otherwise("minor"))
df = df.withColumn("salary_after_raise", col("salary") * 1.1)
df = df.withColumn("year", lit("2024"))
```

**이유**:
- `select`는 한 번에 모든 변환을 처리하여 성능 최적화
- `withColumn` 연쇄는 여러 번의 DataFrame 재구성으로 비효율

---

## 요약

| 항목 | 규칙 |
|------|------|
| 명명 | snake_case (함수/변수), PascalCase (클래스) |
| 타입 | 모든 함수에 타입 힌트 필수 |
| 도메인 | Dataclass/Pydantic 사용 |
| None | Optional[T] 또는 Union 명시 |
| 함수 | 단일 책임, 20줄 이하 지향 |
| 예외 | 구체적 타입 명시, bare except 금지 |
| 문자열 | f-string만 사용 |
| 메모리 | Generator/Comprehension 활용 |
| PySpark DataFrame | 불변 체인 스타일 |
| PySpark 변환 | select 기반, withColumn 최소화 |
