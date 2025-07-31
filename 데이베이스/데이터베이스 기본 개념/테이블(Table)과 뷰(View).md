### **🔹 일반 테이블(Table)과 뷰(View)의 차이점**

데이터베이스에서 **일반 테이블(Table)**과 **뷰(View Table)**는 둘 다 데이터를 조회할 때 사용되지만, **저장 방식과 활용 방법에서 큰 차이점**이 있습니다.

---

## **🔹 1. 일반 테이블(Table)**

### ✅ **정의**

- 실제 **데이터를 저장하는 물리적인 객체**.
- `INSERT`, `UPDATE`, `DELETE`를 통해 데이터를 추가/수정/삭제할 수 있음.
- 기본 키(Primary Key)와 인덱스(Index)를 설정하여 데이터 무결성을 유지 가능.

### ✅ **예제**

```sql

CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);

```

- `users` 테이블은 실제 데이터를 저장하는 공간을 가짐.

### ✅ **특징**

✔ **실제 데이터가 저장됨**

✔ CRUD(INSERT, UPDATE, DELETE) 가능

✔ 테이블 크기에 따라 검색 속도가 결정됨

✔ 인덱스를 추가하여 검색 성능을 향상 가능

---

## **🔹 2. 뷰(View Table)**

### ✅ **정의**

- **기존 테이블에서 특정 데이터를 조회하는 가상의 테이블**.
- 실제 데이터를 저장하지 않고, **원본 테이블을 참조**하여 동작함.
- 테이블을 조합하여 **복잡한 쿼리를 단순화**할 때 유용함.

### ✅ **예제**

```sql

CREATE VIEW user_info AS
SELECT id, name, email FROM users;

```

- `user_info` 뷰는 `users` 테이블을 참조하여 ID, 이름, 이메일을 가져오는 가상의 테이블.

### ✅ **특징**

✔ **실제 데이터를 저장하지 않음 (가상의 테이블)**

✔ 원본 테이블이 변경되면 **뷰의 데이터도 자동 변경됨**

✔ `INSERT`, `UPDATE`, `DELETE`는 일부 제한됨

✔ 복잡한 SQL을 단순화하여 관리가 용이함

---

## **🔹 3. 일반 테이블 vs. 뷰 테이블 비교**

| **구분** | **일반 테이블 (Table)** | **뷰 테이블 (View)** |
| --- | --- | --- |
| **데이터 저장** | ✅ 실제 데이터 저장 | ❌ 데이터 저장하지 않음 (참조) |
| **데이터 변경 가능 여부** |  `INSERT`, `UPDATE`, `DELETE` 가능 | 읽기 전용 (일부 변경 가능) |
| **조회 성능** | 테이블 크기에 따라 결정 | 복잡한 쿼리를 최적화 가능 |
| **데이터 변경 시 영향** | 직접 데이터가 변경됨 | 원본 테이블이 변경되면 자동 반영 |
| **복잡한 SQL 단순화** | ❌ 직접 쿼리를 작성해야 함 | ✅ 여러 테이블을 조합하여 단순화 가능 |
| **인덱스 사용 가능 여부** | ✅ 가능 | ❌ 원본 테이블의 인덱스를 활용 |
| **실제 크기** | 데이터가 실제로 저장됨 | 크기가 없음 (참조) |

---

## **🔹 4. 뷰(View) 사용 예제**

### ✅ **1) 특정 컬럼만 조회하는 뷰**

```sql

CREATE VIEW user_summary AS
SELECT id, name FROM users;

```

- `users` 테이블의 `id`와 `name`만 조회하는 뷰 생성.

### ✅ **2) 여러 테이블을 조합하는 뷰**

```sql

CREATE VIEW order_summary AS
SELECT orders.id, users.name, orders.total_amount
FROM orders
JOIN users ON orders.user_id = users.id;

```

- `orders`와 `users` 테이블을 조인하여 **주문 정보와 사용자 이름을 함께 조회**.

### ✅ **3) 뷰를 사용한 데이터 조회**

```sql

SELECT * FROM order_summary WHERE total_amount > 50000;

```

- 뷰를 통해 고액 주문 정보를 쉽게 조회 가능.

---

## **🔹 5. 뷰(View) 데이터 변경 가능 여부**

기본적으로 **뷰는 데이터 변경이 불가능(읽기 전용)**이지만, **일부 조건을 만족하면 수정 가능**함.

### ✅ **1) 변경 가능한 뷰**

- 단일 테이블에서 생성된 뷰
- `PRIMARY KEY`를 포함하고 있어야 함
- `GROUP BY`, `JOIN`, `DISTINCT` 같은 연산이 없어야 함

✅ **가능한 예제**

```sql

CREATE VIEW editable_view AS
SELECT id, name FROM users;

```

```sql

UPDATE editable_view SET name = 'Alice' WHERE id = 1;

```

- 원본 테이블(`users`)에도 반영됨.

### ❌ **2) 변경 불가능한 뷰**

```sql

CREATE VIEW non_editable_view AS
SELECT u.id, u.name, o.total_amount
FROM users u
JOIN orders o ON u.id = o.user_id;

```

```sql

UPDATE non_editable_view SET total_amount = 100000 WHERE id = 1;
-- ❌ 오류 발생 (JOIN이 포함된 뷰는 업데이트 불가)

```

📌 **해결 방법**

뷰를 대신해서 **트리거(Trigger)를 사용하여 변경 작업을 수행**할 수도 있음.

---

## **🔹 6. 뷰(View) 삭제 및 갱신**

### ✅ **뷰 삭제**

```sql

DROP VIEW user_summary;
```

### ✅ **뷰 갱신 (기존 뷰 변경)**

```sql

CREATE OR REPLACE VIEW user_summary AS
SELECT id, name, email FROM users;

```

- 기존 뷰가 있으면 덮어쓰고 새로운 뷰를 생성.

---

## **🔹 7. 일반 테이블과 뷰의 활용 사례**

| **사용 목적** | **일반 테이블 사용** | **뷰(View) 사용** |
| --- | --- | --- |
| **데이터 저장** | ✅ 가능 | ❌ 불가능 (참조만 가능) |
| **CRUD 연산 (INSERT, UPDATE, DELETE)** | ✅ 가능 | 🔄 일부 가능 (제약 조건 있음) |
| **보안 (특정 컬럼만 노출)** | ❌ 전체 테이블을 노출해야 함 | ✅ 특정 컬럼만 조회 가능 |
| **복잡한 SQL 단순화** | ❌ 직접 JOIN을 해야 함 | ✅ 뷰를 사용하여 간단히 조회 |
| **데이터 자동 업데이트** | ❌ 직접 쿼리 실행 필요 | ✅ 원본 테이블이 변경되면 자동 반영 |

---

## **🔹 8. 결론**

✅ **일반 테이블(Table)은 실제 데이터를 저장하는 기본적인 저장소**이며, CRUD(삽입, 수정, 삭제)가 자유롭게 가능.

✅ **뷰(View)는 실제 데이터를 저장하지 않고, 기존 테이블을 기반으로 가상의 테이블을 제공**.

✅ **뷰를 사용하면 보안이 강화되며, 복잡한 SQL을 단순화할 수 있음**.

✅ **단, 뷰는 데이터 삽입/수정이 제한되며, 원본 테이블이 변경되면 자동으로 반영됨**.
