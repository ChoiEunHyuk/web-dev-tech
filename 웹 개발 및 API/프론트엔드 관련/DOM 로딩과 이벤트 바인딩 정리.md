### 1. DOMContentLoaded vs. 일반 스크립트 실행

### (1) DOMContentLoaded란?

- HTML 문서의 초기 파싱이 완료되고, DOM 트리가 완성되었을 때 발생하는 이벤트
- 이미지, CSS 등의 리소스 로딩과는 무관하게 **DOM만 준비되었는지**를 기준으로 판단함

### (2) DOMContentLoaded 이벤트 사용 목적

- JavaScript가 DOM 요소에 안전하게 접근할 수 있도록 보장하기 위해 사용
- DOM 요소가 존재하지 않는데 .getElementById() 등을 사용하면 null 반환 및 오류 발생 가능성 있음

```jsx
document.addEventListener("DOMContentLoaded", function () {
  // 안전하게 DOM 접근 가능
});
```

### 2. jQuery의 $(function() {})의 역할

- $(document).ready(function () { ... })의 축약 표현
- 내부적으로 DOMContentLoaded와 동일한 타이밍에 실행됨
- jQuery 기반 프로젝트에서는 DOM이 완전히 준비된 후 실행되어 안전하게 DOM 접근 가능

```jsx
$(function () {
  // DOM 접근 및 이벤트 바인딩 가능
});
```

### 3. 스크립트 실행 시점에 따른 DOM 접근 가능 여부

| 스크립트 위치 | DOM 접근 가능 여부 | 설명 |
| --- | --- | --- |
| `<body>` 하단 (`</body>` 앞) | 가능 | DOM이 이미 로드된 상태이므로 안전 |
| `<head>` 또는 `<body>` 상단 | 불가능할 수 있음 | DOM이 아직 로드되지 않았을 수 있음 |
| 상단이더라도 `DOMContentLoaded` 또는 `$(function(){})` 사용 | 가능 | DOM이 로드된 후 실행되도록 예약됨 |

### 4. 이벤트 바인딩 방식 비교

### (1) 정적 바인딩 (직접 바인딩)

```jsx
$('.drtWarnArea li').click(function () {
  // 클릭 이벤트 처리
});
```

- 이 시점에 DOM에 존재하는 요소에만 이벤트가 바인딩됨
- 이후 동적으로 추가된 요소에는 바인딩되지 않음

### (2) 동적 바인딩 (이벤트 위임)

```jsx
$('.drtWarnArea').on('click', 'li', function () {
  // 클릭 이벤트 처리
});
```

- 부모 요소에 이벤트를 위임하여, 자식 요소가 동적으로 생성되어도 이벤트가 정상 작동
- 자주 바뀌는 리스트, 테이블 등에 적합한 방식

### 5. form 전송 시 name 속성의 역할

- <input>, <select>, <textarea> 등 form control 요소가 name 속성을 가지고 있으면, form이 submit될 때 해당 값이 서버로 전송됨
- 별도의 form: 네임스페이스 또는 라이브러리 태그 없이도 HTML 표준만으로 가능

```html
<form action="/search" method="post">
  <input type="text" name="keyword" value="검색어" />
</form>
```

### 6. 요약

- DOMContentLoaded 또는 jQuery ready 구문을 사용하면 스크립트가 어디에 있어도 안전하게 DOM 조작 가능
- 이벤트 바인딩은 요소가 존재하는 시점에 이뤄져야 하며, 동적 요소에는 이벤트 위임을 사용하는 것이 바람직
- form 전송 시에는 name 속성이 필수이며, 해당 속성이 서버로 넘어가는 데이터의 key가 됨
