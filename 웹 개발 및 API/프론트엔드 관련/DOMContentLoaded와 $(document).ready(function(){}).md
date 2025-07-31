## 🔹 DOMContentLoaded

```jsx
document.addEventListener("DOMContentLoaded", function () {
    console.log("DOM이 준비됨!");
});
```

### ✅ 특징

- **순수 JavaScript (Vanilla JS) 방식**
- HTML **문서의 구조 (DOM)** 가 모두 로드되면 실행됨.
- 외부 CSS, 이미지, iframe 등 **리소스가 로드되기 전에도 실행**될 수 있음.
- 실행 속도가 빠름 (필요한 HTML 요소만 로드되면 실행 가능).
- 최신 브라우저에서는 DOMContentLoaded가 권장됨.

---

## 🔹 $(document).ready(function(){}) (jQuery 방식)

```jsx
$(document).ready(function () {
    console.log("DOM이 준비됨! (jQuery)");
});
```

### ✅ 특징

- **jQuery 라이브러리를 사용할 때만 가능** ($ 사용)
- DOMContentLoaded와 동일하게 DOM이 로드되면 실행됨.
- **jQuery 버전 3.0 이후부터는 $(document).ready()와 DOMContentLoaded가 사실상 동일**하게 동작.

💡 **간단한 최신 코드로 변경 가능**

```jsx
$(function () {
    console.log("이것도 $(document).ready()와 동일한 jQuery 방식");
});
```

---

## 🔹 window.onload

```jsx
window.onload = function () {
    console.log("모든 리소스 로드 완료!");
};
```

### ✅ 특징

- HTML 문서뿐만 아니라 **이미지, CSS, iframe 등의 모든 리소스가 로드된 후 실행됨**.
- DOMContentLoaded보다 실행이 늦음.
- 리소스를 모두 불러와야 실행되므로, 일반적으로 UI 조작을 위한 코드에는 적합하지 않음.

---

## 🔹 차이점 정리

| 이벤트 | 실행 시점 | 리소스 로드 필요 여부 | 사용 환경 |
| --- | --- | --- | --- |
| `DOMContentLoaded` | HTML 문서(DOM)만 로드되면 실행 | ❌ (이미지, CSS 로드 필요 없음) | 순수 JS |
| `$(document).ready()` | HTML 문서(DOM)만 로드되면 실행 | ❌ (이미지, CSS 로드 필요 없음) | jQuery |
| `window.onload` | 모든 리소스(이미지, CSS 등) 로드 후 실행 | ✅ 필요 | 순수 JS |

---

## ✅ 결론: 언제 무엇을 써야 할까?

- **jQuery를 사용하지 않는다면?** → DOMContentLoaded 사용.
- **jQuery를 사용하고 있다면?** → $(document).ready() 사용 가능 (하지만 최신 버전에서는 굳이 필요 없음).
- **모든 리소스가 다 로드된 후 실행해야 한다면?** → window.onload 사용.

👉 **실행 속도가 중요한 경우 DOMContentLoaded가 가장 효율적**입니다.
