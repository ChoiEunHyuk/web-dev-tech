### “java heap space 부족”

- 이럴 경우 tool 실행 시 vmarg를 넘겨주면 실행 시 heap memory를 정해준 size만큼 설정 가능
    - -vmargs -Xms4096m (구동시 확보되는 최소 메모리)
    - vmargs -Xmx8192m (최대 사용 메모리)

- 설정 방법
    - 바로가기 아이콘에서 우클릭 → 바로가기 탭 → 대상에 -vmargs -Xms8000m 붙여줌
