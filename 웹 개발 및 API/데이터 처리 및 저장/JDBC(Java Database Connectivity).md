# **JDBC(Java Database Connectivity)란?**

**JDBC(Java Database Connectivity)** 는 Java 애플리케이션과 관계형 데이터베이스(RDBMS) 간의 **연결(Connection), 쿼리 실행, 데이터 처리** 등을 담당하는 API입니다. Java에서 데이터베이스와 상호작용하는 표준 인터페이스 역할을 합니다.

---

## **1. JDBC의 주요 개념**

JDBC는 **Java 애플리케이션 ↔ DBMS** 간의 연결을 제공하며, 아래와 같은 역할을 합니다.

✅ **DB 연결**: Java 애플리케이션에서 MySQL, Oracle, PostgreSQL 등의 데이터베이스와 연결

✅ **SQL 실행**: SELECT, INSERT, UPDATE, DELETE 등의 SQL 실행

✅ **결과 처리**: SQL 실행 후 반환된 결과(ResultSet)를 받아서 Java에서 사용

✅ **트랜잭션 관리**: commit(), rollback() 등을 통해 트랜잭션 제어

✅ **연결 해제**: 사용이 끝난 데이터베이스 연결을 닫음

---

## **2. JDBC 동작 흐름**

JDBC를 이용해 데이터베이스와 상호작용하는 기본적인 **절차**는 다음과 같습니다.

1️⃣ **JDBC 드라이버 로드** → Class.forName("com.mysql.cj.jdbc.Driver");

2️⃣ **데이터베이스 연결(Connection 생성)** → DriverManager.getConnection(url, user, password);

3️⃣ **SQL 실행(Statement 또는 PreparedStatement 사용)**

4️⃣ **결과(ResultSet) 처리**

5️⃣ **자원 해제 (close)**

---

## **3. JDBC 기본 코드 예제 (MySQL 예시)**

아래 예제는 Java에서 JDBC를 이용하여 **MySQL 데이터베이스에 연결**하고, 데이터를 조회하는 코드입니다.

```java

import java.sql.*;

public class JdbcExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydb"; // DB URL
        String user = "root";  // DB 사용자명
        String password = "1234"; // DB 비밀번호

        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;

        try {
            // 1. JDBC 드라이버 로드
            Class.forName("com.mysql.cj.jdbc.Driver");

            // 2. 데이터베이스 연결
            conn = DriverManager.getConnection(url, user, password);
            System.out.println("DB 연결 성공!");

            // 3. SQL 실행
            String sql = "SELECT id, name FROM users";
            stmt = conn.createStatement();
            rs = stmt.executeQuery(sql);

            // 4. 결과 처리
            while (rs.next()) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                System.out.println("ID: " + id + ", Name: " + name);
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 5. 자원 해제 (close)
            try {
                if (rs != null) rs.close();
                if (stmt != null) stmt.close();
                if (conn != null) conn.close();
                System.out.println("DB 연결 해제!");
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}

```

### **📌 실행 흐름**

1. MySQL JDBC 드라이버를 로드 (Class.forName(...))
2. DriverManager.getConnection(...) 으로 데이터베이스 연결
3. Statement 객체를 생성하고 SQL 쿼리를 실행 (executeQuery())
4. ResultSet 을 순회하며 데이터 조회
5. close() 를 호출하여 자원 해제

---

## **4. JDBC 주요 인터페이스 및 클래스**

JDBC를 사용할 때 주요한 클래스 및 인터페이스는 다음과 같습니다.

### **1️⃣ DriverManager**

- 데이터베이스와 연결을 생성하는 역할.
- getConnection(url, user, password)를 호출하여 데이터베이스 연결을 가져옴.

```java

Connection conn = DriverManager.getConnection(url, user, password);

```

### **2️⃣ Connection**

- 데이터베이스와의 연결을 나타내는 객체.
- SQL 실행을 위한 Statement, PreparedStatement 생성 가능.
- 트랜잭션을 제어 (commit(), rollback()).

```java

Connection conn = DriverManager.getConnection(url, user, password);
conn.setAutoCommit(false);  // 자동 커밋 해제 (트랜잭션 직접 관리)
conn.commit();  // 변경사항 저장
conn.rollback();  // 변경사항 취소

```

### **3️⃣ Statement**

- SQL 쿼리를 실행하는 객체 (executeQuery(), executeUpdate()).
- 보안 문제가 있을 수 있어 PreparedStatement 사용이 권장됨.

```java

Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery("SELECT * FROM users");

```

### **4️⃣ PreparedStatement (권장)**

- **?** 를 사용하여 SQL을 미리 컴파일 → **SQL 인젝션 방지 가능**.
- **성능 최적화** (반복 실행 시 SQL을 재사용).

```java

PreparedStatement pstmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
pstmt.setInt(1, 10);  // 첫 번째 ? 에 10을 바인딩
ResultSet rs = pstmt.executeQuery();

```

### **5️⃣ ResultSet**

- SQL 실행 결과를 저장하는 객체.
- next()를 호출하여 행을 하나씩 읽어옴.

```java

while (rs.next()) {
    int id = rs.getInt("id");
    String name = rs.getString("name");
}

```

---

## **5. JDBC 트랜잭션 처리**

트랜잭션(여러 개의 SQL 작업을 하나의 단위로 묶음)은 commit() 또는 rollback()을 이용해 관리합니다.

```java

try {
    conn.setAutoCommit(false); // 자동 커밋 해제

    PreparedStatement pstmt1 = conn.prepareStatement("INSERT INTO users (name) VALUES (?)");
    pstmt1.setString(1, "Alice");
    pstmt1.executeUpdate();

    PreparedStatement pstmt2 = conn.prepareStatement("UPDATE users SET name = ? WHERE id = ?");
    pstmt2.setString(1, "Bob");
    pstmt2.setInt(2, 10);
    pstmt2.executeUpdate();

    conn.commit();  // 모든 작업 정상 수행 시 커밋
} catch (Exception e) {
    conn.rollback();  // 오류 발생 시 롤백
    e.printStackTrace();
}

```

---

## **6. JDBC 연결 풀(Connection Pool)**

JDBC는 **매번 DB 연결을 생성하면 성능이 저하**되므로, **"커넥션 풀(Connection Pool)"** 을 이용하는 것이 일반적입니다.

✅ **HikariCP (Spring 기본 지원)**

✅ **Apache DBCP**

✅ **C3P0**

Spring Boot에서는 spring.datasource.hikari 설정을 사용하면 HikariCP를 쉽게 사용할 수 있습니다.

```

# application.properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=1234
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.hikari.maximum-pool-size=10

```

---

## **7. JDBC vs Spring JDBC vs JPA 비교**

|  | JDBC | Spring JDBC | JPA |
| --- | --- | --- | --- |
| 코드량 | 많음 (SQL 직접 작성) | 줄어듦 (템플릿 제공) | 최소 (자동 SQL 생성) |
| SQL 작성 | 직접 작성 | 직접 작성 | 자동 생성 가능 |
| 트랜잭션 관리 | 수동 (`commit()`, `rollback()`) | Spring이 관리 | Spring이 관리 |
| 유지보수성 | 낮음 | 높음 | 매우 높음 |
| 사용 예시 | 빠른 실행이 필요한 경우 | SQL 직접 제어 필요 | 객체 중심 개발 |

✅ JDBC는 **빠르지만 코드가 길고 유지보수가 어렵기 때문에**, 보통 Spring JDBC 또는 JPA를 선호합니다.

---

## **8. 결론**

- **JDBC(Java Database Connectivity)** 는 Java에서 데이터베이스와 상호작용하는 표준 API.
- 기본적으로 Connection, Statement, PreparedStatement, ResultSet 등을 사용.
- 보안과 성능을 위해 **PreparedStatement**와 **Connection Pool** 사용 권장.
- 대규모 프로젝트에서는 유지보수를 고려하여 **Spring JDBC** 또는 **JPA** 사용을 추천.
