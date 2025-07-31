### 📌 AOP(Aspect-Oriented Programming, 관점지향 프로그래밍)란?

**AOP**는 소프트웨어 개발에서 **핵심적인 기능(비즈니스 로직)과 부가적인 기능(로깅, 보안, 트랜잭션 등)을 분리**하여 모듈화하는 프로그래밍 방법입니다.

즉, 기존의 OOP(객체지향 프로그래밍)가 해결하지 못하는 "공통 관심사(cross-cutting concern)" 문제를 해결하기 위해 등장했습니다.

---

## 📌 AOP의 주요 개념과 용어

AOP를 이해하려면 다음 용어를 명확히 알아야 합니다.

| 용어 | 설명 | 예시 |
| --- | --- | --- |
| **Aspect**(관점) | 공통으로 적용할 부가적 기능을 정의한 모듈 | 로깅, 인증, 트랜잭션 |
| **Advice**(어드바이스) | 특정 시점에 실행할 로직(부가 기능)을 의미 | 메소드 호출 전후에 실행되는 로그 처리 코드 |
| **Join Point**(조인 포인트) | Advice가 적용될 수 있는 위치(메소드 호출, 예외 발생, 객체 생성 등) | 컨트롤러 메소드 호출 |
| **Pointcut**(포인트컷) | 실제로 Advice가 적용될 Join Point를 명시하는 표현식 | insert* 라는 이름의 메소드 |
| **Weaving**(위빙) | 실제 코드와 부가기능(Aspect)을 연결하는 작업 | 컴파일 시점, 런타임 시점 |

---

## 📌 AOP는 왜 사용할까?

소프트웨어 개발 시 발생하는 **공통 관심사**는 애플리케이션 전체에 반복해서 나타납니다.

예를 들면:

- **로깅(Logging)**
    
    메소드 호출 로그, 에러 로그 등 반복적 코드
    
- **보안(Security)**
    
    권한 체크, 인증, 인가 처리
    
- **트랜잭션(Transaction)**
    
    트랜잭션 관리(begin, commit, rollback)
    
- **성능 모니터링(Performance monitoring)**
    
    실행시간 측정, 성능 추적 코드
    

이러한 관심사를 기존의 OOP 방식으로 처리하면 모든 메소드마다 중복 코드가 반복적으로 나타나게 됩니다. 이때 AOP를 사용하면:

- 중복 코드 제거
- 유지보수 편의성 증대
- 관심사의 명확한 분리(Separation of Concerns)

이 가능합니다.

---

## 📌 AOP 동작 원리 (구성 요소 예시)

### 간단한 예제 상황 (로깅)

> 사용자 정보를 조회하는 메소드 실행 시 로깅하기
> 

**Step 1: Aspect 정의 (로깅 기능)**

```java
@Component
@Aspect
public class LoggingAspect {

    @Around("execution(* com.example.service.*Service.*(..))")
    public Object logAround(ProceedingJoinPoint joinPoint) throws Throwable {
        String methodName = joinPoint.getSignature().getName();
        System.out.println("[LOG] 실행 전: " + methodName);

        Object result = joinPoint.proceed(); // 실제 메소드 호출

        System.out.println("[LOG] 실행 후: " + methodName);
        return result;
    }
}
```

이 코드에서:

- @Aspect: 해당 클래스가 Aspect임을 명시합니다.
- @Around: 메소드 호출 전후에 특정 로직을 추가합니다.

**Step 2: Pointcut 정의 (적용할 메소드 지정)**

- execution(* com.example.service.*Service.*(..))
- 이 표현식은 다음을 의미합니다.
    - com.example.service 패키지 내
    - Service로 끝나는 클래스의
    - 모든 메소드 호출을 지정합니다.

**Step 3: Advice 실행 (실제 적용)**

- 메소드 실행 전후로 로그가 자동으로 찍히게 됩니다.

결과 예시:

```
[LOG] 실행 전: getUserInfo
[LOG] 실행 후: getUserInfo
```

---

## 📌 Advice의 종류

Advice는 시점에 따라 다음과 같이 구분됩니다.

| Advice | 설명 | 예시 |
| --- | --- | --- |
| **Before** | 메소드 호출 전 수행 | 로깅, 인증 체크 |
| **After returning** | 메소드 정상 종료 후 수행 | 메소드 결과값 로깅 |
| **After throwing** | 메소드 예외 발생 시 수행 | 예외 로깅, 알림 |
| **After (finally)** | 메소드 호출 후 항상 수행(성공/실패 무관) | 자원 해제 |
| **Around** | 메소드 호출 전/후를 모두 제어 가능 | 트랜잭션 처리, 성능 모니터링 |

---

## 📌 AOP 동작 과정(Weaving 방식)

AOP가 Advice를 실제 코드에 연결하는 것을 **위빙(Weaving)**이라 합니다.

위빙은 다음 방식 중 하나로 이루어집니다.

- **컴파일 타임(Compile-time weaving)**
    
    컴파일 과정에서 Aspect를 코드에 삽입 (AspectJ 사용)
    
- **로딩 타임(Load-time weaving)**
    
    클래스 로딩 시점에 Aspect를 연결
    
- **런타임(Run-time weaving)**
    
    런타임 시점에 프록시(Proxy) 객체를 생성하여 Aspect 연결 (Spring AOP가 사용하는 방식)
    

**Spring AOP**는 내부적으로 프록시 객체(Proxy)를 생성하여 런타임 시점에 Aspect 로직을 실행합니다.

---

## 📌 Spring AOP의 특징과 한계

- Spring AOP는 **런타임 프록시 방식**이므로, AspectJ 같은 컴파일 방식에 비해 기능이 제한됩니다.
- Spring AOP는 **메소드 실행** Join Point만 지원하며,
생성자 호출, 필드 접근, 정적 메소드 실행 등은 처리 불가합니다.

| Spring AOP | AspectJ |
| --- | --- |
| 런타임 방식 | 컴파일 or 로딩 방식 |
| 메소드 실행만 Join Point 가능 | 메소드 실행, 필드 접근, 객체 생성 등 다양한 Join Point 지원 |
| 설정 간편, 러닝커브 낮음 | 다소 복잡하지만 강력한 기능 제공 |

대부분의 애플리케이션에서는 **Spring AOP로 충분히 요구사항을 만족**할 수 있습니다.

---

## ✅ **결론**

- AOP는 소프트웨어의 부가적 관심사(Cross-cutting concerns)를 분리하여 관리 가능한 기술입니다.
- Spring에서 AOP는 주로 **로깅, 트랜잭션, 인증, 성능 측정** 등의 반복적이고 공통적인 작업에 활용됩니다.
- AOP를 잘 활용하면 코드 중복이 줄어들고 유지보수성이 크게 향상됩니다.
