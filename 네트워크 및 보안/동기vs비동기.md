## **1. 동기(Synchronous) 방식**

동기 방식에서는 하나의 요청을 처리하는 동안 다른 요청은 대기해야 합니다. 즉, 현재 실행 중인 작업이 완료되어야만 다음 작업이 실행됩니다.

### 🔹 동기 방식의 특징

- 요청을 처리하는 동안 해당 스레드는 블로킹됨.
- 순차적으로 실행되므로 코드의 흐름이 단순하고 이해하기 쉬움.
- 하지만 네트워크 요청이나 데이터베이스 조회처럼 시간이 걸리는 작업이 있으면 성능 저하가 발생할 수 있음.

---

### **📌 동기 방식 예제 (Spring Boot REST API)**

```java

@RestController
@RequestMapping("/sync")
public class SyncController {
    @GetMapping("/task")
    public String syncTask() throws InterruptedException {
        System.out.println("작업 시작");
        Thread.sleep(5000); // 5초 대기 (블로킹)
        System.out.println("작업 완료");
        return "동기 작업 완료!";
    }
}

```

**📌 실행 흐름**

1. `/sync/task` 요청이 들어오면 **5초 동안 블로킹(대기)**
2. 작업이 완료되면 응답을 반환

**⛔ 문제점**

- 요청을 처리하는 동안 해당 스레드가 블로킹되므로 다른 요청을 처리할 수 없음.
- 트래픽이 많아지면 서버 성능이 저하될 가능성이 큼.

---

## **2. 비동기(Asynchronous) 방식**

비동기 방식에서는 요청을 처리하는 동안 스레드가 블로킹되지 않고 다른 작업을 수행할 수 있습니다. 즉, 하나의 요청을 처리하는 동안 다른 요청도 동시에 처리할 수 있습니다.

### 🔹 비동기 방식의 특징

- 스레드가 블로킹되지 않으며 다른 작업을 수행할 수 있음.
- 서버의 처리 성능을 향상시킬 수 있음.
- 하지만 코드의 복잡도가 증가할 수 있음.

---

### **📌 비동기 방식 예제 (Spring Boot Async)**

Spring에서는 `@Async` 어노테이션을 활용하여 비동기 실행이 가능합니다.

### **🔹 1. `@EnableAsync` 활성화**

Spring에서 비동기 처리를 사용하려면 `@EnableAsync`를 추가해야 합니다.

```java

@Configuration
@EnableAsync
public class AsyncConfig {
}

```

### **🔹 2. 비동기 메서드 정의**

비동기 실행을 위해 `@Async` 어노테이션을 사용합니다.

```java

@Service
public class AsyncService {

    @Async
    public CompletableFuture<String> asyncTask() throws InterruptedException {
        System.out.println("비동기 작업 시작");
        Thread.sleep(5000); // 5초 대기 (비동기)
        System.out.println("비동기 작업 완료");
        return CompletableFuture.completedFuture("비동기 작업 완료!");
    }
}

```

- `@Async`를 붙이면 새로운 스레드에서 실행됨.
- `CompletableFuture<String>`을 반환하여 비동기적으로 결과를 받을 수 있음.

### **🔹 3. 비동기 API Controller**

```java

@RestController
@RequestMapping("/async")
public class AsyncController {

    private final AsyncService asyncService;

    public AsyncController(AsyncService asyncService) {
        this.asyncService = asyncService;
    }

    @GetMapping("/task")
    public CompletableFuture<String> asyncTask() {
        return asyncService.asyncTask();
    }
}

```

---

### **📌 동기 vs 비동기 실행 결과 비교**

### ✅ **동기 방식 (`/sync/task` 요청)**

```

작업 시작
(5초 대기)
작업 완료
-> 응답 반환 (5초 소요)

```

⛔ 5초 동안 요청이 블로킹되므로 성능 저하 발생

### ✅ **비동기 방식 (`/async/task` 요청)**

```

비동기 작업 시작
-> 즉시 응답 가능 (5초 동안 다른 작업 수행 가능)
비동기 작업 완료 (5초 후)

```

✅ 요청을 보내면 즉시 응답 가능하고, 다른 작업도 동시에 수행할 수 있음.

---

## **3. CompletableFuture를 활용한 고급 비동기 처리**

Spring에서 **비동기 로직을 체이닝**할 때 `CompletableFuture`를 활용하면 더 강력한 비동기 처리가 가능합니다.

```java

@Service
public class AdvancedAsyncService {

    @Async
    public CompletableFuture<String> firstTask() throws InterruptedException {
        Thread.sleep(2000);
        return CompletableFuture.completedFuture("첫 번째 작업 완료");
    }

    @Async
    public CompletableFuture<String> secondTask() throws InterruptedException {
        Thread.sleep(2000);
        return CompletableFuture.completedFuture("두 번째 작업 완료");
    }

    @Async
    public CompletableFuture<String> thirdTask() throws InterruptedException {
        Thread.sleep(2000);
        return CompletableFuture.completedFuture("세 번째 작업 완료");
    }
}

```

```java

@RestController
@RequestMapping("/async")
public class AdvancedAsyncController {

    private final AdvancedAsyncService asyncService;

    public AdvancedAsyncController(AdvancedAsyncService asyncService) {
        this.asyncService = asyncService;
    }

    @GetMapping("/multi-task")
    public CompletableFuture<List<String>> executeMultiTasks() {
        CompletableFuture<String> task1 = asyncService.firstTask();
        CompletableFuture<String> task2 = asyncService.secondTask();
        CompletableFuture<String> task3 = asyncService.thirdTask();

        return CompletableFuture.allOf(task1, task2, task3)
                .thenApply(voidResult -> {
                    List<String> results = new ArrayList<>();
                    results.add(task1.join());
                    results.add(task2.join());
                    results.add(task3.join());
                    return results;
                });
    }
}

```

### **📌 실행 결과 (`/async/multi-task` 요청)**

```

2초 후 → "첫 번째 작업 완료"
2초 후 → "두 번째 작업 완료"
2초 후 → "세 번째 작업 완료"

```

✅ 3개의 작업이 **동시에 실행**되므로 총 실행 시간은 **2초**만 걸립니다.

---

## **4. 동기 vs 비동기 비교 정리**

|  | 동기(Synchronous) | 비동기(Asynchronous) |
| --- | --- | --- |
| 실행 방식 | 요청이 끝날 때까지 대기 | 요청을 처리하면서 다른 작업 가능 |
| 성능 | 느림 (블로킹 발생) | 빠름 (논블로킹, 병렬 실행) |
| 사용 예시 | 데이터 변경이 없는 단순한 로직 | 네트워크 요청, DB 조회, 대량 데이터 처리 |
| 코드 복잡도 | 낮음 (순차 실행) | 높음 (콜백, `CompletableFuture` 활용 필요) |

---

## **5. 결론**

✅ **동기(Synchronous) 방식**

- 간단하고 이해하기 쉽지만, 요청이 많아지면 **성능이 저하될 가능성이 큼**.

✅ **비동기(Asynchronous) 방식**

- `@Async`, `CompletableFuture` 등을 활용하여 **고성능 처리가 가능**.
- 네트워크 요청, 파일 입출력, 데이터베이스 병렬 처리에 매우 유용.

🚀 **비동기 처리가 필요할 때는 Spring의 `@Async`와 `CompletableFuture`를 적극 활용하자!**
