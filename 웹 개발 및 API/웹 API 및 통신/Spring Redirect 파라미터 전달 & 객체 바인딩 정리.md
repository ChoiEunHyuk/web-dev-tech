## RedirectAttributes + @ModelAttribute 사용 시 주의사항

### 예제

```java
@RequestMapping(value = "/Acontroller.do")
public String goLogin(LoginVO loginVO, RedirectAttributes redirectAttributes) {
    redirectAttributes.addAttribute("id", loginVO.getId());
    redirectAttributes.addAttribute("pw", loginVO.getPw());
    return "redirect:/portal/loginEvent.do";
}
```

→ 실제 호출되는 URL

```

/portal/loginEvent.do?id=someId&pw=somePw
```

---

## 쿼리스트링은 어떻게 객체로 바인딩될까?

Spring은 다음 두 가지 방식을 통해 값을 객체로 바인딩한다.

| 바인딩 경로 | 설명 |
| --- | --- |
| 쿼리스트링 | URL 파라미터로 넘어온 값이 `request.getParameter()`로 넘어옴 |
| setter | 객체의 setter를 통해 자동으로 바인딩 |

---

## 왜 `@ModelAttribute("xxx")`를 써도 안 들어가는 경우가 있나?

- `@ModelAttribute("loginVO") LoginVO loginVO` 로 썼을 때는
    - **request attribute**에서만 loginVO를 찾으려 함
    - 쿼리 파라미터 (`?id=...&pw=...`)를 잘 못 읽을 수도 있음
- 그냥 `LoginVO loginVO`로 쓰면
    - Spring이 쿼리 파라미터 ↔ setter로 자동 매핑해줌 (편하게 동작)

---

## 올바른 작성법 (추천)

```java
@RequestMapping(value = "/loginEvent.do")
@ResponseBody
public Map<String, Object> mainLoginEvent(LoginVO loginVO) throws Exception {
    // loginVO.getId(), loginVO.getPw() 자동으로 바인딩 됨
}
```

---

## Spring 자동 바인딩 핵심 정리

| 구분 | 역할 | 설명 |
| --- | --- | --- |
| `@ModelAttribute("xxx")` | 모델 등록 | 모델 객체를 뷰로 넘길 때 사용 (바인딩도 가능하지만 보조적인 역할) |
| 파라미터 자동 바인딩 | 필수 | 쿼리 파라미터가 setter로 자동 바인딩됨 (`request.getParameter()` 기반) |
| `RedirectAttributes.addAttribute()` | Redirect 시 쿼리 파라미터 생성 | redirect 시 URL 파라미터로 추가됨 |

---

## 왜 아래 코드는 동작했을까?

```java

@RequestMapping("/selectMenuManageInfo.do")
public String selectMenuManageInfo(@ModelAttribute("menuManage") MenuManage menuManage, Model model) {
    ...
}

```

→ 실제로는

`/selectMenuManageInfo.do?menuId=xxx&pageIndex=1&...`

쿼리스트링으로 넘어온 값이

```
request.getParameter("menuId")
```

로 잡히고,

`menuManage.setMenuId()`로 자동 바인딩된 것임.

**`@ModelAttribute` 때문이 아니라, 그냥 setter 매핑으로 된 것**

---

## 정리

- 쿼리 파라미터와 객체 필드명이 동일하면 setter로 자동 바인딩 O
- `@ModelAttribute("xxx")`는 자동 바인딩과 무관
- `RedirectAttributes.addAttribute()`는 URL 파라미터 세팅용
- Redirect 이후에도 객체 바인딩 가능 (단, `@ModelAttribute` 없어도 됨)

---

## 📢 TIP

> 실무에서는
> 
> 
> Redirect 대상에서 `@ModelAttribute`는 잘 안 쓰고,
> 
> 그냥 객체 파라미터로 받아서 자동 바인딩에 맡기는 게 일반적임.
>
