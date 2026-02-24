# Scala 코딩 규약 (Apache Spark)

Scala 및 Apache Spark 환경에서 팀 전체가 따를 코딩 표준입니다. 함수형 프로그래밍 패러다임을 강조하며, 타입 안전성과 명확성을 우선합니다.

## 1. val 우선, var 최소화 (Prefer val over var)

**규칙**: 기본값은 val을 사용, 변경이 반드시 필요한 경우만 var 사용

```scala
// ✅ 올바른 예
val userId: Int = 123
val user: User = fetchUser(userId)
val totalAmount: Double = calculateTotal(orders)

// var는 필요한 경우만
var retryCount: Int = 0
while (retryCount < MAX_RETRIES) {
  // 재시도 로직
  retryCount += 1
}

// ❌ 피해야 할 예
var user: User = fetchUser(userId)
var totalAmount: Double = calculateTotal(orders)
var name: String = "initial"
name = "updated"  // 불필요한 재할당
```

**이유**:
- val은 불변성을 보장하여 부수 효과 감소
- 함수형 프로그래밍 원칙에 부합
- 코드 예측 가능성 증대

---

## 2. null 금지, Option[T] 활용 (Null Safety)

**규칙**: null 사용 금지, Option[Some/None]으로 null 안전성 표현

```scala
// ✅ 올바른 예
def findUser(userId: Int): Option[User] = {
  if (userId > 0) Some(User(userId, "John"))
  else None
}

val user: Option[User] = findUser(123)
val name: String = user.map(_.name).getOrElse("Unknown")

// Option 체이닝
val result: Option[String] = findUser(123)
  .map(_.email)
  .filter(_.contains("@"))

// ❌ 피해야 할 예
def findUser(userId: Int): User = {
  if (userId > 0) User(userId, "John")
  else null
}

val user: User = findUser(123)
val name: String = if (user != null) user.name else "Unknown"
```

**Option 주요 메서드**:
- `map`: 값이 있으면 변환
- `flatMap`: 중첩된 Option 제거
- `getOrElse`: 기본값 제공
- `filter`: 조건 필터링

---

## 3. case class로 도메인 모델링 (Domain Models)

**규칙**: 도메인 객체는 case class로 정의

```scala
// ✅ 올바른 예
case class User(
  id: Int,
  name: String,
  email: String,
  age: Option[Int] = None
)

case class Order(
  id: Int,
  userId: Int,
  totalAmount: Double,
  status: String = "pending"
)

// 패턴 매칭과 자동 생성 메서드 활용
val user = User(1, "Alice", "alice@example.com", Some(30))
val User(id, name, email, age) = user  // 구조 분해

// ❌ 피해야 할 예
class User(
  val id: Int,
  val name: String,
  val email: String,
  val age: Option[Int]
) {
  // 수동 equals, hashCode, toString 구현 필요
}
```

**case class 장점**:
- 자동 생성: equals, hashCode, toString, copy
- 패턴 매칭 가능
- 불변성 기본 제공

---

## 4. for comprehension으로 모나딕 흐름 표현 (for Comprehension)

**규칙**: Option, Future, Try 등 모나드 조합은 for comprehension 사용

```scala
// ✅ 올바른 예
val result: Option[String] = for {
  user <- findUser(123)
  email <- parseEmail(user.email)
  domain <- extractDomain(email)
} yield domain

// Try를 사용한 에러 핸들링
val result: Try[Int] = for {
  str <- Try("123".toInt)
  doubled <- Try(str * 2)
} yield doubled

// Future 조합
val result: Future[String] = for {
  user <- fetchUserAsync(123)
  profile <- fetchProfileAsync(user.id)
} yield s"${user.name}: ${profile.bio}"

// ❌ 피해야 할 예 - 중첩된 flatMap
val result: Option[String] = findUser(123).flatMap { user =>
  parseEmail(user.email).flatMap { email =>
    extractDomain(email)
  }
}
```

---

## 5. Pattern Matching 활용 (Pattern Matching)

**규칙**: 조건문은 pattern matching으로 표현

```scala
// ✅ 올바른 예 - case class 분해
val response = user match {
  case User(id, name, email, Some(age)) if age > 18 =>
    s"Adult: $name ($age)"
  case User(id, name, email, Some(age)) =>
    s"Minor: $name ($age)"
  case User(id, name, email, None) =>
    s"User: $name (age unknown)"
  case _ =>
    "Unknown user"
}

// Option 매칭
val greeting = user match {
  case Some(User(_, name, _, _)) => s"Hello, $name!"
  case None => "User not found"
}

// List 패턴 매칭
val message = numbers match {
  case Nil => "Empty list"
  case head :: Nil => s"Single element: $head"
  case head :: tail => s"Head: $head, Rest: $tail"
  case _ => "Unknown"
}

// ❌ 피해야 할 예 - if-else로 표현
if (user.age.isDefined && user.age.get > 18) {
  s"Adult: ${user.name} (${user.age.get})"
} else if (user.age.isDefined) {
  s"Minor: ${user.name} (${user.age.get})"
} else {
  s"User: ${user.name} (age unknown)"
}
```

---

## 6. implicit 최소화, 명시적 scope (implicit Usage)

**규칙**: implicit 사용 최소화, 필요 시 명시적 scope와 문서화

```scala
// ✅ 올바른 예 - 명시적 implicit
object MyImplicits {
  implicit val stringOrder: Ordering[String] = Ordering.String.reverse
}

import MyImplicits._  // 명시적 import

val sorted = List("c", "a", "b").sorted

// implicit 파라미터는 명시적으로 표시
def process[T](data: Seq[T])(implicit ordering: Ordering[T]): Unit = {
  // ordering 사용
}

// ❌ 피해야 할 예 - 암묵적이고 추적 어려움
implicit val conversion: String => Int = _.toInt

// 여러 implicit가 스코프에 있음
implicit val format1: Format = JSONFormat
implicit val format2: Format = XMLFormat  // 충돌 위험
```

**암묵적 사용 가이드**:
- 타입 클래스 패턴만 사용
- Scala 표준 라이브러리의 암묵적만 의존
- 명시적 import로 의도 표현

---

## 7. 함수형 스타일 (Pure Functions)

**규칙**: 부수 효과(side effect) 최소화, 순수 함수 우선

```scala
// ✅ 올바른 예 - 순수 함수
def calculateDiscount(amount: Double, rate: Double): Double = {
  amount * (1 - rate)
}

def filterAdults(users: Seq[User]): Seq[User] = {
  users.filter(_.age.exists(_ > 18))
}

// 부수 효과는 경계에서만
def saveAndReturn(user: User): User = {
  // DB 저장 (부수 효과)
  database.save(user)
  // 순수한 값 반환
  user
}

// ❌ 피해야 할 예 - 부수 효과 있는 함수
var globalCounter = 0
def processUser(user: User): String = {
  globalCounter += 1  // 부수 효과
  database.save(user)  // 부수 효과
  println(f"Processed: $user")  // 부수 효과
  f"User ${user.id}"
}
```

**부수 효과 분리**:
- 비즈니스 로직: 순수 함수
- I/O, 상태 변경: 경계 계층으로 분리

---

## 8. Dataset 타입 안전성 활용 (Strongly Typed Spark)

**규칙**: RDD/DataFrame 대신 Dataset 사용하여 타입 안전성 확보

```scala
// ✅ 올바른 예 - Dataset
case class Transaction(id: String, amount: Double, userId: Int)

val ds: Dataset[Transaction] = spark.read
  .parquet("path/to/transactions")
  .as[Transaction]

val result = ds
  .filter(_.amount > 1000)
  .map(t => (t.userId, t.amount))
  .groupByKey(_._1)
  .mapGroups { case (userId, txns) =>
    (userId, txns.map(_._2).sum)
  }

// ❌ 피해야 할 예 - 타입 안전성 없음
val df = spark.read.parquet("path/to/transactions")
val filtered = df.filter(col("amount") > 1000)
val result = filtered.groupBy("userId").agg(sum("amount"))
// 컴파일 시간에 스키마 검증 불가
```

**Dataset 이점**:
- 컴파일 타임 타입 검사
- 런타임 에러 조기 발견
- IDE 자동완성 지원

---

## 코드 레이아웃 권장

```scala
// 임포트: 알파벳순
import scala.collection.mutable
import scala.concurrent.Future

// case class: 맨 위
case class User(id: Int, name: String, email: String)

// 객체/싱글톤
object UserService {
  def findUser(id: Int): Option[User] = ???
}

// 클래스/트레이트
class UserRepository {
  def save(user: User): Unit = ???
}
```

---

## 요약

| 항목 | 규칙 |
|------|------|
| 변수 | val 우선, var 최소화 |
| null | Option[T] 사용, null 금지 |
| 모델 | case class 활용 |
| 조합 | for comprehension 사용 |
| 조건 | Pattern matching 활용 |
| implicit | 최소화, 명시적 scope |
| 함수 | 순수 함수, 부수 효과 분리 |
| Spark | Dataset 사용, 타입 안전성 |

---

## 참고 자료

- [Scala Style Guide](https://docs.scala-lang.org/style/)
- [Scala for the Impatient](https://www.oreilly.com/library/view/scala-for-the-impatient/9780134540566/)
- [Spark Dataset API](https://spark.apache.org/docs/latest/api/scala/org/apache/spark/sql/Dataset.html)
