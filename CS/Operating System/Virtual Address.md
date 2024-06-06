## Multiprogramming
* Multiprogramming를 위해선 동시에 여러 프로세스가 DRAM에 저장되어 있어야 함 -> `Protection` 이 중요
* `Protection` : 하나의 프로세스가 다른 프로세스의 메모리 영역에 침범하지 않도록 막아주는 메커니즘
* Base register 와 Limit register로 구현

## Logical address vs Physical address
* Logical address : CPU에 의해 생성되는 주소
* Physical address : DRAM과 같은 물리 메모리 주소

## MMU
* 프로세서 내부에 Virtual address -> physical address 컨버팅 해주는 하드웨어

## Virtual Address 왜 필요해?
* Virtual Address가 없을 경우
    - 개발자로 하여금 메모리 사용에 신경써야하는 리소스 문제
    - 메모리 사용에 있어 유연성 떨어짐
* Virtual Address가 있을 경우
    - 모든 프로세스의 메모리 시작은 0부터 무한대
    - 프로그램은 물리 메모리 주소를 알 필요가 없어짐
    - 프로세서의 MMU 장치가 가상 주소와 물리 주소를 매핑해줌
    - 메모리 사용에 있어 유연성 증가
