# **JPA(Java Persistence API)란?**

**JPA(Java Persistence API)** 는 **Java 애플리케이션에서 데이터베이스와 상호작용하는 ORM(Object Relational Mapping) 표준 인터페이스**입니다.

JPA는 **SQL을 직접 작성하지 않고** 객체(Entity)와 관계형 데이터베이스(RDBMS)를 매핑하여 데이터베이스를 보다 **객체 지향적으로** 다룰 수 있도록 도와줍니다.

---

## **1. JPA의 주요 개념**

✅ **SQL을 직접 사용하지 않고 객체를 사용하여 DB를 조작**

✅ **Entity(객체) ↔ Table(테이블) 자동 매핑**

✅ **트랜잭션 관리, 캐싱, 성능 최적화 지원**

✅ **JPQL(Java Persistence Query Language)을 사용하여 SQL처럼 데이터 조회 가능**

✅ **Hibernate, EclipseLink 등 다양한 구현체 존재 (Spring Boot에서는 Hibernate 기본 제공)**

---

## **2. JPA vs JDBC vs MyBatis 비교**

|  | **JDBC (Java Database Connectivity)** | **MyBatis** | **JPA (ORM)** |
| --- | --- | --- | --- |
| **코드량** | SQL 직접 작성 (많음) | SQL 직접 작성 (중간) | SQL 최소화 (적음) |
| **SQL 사용 여부** | 직접 작성 (`SELECT * FROM table`) | 직접 작성 (XML, Annotation) | 자동 생성 (JPQL, QueryDSL) |
| **트랜잭션 관리** | 직접 `commit()`, `rollback()` | 직접 `commit()`, `rollback()` | Spring이 자동 관리 |
| **쿼리 최적화** | 없음 | 없음 | `@Cacheable`, `@FetchType.LAZY` 지원 |
| **유지보수성** | 낮음 | 중간 | 높음 |
| **객체지향적 접근** | 없음 | 부분 지원 | 완전 지원 |
| **사용 예시** | SQL 제어가 필요한 경우 | 복잡한 쿼리 활용 시 | 비즈니스 로직 중심 개발 |

🚀 **JPA는 객체 중심으로 데이터를 다루므로 유지보수성이 높고 개발 속도가 빠르다는 장점이 있음!**

---

## **3. JPA 기본 사용법**

Spring Boot 환경에서 JPA를 사용하려면 **Spring Data JPA**를 함께 활용하면 더욱 편리합니다.

### **1️⃣ JPA 의존성 추가 (Spring Boot 프로젝트)**

Spring Boot에서 JPA를 사용하려면 `spring-boot-starter-data-jpa`를 추가합니다.

```xml
xml
복사편집
<!-- pom.xml -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- H2 Database (테스트용) -->
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>

```

---

### **2️⃣ 데이터베이스 설정 (application.properties)**

```

# H2 Database 설정 (테스트용)
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# JPA 설정
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

```

💡 **`ddl-auto=update`**: 애플리케이션 실행 시 테이블을 자동 생성 또는 업데이트

---

### **3️⃣ Entity (객체-테이블 매핑) 생성**

```java

import jakarta.persistence.*;

@Entity  // 데이터베이스 테이블과 매핑
@Table(name = "users")  // 테이블명 지정
public class User {

    @Id  // PK 설정
    @GeneratedValue(strategy = GenerationType.IDENTITY)  // 자동 증가(Auto Increment)
    private Long id;

    @Column(nullable = false, length = 100)  // 컬럼 설정
    private String name;

    @Column(nullable = false, unique = true)
    private String email;

    // 기본 생성자
    public User() {}

    // 생성자
    public User(String name, String email) {
        this.name = name;
        this.email = email;
    }

    // Getter, Setter
    public Long getId() { return id; }
    public String getName() { return name; }
    public String getEmail() { return email; }
    public void setName(String name) { this.name = name; }
    public void setEmail(String email) { this.email = email; }
}

```

✅ **`@Entity`**: 해당 클래스가 **데이터베이스의 테이블과 매핑**됨

✅ **`@Id`**: 기본 키(PK)

✅ **`@GeneratedValue(strategy = GenerationType.IDENTITY)`**: 자동 증가

✅ **`@Column(nullable = false, unique = true)`**: 필수 컬럼 설정

---

### **4️⃣ Repository (CRUD 자동 처리)**

JPA를 사용하면 **DAO를 직접 만들 필요 없이**, `JpaRepository` 인터페이스를 상속받아 CRUD를 자동으로 처리할 수 있습니다.

```java

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

// JPA Repository (기본적인 CRUD 자동 지원)
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    User findByEmail(String email);  // 이메일로 사용자 조회
}

```

✅ **`JpaRepository<User, Long>`**: `User` 엔티티를 다루며, 기본키 타입은 `Long`

✅ 기본적인 CRUD (`save()`, `findAll()`, `findById()`, `delete()`) 자동 제공

✅ **쿼리 메서드 (`findByEmail()`)** 를 사용하여 SQL 없이 데이터 조회 가능

---

### **5️⃣ Service (비즈니스 로직)**

```java
import org.springframework.stereotype.Service;
import java.util.List;

@Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    // 사용자 저장
    public User saveUser(String name, String email) {
        return userRepository.save(new User(name, email));
    }

    // 모든 사용자 조회
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    // 특정 사용자 조회
    public User getUserByEmail(String email) {
        return userRepository.findByEmail(email);
    }
}

```

✅ `save()`, `findAll()`, `findByEmail()` 활용하여 간결한 코드 작성 가능

---

### **6️⃣ Controller (API 제공)**

```java
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    // 모든 사용자 조회
    @GetMapping
    public List<User> getUsers() {
        return userService.getAllUsers();
    }

    // 사용자 등록
    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.saveUser(user.getName(), user.getEmail());
    }

    // 특정 사용자 조회
    @GetMapping("/{email}")
    public User getUser(@PathVariable String email) {
        return userService.getUserByEmail(email);
    }
}

```

✅ RESTful API 제공 (`GET`, `POST` 요청)

✅ `@RequestBody` 로 JSON 데이터를 받아 엔티티 생성

---

## **4. JPA의 장점 & 단점**

### **✅ 장점**

- **SQL을 직접 작성하지 않아도 됨 (객체 지향적 개발)**
- **CRUD 메서드 제공 (`save()`, `findById()`, `delete()`)**
- **JPQL, Criteria API, QueryDSL 지원 (복잡한 쿼리 가능)**
- **트랜잭션 자동 관리**
- **성능 최적화 기능 제공 (1차 캐시, 지연 로딩, 벌크 연산)**

### **⛔ 단점**

- **복잡한 SQL을 직접 최적화하기 어려움** (MyBatis보다 SQL 제어가 어려울 수 있음)
- **JPA의 학습 곡선이 높음** (객체 매핑, 지연 로딩 등 이해 필요)
- **대규모 데이터 처리 시 성능 튜닝 필요** (N+1 문제 등)

---

## **5. 결론**

JPA는 **SQL을 최소화하고 객체 중심으로 데이터베이스를 다룰 수 있도록 도와주는 ORM 기술**입니다.

Spring Boot에서는 `Spring Data JPA`를 활용하여 CRUD 및 트랜잭션 관리를 자동화할 수 있습니다.
