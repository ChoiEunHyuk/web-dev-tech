- 기존에는 게시글을 조회할 때 +1을 해주는 방식을 사용
    - 조회수가 증가는 하지만 한 사용자가 조회수를 무한정 증가시킬 수 있어 조회수의 정확도가 내려감

### youtube의 조회수 카운트 프로세스

- 유튜브 사용자가 동영상을 시청
- 적어도 30초 동안 영상을 시청해야함 → 만약 영상의 길이가 30초를 넘지 않는다면 사용자가 영상을 끝까지시청해야 조회수 카운트
- 유튜브 동영상에 스팸댓글을 남기는 사용자의 보기는 집계되지 않음
- 웹 사이트에 포함된 자동 시작 동영상은 조회수로 집계되지 않음
- 최대 반복수는 300번(예상)
- 조회수가 증가하지 않는 상황
    - 많은 장치에 대해 하나의 IP 주소를 사용하여 동시에 동일한 영상을 시청하는 경우
    - 윈도우 또는 탭을 많이 실행시켜 영상을 동시에 보는 행위
    - 영상을 시청하는 30초마다 페이지 새로고침

### 방법1: IP

- 장점
    - 조작이 불가
- 단점
    - ip는 장소에 따라 유동적으로 변함
    - 값이 길기때문에 수많은 유저와 수많은 게시글과 날짜를 함께 저장하기에는 무리가 있음

### 방법2: Session

- 장점
    - 사용자 정보를 서버에 둠 → 쿠키보다 보안이 높음
    - 저장데이터에 제한이 없음
- 단점
    - 서버의 리소스 사용량이 높음

### 방법3: Cookie

- 장점
    - 서버의 공간을 절약
- 단점
    - 개인정보가 기록된다면 보안이슈
    - 서버에 저장하는 방식이 아닌 사용자의 브라우저에 저장되기 때문에 임의로 고치거나 지울 수 없음

### 쿠키로 선택

- 이유
    - 단순 조회수 증가 로직이기 때문

### 코드

```java
@RequestMapping(value = "/drtWarnDetailView.do")
public String drtWarnDetailView(@RequestParam Map<String, Object> commandMap, HttpServletRequest request, HttpServletResponse response, ModelMap model) throws Exception {

    String pageIdx = (String) commandMap.getOrDefault("pageIdx", "1");
    String srchWrd = (String) commandMap.getOrDefault("srchWrd", "");
    String id = (String) commandMap.get("id");
    commandMap.put("id", id);

    model.addAttribute("pageIdx", pageIdx);
    model.addAttribute("srchWrd", srchWrd);

    final String cookieName = "readDrtWarnDetail";
    boolean found = false;
    Cookie[] cookies = request.getCookies();

    if (cookies != null) {
        for (Cookie cookie : cookies) {
            if (cookieName.equals(cookie.getName())) {
                found = true;
                String value = cookie.getValue();
                // 이미 본 적이 없으면 조회수 올리고, id를 값에 추가
                if (!value.contains(id)) {
                    drtWarnService.addDrtWarnViewCount(commandMap);
                    cookie.setValue(value + "_" + id);
                    cookie.setMaxAge(60 * 60 * 24); // 만료: 24시간
                    response.addCookie(cookie);
                }
                break;
            }
        }
    }

    // 쿠키가 없었으면 새로 만들어서 조회수 증가
    if (!found) {
        drtWarnService.addDrtWarnViewCount(commandMap);
        Cookie newCookie = new Cookie(cookieName, id);
        newCookie.setMaxAge(60 * 60 * 24); // 만료: 24시간
        response.addCookie(newCookie);
    }
    EgovMap drtWarnView = drtWarnService.selectDrtWarnView(commandMap);
    model.addAttribute("drtWarnView", drtWarnView);

    return "portal/wcs/drtWarnDetailView";
}
```
