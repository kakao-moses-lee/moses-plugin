# 헥사고날 아키텍처 (Hexagonal Architecture)

## 개요

헥사고날 아키텍처는 "포트 & 어댑터" 패턴으로도 불리며, 비즈니스 로직(도메인)을 외부 기술(DB, API, 프레임워크)로부터 독립시키는 아키텍처입니다.

```
┌─────────────────────────────────────────┐
│        외부 시스템 (Actors)              │
│   (API Client, Database, Message Queue) │
└────────────┬────────────────────────────┘
             │
        ┌────▼─────┐
        │ Adapters  │  ◄─ 외부 기술 통합
        └────┬─────┘
             │
     ┌───────▼────────┐
     │ Ports (분기점) │  ◄─ 기술 독립적 인터페이스
     └───────┬────────┘
             │
    ┌────────▼──────────┐
    │   Domain Core     │  ◄─ 순수 비즈니스 로직
    │  (Entity, Service)│
    └───────────────────┘
```

---

## 패키지 구조

```
src/main/
├── scala/
│   └── com/moses/
│       └── domain/
│           ├── atom/                 # 가장 작은 단위 (primitive value objects)
│           │   └── ...
│           │
│           ├── tag/                  # 한 개의 entity를 나타냄
│           │   └── User/
│           │       ├── domain/
│           │       │   ├── User.scala          (Entity)
│           │       │   └── UserId.scala        (Value Object)
│           │       ├── port/
│           │       │   ├── in/
│           │       │   │   ├── CreateUserUseCase.scala
│           │       │   │   └── GetUserUseCase.scala
│           │       │   └── out/
│           │       │       ├── UserRepository.scala
│           │       │       └── UserValidator.scala
│           │       ├── service/
│           │       │   └── UserService.scala
│           │       └── adapter/
│           │           ├── in/
│           │           │   ├── rest/
│           │           │   │   └── UserRestController.scala
│           │           │   └── messaging/
│           │           │       └── UserEventConsumer.scala
│           │           └── out/
│           │               ├── persistence/
│           │               │   └── JdbcUserRepository.scala
│           │               └── external/
│           │                   └── EmailServiceAdapter.scala
│           │
│           ├── aggregation/          # 여러 entity의 조합
│           │   └── Order/
│           │       ├── domain/
│           │       ├── port/in
│           │       ├── port/out
│           │       ├── service/
│           │       └── adapter/
│           │
│           ├── shared/               # 공유 컴포넌트
│           │   ├── domain/
│           │   │   ├── DomainException.scala
│           │   │   └── PageRequest.scala
│           │   ├── port/
│           │   │   └── LoggerPort.scala
│           │   └── adapter/
│           │       └── LoggerAdapter.scala
│           │
│           └── config/               # 스파크 앱 설정
│               ├── SparkConfig.scala
│               └── ApplicationConfig.scala
│
└── python/                           # Python/PySpark 구조
    └── moses/
        └── domain/
            └── [동일한 구조 적용]
```

---

## 각 레이어 역할

### 1. Domain (도메인 계층)

**역할**: 순수 비즈니스 로직, 외부 기술에 의존하지 않음

**구성**:
- **Entity**: 비즈니스 개념을 나타내는 객체 (identity 보유)
- **Value Object**: 속성을 표현하는 불변 객체 (identity 없음)
- **DTO**: 레이어 간 데이터 전달 객체

```scala
// Entity: 고유 ID를 가짐
case class User(
  id: UserId,
  name: String,
  email: Email
)

// Value Object: id 없음, 자신의 값으로 동등성 판정
case class UserId(value: Int)
case class Email(value: String)

// DTO: 전달용 데이터
case class CreateUserRequest(
  name: String,
  email: String
)
```

**주의**:
- 외부 라이브러리 임포트 금지 (javax.*, org.springframework.* 등)
- 순수 함수 로직만 포함
- 부수 효과 없음

---

### 2. Service (서비스 계층)

**역할**: 도메인 로직의 복잡한 조합, 여러 entity 간의 상호작용

```scala
class UserService(
  userValidator: UserValidator,        // port/out
  emailService: EmailService           // port/out
) {
  def createUser(request: CreateUserRequest): User = {
    // 유효성 검사
    val email = Email(request.email)
    userValidator.validate(email)

    // 도메인 모델 생성
    val userId = UserId(generateId())
    val user = User(userId, request.name, email)

    // 이메일 발송 (port 호출)
    emailService.sendWelcomeEmail(user)

    user
  }
}
```

**특징**:
- Port(인터페이스)에 의존
- 도메인 로직 조합
- 트랜잭션 관리

---

### 3. Port (포트 = 인터페이스)

포트는 외부와의 연결점입니다. **방향성에 따라 두 가지로 분류**:

#### 3.1 Inbound Port (사용 케이스 인터페이스)

**역할**: 외부 요청을 도메인 로직으로 변환

```scala
// port/in/CreateUserUseCase.scala
trait CreateUserUseCase {
  def execute(request: CreateUserRequest): User
}

// port/in/GetUserUseCase.scala
trait GetUserUseCase {
  def execute(userId: UserId): Option[User]
}
```

**사용**:
- REST Controller가 implement
- Messaging Consumer가 implement

#### 3.2 Outbound Port (의존성 인터페이스)

**역할**: 도메인이 필요한 외부 서비스 정의

```scala
// port/out/UserRepository.scala
trait UserRepository {
  def save(user: User): Unit
  def findById(id: UserId): Option[User]
  def delete(id: UserId): Unit
}

// port/out/EmailService.scala
trait EmailService {
  def send(to: Email, subject: String, body: String): Unit
}

// port/out/UserValidator.scala
trait UserValidator {
  def validate(email: Email): Boolean
}
```

**특징**:
- 도메인 관점에서 정의 (기술 중립)
- Service에서만 사용
- Adapter에서 구현

---

### 4. Adapter (어댑터 = 구현)

**역할**: Port의 구체적 구현, 외부 기술 담당

#### 4.1 Inbound Adapter (진입점)

```scala
// adapter/in/rest/UserRestController.scala
class UserRestController(
  createUserUseCase: CreateUserUseCase,
  getUserUseCase: GetUserUseCase
) {
  def post(request: CreateUserRequest): UserResponse = {
    val user = createUserUseCase.execute(request)
    UserResponse.from(user)
  }

  def get(userId: String): UserResponse = {
    val user = getUserUseCase.execute(UserId(userId.toInt))
    UserResponse.from(user)
  }
}

// adapter/in/messaging/UserEventConsumer.scala
class UserEventConsumer(
  createUserUseCase: CreateUserUseCase
) {
  def onUserCreated(event: UserCreatedEvent): Unit = {
    createUserUseCase.execute(event.toRequest)
  }
}
```

#### 4.2 Outbound Adapter (외부 의존성)

```scala
// adapter/out/persistence/JdbcUserRepository.scala
class JdbcUserRepository(dataSource: DataSource)
  extends UserRepository {

  override def save(user: User): Unit = {
    val conn = dataSource.getConnection
    val stmt = conn.prepareStatement("INSERT INTO users ...")
    stmt.setString(1, user.id.value.toString)
    stmt.setString(2, user.name)
    stmt.executeUpdate()
  }

  override def findById(id: UserId): Option[User] = {
    val conn = dataSource.getConnection
    val stmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?")
    stmt.setString(1, id.value.toString)
    val rs = stmt.executeQuery()
    if (rs.next()) Some(User(...)) else None
  }
}

// adapter/out/external/SmtpEmailServiceAdapter.scala
class SmtpEmailServiceAdapter(smtpConfig: SmtpConfig)
  extends EmailService {

  override def send(to: Email, subject: String, body: String): Unit = {
    val message = new MimeMessage(Session.getInstance(smtpConfig.props))
    message.setRecipients(Message.RecipientType.TO, to.value)
    message.setSubject(subject)
    message.setText(body)
    Transport.send(message)
  }
}
```

---

### 5. Spark App (진입점)

```scala
// SparkApp.scala
object UserProcessingApp extends App {
  // 설정 로드
  val config = ApplicationConfig.load()

  // Spark 세션 생성
  implicit val spark: SparkSession = SparkSession.builder()
    .appName("UserProcessing")
    .config("spark.sql.adaptive.enabled", "true")
    .getOrCreate()

  // Adapter 인스턴스화 (의존성 주입)
  val userRepository = JdbcUserRepository(config.dataSource)
  val emailService = SmtpEmailServiceAdapter(config.smtpConfig)
  val userValidator = DefaultUserValidator()

  // Service 생성
  val userService = UserService(userValidator, emailService)

  // UseCase 구현
  val createUserUseCase = new CreateUserUseCase {
    override def execute(request: CreateUserRequest): User = {
      userService.createUser(request)
    }
  }

  // 메인 로직
  val userDF = spark.read.parquet("path/to/users")
  val result = userDF
    .map(row => createUserUseCase.execute(row.toRequest))
    .coalesce(1)
    .write
    .parquet("path/to/output")

  spark.stop()
}
```

---

### 6. Spark App Config (환경 설정)

```scala
// config/ApplicationConfig.scala
case class ApplicationConfig(
  dataSource: DataSource,
  smtpConfig: SmtpConfig,
  appName: String,
  environment: String
)

object ApplicationConfig {
  def load(): ApplicationConfig = {
    val env = sys.env.getOrElse("ENV", "dev")
    val configFile = s"application-$env.conf"

    // 설정 파일 로드 (예: typesafe config)
    val config = ConfigFactory.load(configFile)

    ApplicationConfig(
      dataSource = createDataSource(config),
      smtpConfig = SmtpConfig(
        host = config.getString("smtp.host"),
        port = config.getInt("smtp.port")
      ),
      appName = config.getString("app.name"),
      environment = env
    )
  }

  private def createDataSource(config: Config): DataSource = {
    val ds = new HikariDataSource()
    ds.setJdbcUrl(config.getString("db.url"))
    ds.setUsername(config.getString("db.user"))
    ds.setPassword(config.getString("db.password"))
    ds
  }
}
```

---

## 의존성 방향 규칙

```
     ┌─────────────────────┐
     │   Spark App Config  │
     └──────────┬──────────┘
                │
     ┌──────────▼──────────┐
     │   Spark App         │  ◄─ 설정을 사용
     └──────────┬──────────┘
                │
     ┌──────────▼──────────┐
     │  In/Out Adapter     │  ◄─ 진입점 & 외부 기술
     └──────────┬──────────┘
                │
     ┌──────────▼──────────┐
     │  Port Interface     │  ◄─ 기술 중립 계약
     └──────────┬──────────┘
                │
     ┌──────────▼──────────┐
     │  Service & Domain   │  ◄─ 순수 비즈니스 로직
     └─────────────────────┘
```

**핵심 규칙**:
1. **안쪽 → 바깥쪽 의존 금지**: Domain은 Service, Port를 모르고, Service는 Adapter를 모른다
2. **의존성 역전**: Service는 Port 인터페이스에만 의존
3. **기술 중립**: Domain 계층에 외부 라이브러리 임포트 금지

---

## 명명 규칙

### Port 명명

```scala
// Inbound: UseCase 접미사
trait CreateUserUseCase
trait GetUserUseCase
trait UpdateUserUseCase

// Outbound: Service/Repository 접미사
trait UserRepository
trait EmailService
trait LoggerPort
```

### Adapter 명명

```scala
// Inbound: 기술명 접미사
class UserRestController      // REST 진입점
class UserEventConsumer       // 메시징 진입점
class UserCliCommand          // CLI 진입점

// Outbound: 기술명 + Port명
class JdbcUserRepository      // JDBC 기반 저장소
class HttpEmailService        // HTTP 기반 이메일
class CloudStorageAdapter     // 클라우드 저장소
```

---

## 예시: 전체 흐름

1. **REST 요청 수신**
   ```
   POST /users → UserRestController
   ```

2. **UseCase 실행**
   ```
   UserRestController → CreateUserUseCase.execute()
   ```

3. **Service 로직**
   ```
   Service.createUser()
     ├─ Port: UserValidator.validate()
     ├─ Domain: User 생성
     ├─ Port: UserRepository.save()
     └─ Port: EmailService.send()
   ```

4. **Adapter 실행**
   ```
   JdbcUserRepository.save()     ← DB 저장
   SmtpEmailService.send()       ← 이메일 발송
   ```

5. **응답 반환**
   ```
   UserResponse → HTTP 200
   ```

---

## Python/PySpark 구조

Python은 Scala와 동일한 구조를 따르되, Python의 관례를 유지합니다:

```python
# domain/user/domain/user.py
from dataclasses import dataclass
from typing import Optional

@dataclass
class User:
    id: int
    name: str
    email: str
    age: Optional[int] = None

# domain/user/port/out.py
from abc import ABC, abstractmethod

class UserRepository(ABC):
    @abstractmethod
    def save(self, user: User) -> None:
        pass

# domain/user/service/user_service.py
class UserService:
    def __init__(self, validator, email_service):
        self.validator = validator
        self.email_service = email_service

    def create_user(self, request):
        # 서비스 로직
        pass
```

---

## 요약

| 계층 | 역할 | 외부 의존 | 예시 |
|------|------|---------|------|
| Domain | 비즈니스 로직 | ✗ | User, Email |
| Service | 로직 조합 | Port만 | UserService |
| Port | 인터페이스 | ✗ | UserRepository |
| Adapter | 구현 | ✓ | JdbcUserRepository |
| Spark App | 진입점 | ✓ | App, Config |

---

## 참고 자료

- [Hexagonal Architecture - Alistair Cockburn](https://alistair.cockburn.us/hexagonal-architecture/)
- [Clean Architecture - Robert C. Martin](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
