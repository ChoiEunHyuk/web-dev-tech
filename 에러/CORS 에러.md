CORS(Cross-Origin Resource Sharing) 에러는 브라우저에서 보안상의 이유로 발생한다.

웹 애플리케이션이 자신이 로드된 도메인(origin)과 다른 도메인으로 HTTP 요청을 보낼 때 발생할 수 있다. 예를 들어, `https://example.com`에서 로드된 스크립트가 `https://api.another.com/data`에 요청을 보내면, 이건 **cross-origin** 요청이 된다.

브라우저는 이런 요청에 대해 서버가 적절한 CORS 헤더를 포함해 응답하지 않으면 보안을 위해 요청을 차단한다.

### 대표적인 CORS 에러 상황

1. **서버에 CORS 허용 헤더가 없음**
    
    서버 응답에 `Access-Control-Allow-Origin` 헤더가 없거나 잘못된 경우.
    
2. **허용되지 않은 메서드나 헤더 사용**
    
    `PUT`, `DELETE` 등 일부 메서드나 커스텀 헤더를 사용할 경우, **preflight 요청**(OPTIONS)이 먼저 보내지는데, 서버가 이에 적절히 응답하지 않으면 에러가 난다.
    
3. **Credentials 관련 설정 문제**
    
    `withCredentials: true` 설정 후 요청하는 경우, 서버가 `Access-Control-Allow-Credentials: true`를 포함하지 않으면 차단된다.
    

### 해결 방법

- **서버 측에서 CORS 허용 설정 추가**
    
    예를 들어, 모든 도메인 허용:
    
    ```
    Access-Control-Allow-Origin: *
    ```
    
    특정 도메인만 허용:
    
    ```
    Access-Control-Allow-Origin: https://example.com
    ```
    
- **필요 시 preflight 요청에 대한 처리 추가**
    
    OPTIONS 요청에 대해 적절한 응답을 반환하도록 서버 설정 필요.
    
- **Credentials 허용 시에는 와일드카드(*) 사용 불가**
    
    쿠키 등을 주고받으려면 정확한 도메인을 지정하고 `Access-Control-Allow-Credentials: true` 설정 필요.
    

요약하면, CORS 에러는 브라우저의 보안 정책으로 인해 발생하며, 서버가 이에 맞는 HTTP 응답 헤더를 포함해야 해결된다.
