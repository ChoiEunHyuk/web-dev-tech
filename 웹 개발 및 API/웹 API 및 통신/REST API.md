REST(Representational State Transfer)의 약자로 자원을 이름으로 구분하여 해당 자원의 상태를 주고받는 모든 것을 의미

- **REST란?**
    - HTTP URI(Uniform Resource Identifier)를 통해 자원(Resource)을 명시하고,
    - HTTP Method(POST, GET, PUT, DELETE, PATCH 등)를 통해
    - 해당 자원(URI)에 대한 CRUD Operation을 적용하는 것을 의미합니다.
    
- **CRUD Operation이란**
    
    - CRUD는 대부분의 컴퓨터 소프트웨어가 가지는 기본적인 데이터 처리 기능인 Create(생성), Read(읽기), Update(갱신), Delete(삭제)를 묶어서 일컫는 말
    
- **REST 구성 요소**
    - **자원(Resource) : HTTP URI**
    - **자원에 대한 행위(Verb) : HTTP Method**
    - **자원에 대한 행위의 내용 (Representations) : HTTP Message Pay Load**

- **REST의 특징**
    - Server-Client(서버-클라이언트 구조)
    - Stateless(무상태)
    - Cacheable(캐시 처리 가능)
    - Layered System(계층화)
    - Uniform Interface(인터페이스 일관성)

- **REST의 장단점**
    
    장점
    
    - HTTP 프로토콜의 인프라를 그대로 사용하므로 REST API 사용을 위한 별도의 인프라를 구출할 필요가 없다.
    - HTTP 프로토콜의 표준을 최대한 활용하여 여러 추가적인 장점을 함께 가져갈 수 있게 해 준다.
    - HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능하다.
    - Hypermedia API의 기본을 충실히 지키면서 범용성을 보장한다.
    - REST API 메시지가 의도하는 바를 명확하게 나타내므로 의도하는 바를 쉽게 파악할 수 있다.
    - 여러 가지 서비스 디자인에서 생길 수 있는 문제를 최소화한다.
    - 서버와 클라이언트의 역할을 명확하게 분리한다.
    
    단점
    
    - 표준이 자체가 존재하지 않아 정의가 필요하다.
    - HTTP Method 형태가 제한적이다.
    - 브라우저를 통해 테스트할 일이 많은 서비스라면 쉽게 고칠 수 있는 URL보다 Header 정보의 값을 처리해야 하므로 전문성이 요구된다.
    - 구형 브라우저에서 호환이 되지 않아 지원해주지 못하는 동작이 많다.(익스폴로어)
