### **1. @ResponseBody를 왜 사용했는지?**

```java
@ResponseBody
@RequestMapping("/selectUpperMenu.do")
public Map<String, Object> selectUpperMenu(@RequestBody MenuManage menuManage) throws Exception {
```

- @ResponseBody는 **컨트롤러의 반환 값을 HTTP 응답 본문(Body)으로 변환하여 클라이언트에게 전달하는 역할**을 합니다.
- Spring에서는 기본적으로 **ViewResolver**를 사용하여 JSP 등 View를 반환하려고 하지만, @ResponseBody를 사용하면 JSON 형태로 직접 데이터를 반환할 수 있습니다.
- 이 경우, Map<String, Object> 형태의 데이터를 **JSON 형식**으로 변환하여 클라이언트(AJAX)로 전달합니다.
- 결국 response.upperMenuList로 받은 데이터는 JSON 응답을 기반으로 사용됩니다.

**요약:** @ResponseBody를 사용하면 컨트롤러가 데이터를 JSON 형태로 변환하여 클라이언트(AJAX)에게 응답합니다.

---

### **2. AJAX 요청에서 contentType: "application/json"을 설정한 이유?**

```jsx
$.ajax({
    url: "/dt3d/adm/menu/selectUpperMenu.do",
    type: "POST",
    contentType: "application/json",
    data: JSON.stringify({ sysCd: sysCd, sysClCd: sysClCd }),
    dataType: "json",
    success: function (response) { ... }
});
```

- contentType: "application/json"은 **클라이언트(AJAX)가 서버에 보낼 데이터 형식이 JSON임을 명시**하는 역할을 합니다.
- 기본적으로 $.ajax()에서 데이터를 전송할 때는 **application/x-www-form-urlencoded 형식**이 기본값입니다.
- 하지만 Spring에서 @RequestBody로 데이터를 받을 경우, **JSON 형태로 전달해야 하기 때문에 application/json으로 설정**해야 합니다.

**요약:**

- contentType: "application/json"을 설정하면, 서버(Spring)에서 JSON 데이터를 올바르게 받을 수 있도록 합니다.

---

### **3. data: JSON.stringify(...)를 왜 사용하는지?**

```jsx
data: JSON.stringify({ sysCd: sysCd, sysClCd: sysClCd })
```

- AJAX를 통해 서버로 데이터를 전송할 때, JavaScript 객체 { sysCd: sysCd, sysClCd: sysClCd }을 **JSON 문자열로 변환**하기 위해 JSON.stringify(...)를 사용합니다.
- 만약 JSON.stringify()를 사용하지 않고 그냥 { sysCd: sysCd, sysClCd: sysClCd }를 전달하면,→ 기본적으로 **application/x-www-form-urlencoded 형태**로 변환되어 서버로 전달됩니다.→ 하지만 서버에서 @RequestBody를 사용해 JSON을 받을 경우, **문자열(JSON 형식)로 보내야** 합니다.
- 따라서, JSON.stringify(...)를 사용해 데이터를 JSON 형식의 문자열로 변환해야 합니다.

**요약:**

- JSON.stringify(...)를 사용하면, JavaScript 객체를 JSON 문자열로 변환하여 서버가 정상적으로 받을 수 있도록 합니다.

---

### **4. 서버에서 데이터를 받을 때 @RequestBody를 사용한 이유?**

```java
public Map<String, Object> selectUpperMenu(@RequestBody MenuManage menuManage) throws Exception
```

- @RequestBody는 **클라이언트가 보낸 JSON 데이터를 Java 객체로 변환하여 받을 때 사용**됩니다.
- AJAX 요청에서 contentType: "application/json"을 설정하고 JSON 형식으로 데이터를 전송했기 때문에,→ 서버에서도 이를 **@RequestBody를 사용해 MenuManage 객체로 변환**해야 합니다.
- 만약 @RequestBody 없이 MenuManage menuManage를 받는다면, JSON이 아닌 **폼 데이터(application/x-www-form-urlencoded)** 형식으로 데이터를 받아야 합니다.

**요약:**

- @RequestBody는 **JSON 형식의 데이터를 Java 객체(MenuManage)로 변환**하는 역할을 합니다.

---

## **최종 요약**

| 항목 | 역할 및 이유 |
| --- | --- |
| @ResponseBody | 컨트롤러가 JSON 데이터를 응답 본문으로 반환할 수 있도록 함 |
| contentType: "application/json" | 클라이언트가 JSON 형식으로 데이터를 전송한다고 명시 |
| JSON.stringify(...) | JavaScript 객체를 JSON 문자열로 변환하여 서버에 보냄 |
| @RequestBody | 클라이언트가 보낸 JSON 데이터를 Java 객체로 변환 |

## **@RequestBody 없이 Map을 활용하는 방법**

### **1. @RequestBody 없이 Map을 활용하는 방법**

```java
@ResponseBody
@RequestMapping("/selectUpperMenu.do")
public Map<String, Object> selectUpperMenu(@RequestParam Map<String, Object> paramMap) throws Exception {
    if ("SYSUSR".equals(paramMap.get("sysCd"))) {
        paramMap.put("sysCd", "SYSUSER");
    }

    List<String> upperMenuList = menuManageService.selectUpperMenu(paramMap);

    Map<String, Object> result = new HashMap<>();
    result.put("upperMenuList", upperMenuList);

    return result;
}
```

### **차이점**

- @RequestBody를 제거하고 대신 **@RequestParam Map<String, Object>*를 사용함.
- 클라이언트에서 JSON을 보낼 필요 없이 **URL 파라미터 또는 x-www-form-urlencoded 방식의 데이터를 전송**해야 함.
- paramMap.get("sysCd")을 통해 데이터를 가져옴.

---

### **2. AJAX 요청을 어떻게 변경해야 하는가?**

### **기존 방식 (@RequestBody 사용)**

```jsx
$.ajax({
    url: "/dt3d/adm/menu/selectUpperMenu.do",
    type: "POST",
    contentType: "application/json",
    data: JSON.stringify({ sysCd: sysCd, sysClCd: sysClCd }),
    dataType: "json",
    success: function (response) { ... }
});
```

- JSON 데이터를 본문에 담아서 전송 (@RequestBody 필요)

### **변경된 방식 (Map<String, Object> 사용)**

```jsx
$.ajax({
    url: "/dt3d/adm/menu/selectUpperMenu.do",
    type: "POST",
    data: { sysCd: sysCd, sysClCd: sysClCd }, // JSON.stringify 필요 없음
    success: function (response) { ... }
});
```

- 데이터를 **일반 form-data 방식**으로 전송 (application/x-www-form-urlencoded)
- contentType: "application/json"을 제거해야 함 (기본값 application/x-www-form-urlencoded)

---

### **3. @RequestBody vs @RequestParam Map<String, Object> 차이점**

| 방식 | @RequestBody 사용 | @RequestParam Map<String, Object> 사용 |
| --- | --- | --- |
| **데이터 전송 방식** | JSON 형식 (Request Body) | URL 파라미터 또는 Form Data |
| **AJAX 설정** | JSON.stringify(...) 필요 & contentType: "application/json" 설정 | data: { key: value } 형식 사용 |
| **서버 데이터 매핑 방식** | Java 객체(@RequestBody MenuManage)로 변환 | Map<String, Object>에 Key-Value로 저장 |
| **사용 사례** | 복잡한 JSON 데이터 구조 (객체, 배열 포함) 전송 시 유용 | 간단한 Key-Value 데이터 전송 시 유용 |

---

### **4. 언제 @RequestBody를 사용하고, 언제 Map을 사용할까?**

**@RequestBody 사용이 적절한 경우**

- JSON 데이터를 그대로 받아야 할 때
- 중첩된 객체, 배열을 포함한 복잡한 데이터 구조를 처리할 때
- RESTful API를 설계할 때 (일반적으로 JSON 기반 API가 많음)

**@RequestParam Map<String, Object> 사용이 적절한 경우**

- 간단한 Key-Value 데이터(쿼리 파라미터 같은 형식)를 받을 때
- form-data 방식의 요청을 받을 때 (ex: HTML Form)
- 기존 시스템이 application/x-www-form-urlencoded 방식을 사용 중일 때

---

### **결론**

- @RequestBody는 **JSON 데이터를 Request Body에서 직접 추출**하여 객체로 변환할 때 사용.
- @RequestParam Map<String, Object>는 **폼 데이터 또는 URL 파라미터를 간단한 Key-Value 매핑**으로 받을 때 사용.
- 클라이언트에서 데이터를 보내는 방식에 따라 서버에서 받는 방식도 변경해야 함!

## ModelAndView

### **1. ModelAndView를 사용하여 JSON 데이터 반환**

```java
@RequestMapping("/selectUpperMenu.do")
public ModelAndView selectUpperMenu(@RequestParam Map<String, Object> paramMap) throws Exception {
    if ("SYSUSR".equals(paramMap.get("sysCd"))) {
        paramMap.put("sysCd", "SYSUSER");
    }

    List<String> upperMenuList = menuManageService.selectUpperMenu(paramMap);

    ModelAndView mav = new ModelAndView("jsonView");
    mav.addObject("upperMenuList", upperMenuList);

    return mav;
}

```

### **설명**

- ModelAndView 객체를 생성하여 데이터를 담고 반환.
- mav.addObject("upperMenuList", upperMenuList); → 데이터를 upperMenuList라는 키로 저장.
- new ModelAndView("jsonView") → **뷰 이름을 "jsonView"로 설정**하여 JSON 응답이 가능하도록 처리.

**⚠주의:**

- **Spring 설정 파일에 jsonView가 설정되어 있어야 함.**→ Jackson이 JSON 변환을 담당하도록 설정이 필요함.→ 일반적으로 MappingJackson2JsonView를 Bean으로 등록해야 함.

### **Spring 설정에 jsonView 추가 (스프링 설정 파일 사용 시)**

```xml
<bean id="jsonView" class="org.springframework.web.servlet.view.json.MappingJackson2JsonView"/>
```

### **Spring Boot 사용 시 jsonView Bean 등록**

```java
@Bean
public MappingJackson2JsonView jsonView() {
    return new MappingJackson2JsonView();
}
```

---

### **방법 2: Model 객체를 사용하여 데이터를 반환**

```java
@RequestMapping("/selectUpperMenu.do")
public String selectUpperMenu(@RequestParam Map<String, Object> paramMap, Model model) throws Exception {
    if ("SYSUSR".equals(paramMap.get("sysCd"))) {
        paramMap.put("sysCd", "SYSUSER");
    }

    List<String> upperMenuList = menuManageService.selectUpperMenu(paramMap);

    model.addAttribute("upperMenuList", upperMenuList);

    return "jsonView";
}
```

### **설명**

- model.addAttribute("upperMenuList", upperMenuList); → 데이터를 모델에 추가.
- return "jsonView"; → **뷰 리졸버(ViewResolver)가 "jsonView"를 찾아 JSON으로 변환.**
- **뷰 이름이 "jsonView"이므로, MappingJackson2JsonView가 JSON을 반환하도록 설정 필요.**

---

### **방법 3: View를 사용하여 직접 JSON 반환**

```java
@RequestMapping("/selectUpperMenu.do")
public View selectUpperMenu(@RequestParam Map<String, Object> paramMap, Model model) throws Exception {
    if ("SYSUSR".equals(paramMap.get("sysCd"))) {
        paramMap.put("sysCd", "SYSUSER");
    }

    List<String> upperMenuList = menuManageService.selectUpperMenu(paramMap);
    model.addAttribute("upperMenuList", upperMenuList);

    return new MappingJackson2JsonView();
}
```

### **설명**

- MappingJackson2JsonView를 직접 반환하여 JSON으로 변환.

---

### **정리: @ResponseBody 없이 JSON 응답을 만드는 방법**

| 방법 | 반환 타입 | 설명 |
| --- | --- | --- |
| **방법 1** | ModelAndView | "jsonView"를 사용하여 JSON 변환 |
| **방법 2** | String (뷰 이름) | "jsonView"를 리턴하여 모델 데이터를 JSON 변환 |
| **방법 3** | View (MappingJackson2JsonView) | 직접 JSON 변환 뷰를 반환 |

---

### **결론**

- @ResponseBody를 사용하지 않고 JSON 데이터를 반환하려면, **뷰(ViewResolver)를 통해 JSON 변환**이 필요.
- **가장 쉬운 방법:** ModelAndView("jsonView")를 사용하거나, MappingJackson2JsonView를 활용하면 해결 가능.
- **뷰 설정이 필요함:** MappingJackson2JsonView를 설정해야 JSON 변환이 가능
