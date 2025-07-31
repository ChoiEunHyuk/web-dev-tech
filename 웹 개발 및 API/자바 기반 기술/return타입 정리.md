### 1. Map<String, Object>

```java

@GetMapping("/example")
public Map<String, Object> getExample() {
    Map<String, Object> response = new HashMap<>();
    response.put("message", "Hello, World!");
    response.put("status", 200);
    return response;
}

```

### **설명**

- Map<String, Object>는 JSON 형태의 응답을 생성할 때 사용됩니다.
- @RestController 또는 @ResponseBody가 붙어 있으면, 자동으로 JSON 형식으로 변환되어 반환됩니다.
- 키-값 구조로 데이터를 반환할 때 적절합니다.

### **예상 응답 (JSON)**

```json

{
  "message": "Hello, World!",
  "status": 200
}

```

---

### 2. List<String>

```java

@GetMapping("/names")
public List<String> getNames() {
    return Arrays.asList("Alice", "Bob", "Charlie");
}

```

### **설명**

- 문자열 리스트를 반환하는 방식으로, JSON 배열로 변환됩니다.
- 주로 간단한 리스트 데이터를 반환할 때 사용됩니다.

### **예상 응답 (JSON)**

```json

[
  "Alice",
  "Bob",
  "Charlie"
]

```

---

### 3. List<Map<String, Object>>

```java

@GetMapping("/users")
public List<Map<String, Object>> getUsers() {
    Map<String, Object> user1 = new HashMap<>();
    user1.put("id", 1);
    user1.put("name", "Alice");

    Map<String, Object> user2 = new HashMap<>();
    user2.put("id", 2);
    user2.put("name", "Bob");

    return Arrays.asList(user1, user2);
}

```

### **설명**

- List<Map<String, Object>>는 여러 개의 JSON 객체를 리스트 형태로 반환하는 방식입니다.
- 데이터베이스의 여러 레코드를 반환할 때 유용합니다.

### **예상 응답 (JSON)**

```json

[
  {
    "id": 1,
    "name": "Alice"
  },
  {
    "id": 2,
    "name": "Bob"
  }
]

```

---

### 📌 기타 다양한 return 타입

### 4. ResponseEntity<T>

```java

@GetMapping("/response")
public ResponseEntity<Map<String, Object>> getResponse() {
    Map<String, Object> body = new HashMap<>();
    body.put("message", "Success");

    return ResponseEntity.status(HttpStatus.OK).body(body);
}

```

- ResponseEntity<T>는 HTTP 상태 코드와 헤더를 포함하여 반환할 수 있습니다.
- API 응답을 좀 더 세밀하게 제어할 때 사용합니다.

---

### 5. String

```java

@GetMapping("/hello")
public String hello() {
    return "Hello, Spring!";
}

```

- String을 반환하면 기본적으로 텍스트로 반환됩니다.
- 하지만 @ResponseBody가 붙어있으면 JSON 문자열이 아니라, 그냥 문자열로 반환됩니다.

### **예상 응답**

```json
"Hello, Spring!"

```

---

### 6. void

```java

@PostMapping("/save")
@ResponseStatus(HttpStatus.CREATED)
public void saveData() {
    // 데이터 저장 로직
}

```

- void를 반환하면 응답 본문 없이 HTTP 상태 코드만 반환할 수 있습니다.

---

### ✅ 정리

| Return 타입 | 설명 | JSON 변환 예시 |
| --- | --- | --- |
| Map<String, Object> | JSON 객체 반환 | { "key": "value" } |
| List<String> | 문자열 리스트 반환 | ["a", "b", "c"] |
| List<Map<String, Object>> | JSON 배열 반환 | [ { "id": 1, "name": "Alice" }, { "id": 2, "name": "Bob" } ] |
| ResponseEntity<T> | HTTP 상태 코드와 응답 본문 포함 | { "message": "Success" } (with status) |
| String | 단순 텍스트 반환 | "Hello, Spring!" |
| void | 응답 본문 없이 상태 코드만 반환 | N/A |
