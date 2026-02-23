# 테스트 규약

테스트는 코드의 품질을 보장하고 리팩토링의 자신감을 제공합니다. 언어별로 다른 테스트 프레임워크를 사용하지만, 동일한 원칙을 따릅니다.

---

## 공통 원칙

### 1. 테스트 구조: Given-When-Then

모든 테스트는 **Given-When-Then** 패턴을 따릅니다.

```
Given: 테스트 초기 상태 설정
When:  테스트 대상 동작 실행
Then:  결과 검증
```

### 2. 테스트 대상 우선순위

1. **Entity & Value Object**: 도메인 모델 (가장 중요)
2. **Service**: 비즈니스 로직
3. **Port/Adapter**: 필요시 (주로 Mock으로 처리)
4. **Spark App**: 통합 테스트 (선택)

### 3. 환경 구분

테스트는 세 가지 환경에서 실행됩니다:

- **local (Sandbox)**: IDE에서 단위 테스트 실행 (개발자 머신)
- **local submit (CBT)**: Spark-submit으로 로컬 실행 (CI/CD)
- **Airflow 통합 (Prod)**: 프로덕션 환경 (최종 검증)

---

## Python 테스트 규약 (pytest)

### 기본 구조

```python
# tests/domain/user/test_user.py
import pytest
from moses.domain.user.domain.user import User, UserId, Email

class TestUserCreation:
    """사용자 생성 테스트"""

    def test_user_created_successfully(self):
        """Given: 유효한 정보
        When: 사용자 생성
        Then: 사용자가 정상 생성됨"""
        # Given
        user_id = UserId(1)
        name = "Alice"
        email = Email("alice@example.com")

        # When
        user = User(id=user_id, name=name, email=email)

        # Then
        assert user.id == user_id
        assert user.name == name
        assert user.email == email
```

### 테스트 파일 구성

```python
# tests/domain/user/test_user_service.py
import pytest
from unittest.mock import Mock, MagicMock
from moses.domain.user.service.user_service import UserService
from moses.domain.user.domain.user import User, UserId, Email
from moses.domain.user.port.out import UserRepository, EmailService

class TestUserService:
    """사용자 서비스 테스트"""

    @pytest.fixture
    def user_repository_mock(self):
        """Mock UserRepository"""
        return Mock(spec=UserRepository)

    @pytest.fixture
    def email_service_mock(self):
        """Mock EmailService"""
        return Mock(spec=EmailService)

    @pytest.fixture
    def user_service(self, user_repository_mock, email_service_mock):
        """UserService 인스턴스"""
        return UserService(user_repository_mock, email_service_mock)

    def test_create_user_saves_and_sends_email(
        self,
        user_service,
        user_repository_mock,
        email_service_mock
    ):
        """Given: 유효한 사용자 요청
        When: 사용자 생성
        Then: 저장하고 이메일 발송"""
        # Given
        request = CreateUserRequest(name="Alice", email="alice@example.com")

        # When
        user = user_service.create_user(request)

        # Then
        user_repository_mock.save.assert_called_once()
        email_service_mock.send.assert_called_once()
        assert user.name == "Alice"

    def test_create_user_fails_with_invalid_email(
        self,
        user_service,
        user_repository_mock
    ):
        """Given: 잘못된 이메일
        When: 사용자 생성 시도
        Then: 예외 발생"""
        # Given
        request = CreateUserRequest(name="Alice", email="invalid-email")

        # When & Then
        with pytest.raises(ValueError):
            user_service.create_user(request)

        user_repository_mock.save.assert_not_called()
```

### pytest 조직화

```python
# 클래스로 테스트 그룹화
class TestUserValidation:
    def test_valid_email(self): ...
    def test_invalid_email(self): ...

class TestUserRepository:
    def test_save_user(self): ...
    def test_find_user(self): ...

# Fixture 활용
@pytest.fixture
def user_factory():
    """사용자 생성 팩토리"""
    def _create_user(id=1, name="Alice", email="alice@example.com"):
        return User(UserId(id), name, Email(email))
    return _create_user
```

### Mock 패턴

```python
from unittest.mock import Mock, patch, MagicMock

# Mock 인터페이스
def test_service_with_mock(self):
    # Given
    mock_repo = Mock(spec=UserRepository)
    mock_repo.find_by_id.return_value = User(UserId(1), "Alice", Email("alice@example.com"))

    # When
    result = mock_repo.find_by_id(UserId(1))

    # Then
    assert result.name == "Alice"
    mock_repo.find_by_id.assert_called_once_with(UserId(1))
```

### PySpark 테스트

```python
# tests/domain/test_spark_processing.py
import pytest
from pyspark.sql import SparkSession
from moses.domain.processor import process_user_data

@pytest.fixture(scope="session")
def spark_session():
    """테스트용 Spark 세션"""
    return SparkSession.builder \
        .appName("test") \
        .master("local[1]") \
        .getOrCreate()

class TestSparkProcessing:
    def test_process_user_data(self, spark_session):
        """Given: 사용자 데이터 DataFrame
        When: 데이터 처리
        Then: 정상 변환됨"""
        # Given
        data = [
            (1, "Alice", "alice@example.com"),
            (2, "Bob", "bob@example.com"),
        ]
        df = spark_session.createDataFrame(data, ["id", "name", "email"])

        # When
        result = process_user_data(df)

        # Then
        assert result.count() == 2
        assert result.columns == ["id", "name", "email"]
```

### 실행 방법

```bash
# 모든 테스트
pytest

# 특정 클래스
pytest tests/domain/user/test_user_service.py::TestUserService

# 특정 테스트
pytest tests/domain/user/test_user_service.py::TestUserService::test_create_user_saves_and_sends_email

# 커버리지
pytest --cov=moses --cov-report=html
```

---

## Scala 테스트 규약 (AnyFunSpec)

### 기본 구조

```scala
// src/test/scala/com/moses/domain/user/UserSpec.scala
import org.scalatest.funspec.AnyFunSpec
import org.scalatest.matchers.should.Matchers

class UserSpec extends AnyFunSpec with Matchers {

  describe("User Creation") {
    it("should create a user successfully") {
      // Given
      val userId = UserId(1)
      val name = "Alice"
      val email = Email("alice@example.com")

      // When
      val user = User(userId, name, email)

      // Then
      user.id shouldBe userId
      user.name shouldBe name
      user.email shouldBe email
    }

    it("should handle optional age") {
      // Given
      val user1 = User(UserId(1), "Alice", Email("alice@example.com"))
      val user2 = User(UserId(2), "Bob", Email("bob@example.com"), Some(30))

      // Then
      user1.age shouldBe None
      user2.age shouldBe Some(30)
    }
  }

  describe("Email Validation") {
    it("should validate correct email") {
      Email("alice@example.com").isValid shouldBe true
    }

    it("should reject invalid email") {
      Email("invalid-email").isValid shouldBe false
    }
  }
}
```

### Service 테스트

```scala
// src/test/scala/com/moses/domain/user/UserServiceSpec.scala
import org.scalatest.funspec.AnyFunSpec
import org.scalatest.matchers.should.Matchers
import org.scalatestplus.mockito.MockitoSugar

class UserServiceSpec extends AnyFunSpec with Matchers with MockitoSugar {

  describe("UserService") {

    describe("createUser") {
      it("should save user and send email") {
        // Given
        val mockRepository = mock[UserRepository]
        val mockEmailService = mock[EmailService]
        val service = UserService(mockRepository, mockEmailService)

        val request = CreateUserRequest("Alice", "alice@example.com")
        val expectedUser = User(UserId(1), "Alice", Email("alice@example.com"))

        when(mockRepository.save(any[User])).thenReturn(())
        when(mockEmailService.send(any[Email], any[String], any[String])).thenReturn(())

        // When
        val result = service.createUser(request)

        // Then
        result.name shouldBe "Alice"
        verify(mockRepository).save(any[User])
        verify(mockEmailService).send(any[Email], any[String], any[String])
      }

      it("should fail with invalid email") {
        // Given
        val mockRepository = mock[UserRepository]
        val mockEmailService = mock[EmailService]
        val service = UserService(mockRepository, mockEmailService)

        val request = CreateUserRequest("Alice", "invalid-email")

        // When & Then
        assertThrows[IllegalArgumentException] {
          service.createUser(request)
        }

        verify(mockRepository, never).save(any[User])
      }
    }
  }
}
```

### Fixture와 Setup/Teardown

```scala
class UserRepositorySpec extends AnyFunSpec with Matchers with BeforeAndAfterEach {

  var repository: UserRepository = _
  var testUser: User = _

  override def beforeEach(): Unit = {
    repository = new InMemoryUserRepository()
    testUser = User(UserId(1), "Alice", Email("alice@example.com"))
  }

  override def afterEach(): Unit = {
    repository.clear()
  }

  describe("UserRepository") {
    it("should save and retrieve user") {
      // Given
      repository.save(testUser)

      // When
      val result = repository.findById(UserId(1))

      // Then
      result shouldBe Some(testUser)
    }
  }
}
```

### Property-Based Testing (선택)

```scala
import org.scalacheck.Gen
import org.scalatest.propspec.AnyPropSpec
import org.scalatestplus.scalacheck.ScalaCheckPropertyChecks

class UserPropertySpec
  extends AnyPropSpec
  with ScalaCheckPropertyChecks
  with Matchers {

  property("User ID should always be positive") {
    forAll(Gen.posNum[Int]) { id =>
      val user = User(UserId(id), "Alice", Email("alice@example.com"))
      user.id.value should be > 0
    }
  }
}
```

### Spark 테스트

```scala
// src/test/scala/com/moses/domain/processor/DatasetProcessorSpec.scala
import org.scalatest.funspec.AnyFunSpec
import org.apache.spark.sql.SparkSession
import org.apache.spark.sql.Dataset

class DatasetProcessorSpec extends AnyFunSpec {

  val spark = SparkSession.builder()
    .appName("test")
    .master("local[1]")
    .getOrCreate()

  import spark.implicits._

  describe("DatasetProcessor") {
    it("should process user data correctly") {
      // Given
      val data = Seq(
        User(UserId(1), "Alice", Email("alice@example.com")),
        User(UserId(2), "Bob", Email("bob@example.com"))
      )
      val ds: Dataset[User] = data.toDS()

      // When
      val result = ds.filter(_.id.value > 0).collect()

      // Then
      result should have size 2
    }
  }
}
```

### 실행 방법

```bash
# 모든 테스트
sbt test

# 특정 스펙
sbt testOnly com.moses.domain.user.UserServiceSpec

# 커버리지
sbt coverage test coverageReport
```

---

## 테스트 데이터 팩토리

### Python

```python
# tests/fixtures/user_factory.py
from moses.domain.user.domain.user import User, UserId, Email

class UserFactory:
    @staticmethod
    def create_user(
        id: int = 1,
        name: str = "Alice",
        email: str = "alice@example.com",
        age: int = None
    ) -> User:
        return User(
            id=UserId(id),
            name=name,
            email=Email(email),
            age=age
        )
```

### Scala

```scala
// src/test/scala/com/moses/domain/user/UserFactory.scala
object UserFactory {
  def createUser(
    id: Int = 1,
    name: String = "Alice",
    email: String = "alice@example.com",
    age: Option[Int] = None
  ): User = {
    User(UserId(id), name, Email(email), age)
  }
}
```

---

## 커버리지 기준

| 계층 | 커버리지 | 이유 |
|------|---------|------|
| Domain | ≥ 90% | 비즈니스 로직이므로 높은 커버리지 필요 |
| Service | ≥ 85% | 복잡한 로직이므로 높은 커버리지 필요 |
| Adapter | ≥ 60% | 외부 기술에 의존하므로 낮아도 됨 |
| Spark App | ≥ 50% | 통합 테스트, 선택적 |

---

## CI/CD 통합

### 로컬 개발
```bash
# IDE에서 단위 테스트 실행
pytest  # Python
sbt test  # Scala
```

### CBT (CI/CD)
```bash
# docker/cbt-entrypoint.sh
set -e

# 테스트 실행
pytest --cov=moses

# Spark 로컬 제출 테스트
spark-submit \
  --class com.moses.app.UserProcessingApp \
  --master local[4] \
  target/moses-spark.jar
```

### Airflow 통합
```python
# dags/user_processing_dag.py
from airflow import DAG
from airflow.operators.spark_submit_operator import SparkSubmitOperator

spark_task = SparkSubmitOperator(
    task_id='user_processing',
    application='s3://bucket/moses-spark.jar',
    conf={'spark.executor.instances': '10'},
    spark_binary='/opt/spark/bin/spark-submit'
)
```

---

## 요약

| 항목 | Python | Scala |
|------|--------|-------|
| 프레임워크 | pytest | AnyFunSpec |
| 구조 | Given-When-Then | describe/it |
| Mock | unittest.mock | MockitoSugar |
| 패턴 | Fixture + Class | BeforeAndAfter + Class |
| 실행 | pytest | sbt test |

모든 테스트는 **Given-When-Then** 패턴을 따르고, **Domain → Service** 순서로 작성합니다.
