**HashMap**과 **EgovMap**은 유사한 개념을 가지고 있지만, **동일하지 않습니다**.

EgovMap은 **한국 전자정부 프레임워크(eGovFrame)에서 제공하는 HashMap 기반의 Map 객체**로, **HashMap을 확장(상속)하여 추가적인 기능을 제공하는 클래스**입니다

---

## **🔹 HashMap vs. EgovMap 비교**

| **구분** | **HashMap** | **EgovMap** |
| --- | --- | --- |
| **소속 패키지** | `java.util.HashMap<K, V>` | `egovframework.rte.psl.dataaccess.util.EgovMap` |
| **기반 구조** | Java 기본 `HashMap<K, V>` | `HashMap<String, Object>` 확장 |
| **Key 타입** | 일반적으로 **제네릭 타입(K, V) 사용 가능** | Key가 **항상 String 타입** |
| **Value 타입** | 모든 객체 타입 사용 가능 | 모든 객체 타입 사용 가능 (`Object`) |
| **특징** | - Java에서 제공하는 표준 컬렉션 프레임워크 - Key-Value 저장 및 검색 가능 | - **MyBatis에서 SQL 조회 결과를 쉽게 다룰 수 있도록 설계** - Key를 대소문자 구분 없이 접근 가능 |
| **활용 사례** | - 일반적인 Key-Value 데이터 저장 및 조회 | - 전자정부 프레임워크에서 DB 조회 결과를 담는 객체로 활용 |

---

## **🔹 차이점 상세 설명**

### **1. Key 타입 차이**

- **HashMap**은 **제네릭 타입(K, V)**을 사용할 수 있음.
- **EgovMap**은 항상 **`String`을 Key로 사용**함.

```java

// HashMap 예제 (Key의 타입이 Integer)
HashMap<Integer, String> hashMap = new HashMap<>();
hashMap.put(1, "Hello");
System.out.println(hashMap.get(1)); // "Hello"

// EgovMap 예제 (Key는 항상 String)
EgovMap egovMap = new EgovMap();
egovMap.put("key1", "Hello");
System.out.println(egovMap.get("key1")); // "Hello"

```

---

### **2. Key의 대소문자 구분 여부**

EgovMap은 기본적으로 Key의 **대소문자를 구분하지 않음**

→ 즉, `"key1"`과 `"KEY1"`을 동일한 Key로 인식할 수 있음.

```java

EgovMap egovMap = new EgovMap();
egovMap.put("username", "JohnDoe");
System.out.println(egovMap.get("USERNAME")); // "JohnDoe" (대소문자 구분 없이 접근 가능)

```

반면, **HashMap은 Key의 대소문자를 구분**함.

```java

HashMap<String, String> hashMap = new HashMap<>();
hashMap.put("username", "JohnDoe");
System.out.println(hashMap.get("USERNAME")); // null (대소문자 구분)

```

---

### **3. 데이터 조회 방식**

EgovMap은 **MyBatis 등의 DB 매핑 결과를 쉽게 다루도록 설계**됨.

즉, SQL 결과를 `EgovMap` 객체에 담아서 활용하는 경우가 많음.

```java

EgovMap egovMap = new EgovMap();
egovMap.put("id", 1001);
egovMap.put("name", "Alice");

System.out.println(egovMap.get("id")); // 1001
System.out.println(egovMap.get("name")); // "Alice"

```

반면, HashMap은 일반적인 Key-Value 저장 용도로 활용됨.

---

## **🔹 결론**

✅ **HashMap**과 **EgovMap**은 **Key-Value 저장 구조**를 가지는 점에서 유사하지만,

✅ **EgovMap은 HashMap을 기반으로 확장한 전자정부 프레임워크(EgovFrame)용 특화 클래스**입니다.

✅ **EgovMap은 Key가 항상 String이며, 대소문자를 구분하지 않고, 주로 SQL 조회 결과를 다루는 데 사용**됩니다.

✅ 반면, **HashMap은 Key와 Value 타입을 자유롭게 지정할 수 있으며, 대소문자를 구분**합니다.

즉, **HashMap은 일반적인 컬렉션이며, EgovMap은 HashMap을 기반으로 만든 특수한 구현체**라고 이해하면 됩니다
