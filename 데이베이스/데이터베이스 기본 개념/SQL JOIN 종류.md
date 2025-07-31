## SQL JOIN 종류와 상세 설명

### 1. INNER JOIN (내부 조인)

- **설명**: 두 테이블 간 조건에 일치하는 레코드만 반환.
- **특징**: 양쪽 모두에서 조건이 만족되는 경우만 결과에 포함.
- **활용 예시**: 고객 테이블과 주문 테이블에서 주문이 있는 고객만 조회.

```sql
SELECT *
FROM A
INNER JOIN B
  ON A.key = B.key;
```

### 2. LEFT JOIN (LEFT OUTER JOIN)

- **설명**: 왼쪽 테이블의 모든 레코드를 반환하고, 오른쪽 테이블에 일치하는 레코드가 없으면 NULL.
- **특징**: 왼쪽 테이블을 기준으로 모든 값을 보장하고, 오른쪽 테이블은 일치할 때만 채워짐.
- **활용 예시**: 전체 고객 목록과 그들의 주문 정보를 보고, 주문이 없는 고객도 포함.

```sql
SELECT *
FROM A
LEFT JOIN B
  ON A.key = B.key;
```

### 3. RIGHT JOIN (RIGHT OUTER JOIN)

- **설명**: 오른쪽 테이블의 모든 레코드를 반환하고, 왼쪽 테이블에 일치하는 레코드가 없으면 NULL.
- **특징**: RIGHT JOIN은 LEFT JOIN의 반대. 오른쪽 테이블의 데이터를 모두 보여주는 것이 목적.
- **활용 예시**: 전체 주문 목록과 그 주문한 고객을 보고, 고객 정보가 없는 주문도 포함.

```sql
SELECT *
FROM A
RIGHT JOIN B
  ON A.key = B.key;
```

### 4. FULL OUTER JOIN

- **설명**: LEFT JOIN + RIGHT JOIN을 합친 결과. 양쪽 테이블 모두에서 일치하지 않는 것도 포함.
- **특징**: 둘 중 하나라도 값이 있으면 결과에 포함되고, 매칭 안 되는 부분은 NULL로 표시.
- **활용 예시**: 고객과 주문 모두를 보고, 고객이 없어도 주문이 없어도 모두 보고 싶을 때.

```sql
SELECT *
FROM A
FULL OUTER JOIN B
  ON A.key = B.key;
```

### 5. CROSS JOIN

- **설명**: 두 테이블의 모든 레코드를 곱집합으로 반환 (모든 조합).
- **특징**: WHERE 절이 없으면 모든 레코드 조합을 가져오며, 대량의 데이터가 생성될 수 있으므로 주의.
- **활용 예시**: 제품 테이블과 색상 테이블에서 가능한 모든 제품-색상 조합 만들기.

```sql
SELECT *
FROM A
CROSS JOIN B;
```

### 6. SELF JOIN

- **설명**: 같은 테이블을 스스로 조인.
- **특징**: 테이블에 별칭을 주고 두 번 참조하여 부모-자식 관계나 계층형 데이터를 표현.
- **활용 예시**: 조직도에서 상사와 부하 관계 찾기.

```sql
SELECT a.*, b.*
FROM Employee a
JOIN Employee b
  ON a.ManagerID = b.EmployeeID;
```

### 7. OUTER APPLY (Oracle 12c+, SQL Server)

- **설명**: 테이블마다 다른 서브쿼리를 동적으로 실행하고 결과를 붙임. 없는 경우 NULL 반환.
- **특징**: LEFT JOIN + 동적 서브쿼리 역할. 테이블의 각 행별로 서브쿼리가 실행됨.
- **활용 예시**: 고객마다 가장 최근 주문만 가져오기.

```sql
SELECT A.*, C.*
FROM A
OUTER APPLY (
    SELECT TOP 1 *
    FROM C
    WHERE C.A_ID = A.ID
    ORDER BY C.OrderDate DESC
) C;
```

### 8. CROSS APPLY (Oracle 12c+, SQL Server)

- **설명**: INNER JOIN + 동적 서브쿼리 역할. 매칭 안 되면 제외.
- **특징**: OUTER APPLY와 동일하지만, 매칭이 없으면 아예 결과에서 제외됨.
- **활용 예시**: 고객마다 주문이 있는 경우만 가장 최근 주문을 가져오기.

```sql
SELECT A.*, C.*
FROM A
CROSS APPLY (
    SELECT TOP 1 *
    FROM C
    WHERE C.A_ID = A.ID
    ORDER BY C.OrderDate DESC
) C;
```

---

## 요약 비교

| JOIN 종류 | 설명 | NULL 발생 가능 |
| --- | --- | --- |
| INNER JOIN | 조건 일치 시에만 반환 | 없음 |
| LEFT JOIN | 왼쪽 기준, 오른쪽 없으면 NULL | 있음 |
| RIGHT JOIN | 오른쪽 기준, 왼쪽 없으면 NULL | 있음 |
| FULL OUTER JOIN | 양쪽 모두 포함, 없으면 NULL | 있음 |
| CROSS JOIN | 모든 조합 (곱집합) | 없음 |
| SELF JOIN | 자기 자신과 JOIN | 조건에 따라 |
| OUTER APPLY | 동적 서브쿼리, 없으면 NULL | 있음 |
| CROSS APPLY | 동적 서브쿼리, 매칭 없으면 제외 | 없음 |
