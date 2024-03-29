### 해당 글은 스프링 시큐리티 동작 과정에 대해 이해를 한다는 가정하에 작성했습니다

## 서론

* 테스트 코드를 작성할 때, 가장 기본이자 중요한 것은 단위테스트라고 생각합니다.
* 단위 테스트는 가장 적은 비용으로 테스트 코드를 작성할 수 있기 때문입니다.

### 마틴 파울러의 테스트 피라미드
<img width="690" alt="스크린샷 2024-03-19 오후 3 07 09" src="https://github.com/mikekks/traces-of-thought/assets/100754581/6a35d0a6-7dea-4096-b051-591516cc7427">

## 계기
* 따라서 단위테스트는 mocking를 통한 독립적인 테스트 환경을 만드는 것이 중요하다고 생각합니다.
* 하지만 컨트롤러 단위 테스트를 진행하다가 문제가 발생했습니다.
* mockMvc 라이브러리를 사용했는데, 해당 컨트롤러 메서드 특징 상 **인증된 유저 객체**를 필터로 부터 받아와야 했기 때문입니다.
* 그래서 mockMvc에 대해서 자세히 살펴봤습니다.

## MockMvc
* 그렇다면 MockMvc의 동작 과정은 어떻게 될까요?
* 처음엔 당연히 필터부터 시작하여 spring context로 들어오는 흐름 인줄 알았습니다.
* 하지만 브레이킹 포인트를 찍어서 디버깅 해본 결과 filter 단에서는 잡히지 않았고, 컨트롤러 단부터 잡히기 시작했습니다.
* 그래서 MockMvc의 코드를 분해 해보기로 했습니다!

## MockMvc 필터
* mockMvc로 컨트롤러 테스트를 진행할 때는 보통 `perform` 이라는 함수를 사용합니다.
* 저는 해당 함수의 동작 과정에 대해 자세히 봐야겠다 생각했습니다!
<img width="915" alt="스크린샷 2024-03-19 오후 3 17 15" src="https://github.com/mikekks/traces-of-thought/assets/100754581/895ab28d-a270-42fa-81a9-9381fe20eca5">

* perform 내에는 위 사진과 같은 코드가 있었습니다.
* 딱 보니 해당 코드가 filter 관련 처리를 한다고 생각했습니다. (실제 필터와 구현이 비슷하게 되어있어서 그렇게 생각했습니다!)
<img width="1209" alt="스크린샷 2024-03-19 오후 3 27 33" src="https://github.com/mikekks/traces-of-thought/assets/100754581/b6898580-ecee-4aff-88cb-c2b383d65726">

* 위 코드는 `MockFilterChain.doFilter()` 메서드의 구현체입니다.
* 해당 코드의 아래 디버거를 보시면, `TestDispatcherServlet` 만 존재하는 것을 확인할 수 있습니다.
* 다시 말해, 현재 어플리케이션에서 제공하는 여러 필터들이 아무것도 설정되지 않음을 알 수 있습니다.

<br></br>
* 그 다음으로 넘어가기 전, **'왜 perform내에 filter 관련 코드가 있지?'** 또는 **'왜 이렇게 구현이 되어있을까?'** 라고 의문이 드신다면 정말 좋은 의문인거 같습니다!
* 사실 그 이유는 어찌보면 너무 당연한 것입니다.
<img width="635" alt="스크린샷 2024-03-19 오후 3 23 04" src="https://github.com/mikekks/traces-of-thought/assets/100754581/da5c580b-36b7-43d7-9eed-e2068aa1a01c">

* 위 그림과 같이 특별한 설정없이 spring context는 web context에 대해서 알 수 없습니다.
* 보통 그래서 `SecurityConfig` 에서 `addFilter` 메서드를 사용하여 필터를 추가하죠.
* MockMvc도 **완전 동일한 이유** 라고 보셔도 좋을 것 같습니다!

## 인증 객체 mocking
* 서론이 길었습니다...ㅋㅋㅋㅋ
* 그래서 저는 처음에 `MockMvc` 에 우리의 `jwtFilter` 를 추가해야겠다고 생각했습니다.
* 근데 이렇게 되면 인증정보를 받기 위해서 mockMvc에 필터를 추가해야 되고, 그렇게 되면 컨트롤러 단위 테스트가 아니라 필터까지도 의존관계가 생기는 문제가 발생합니다.
* 이는 결국 본질적으로 컨트롤러 단위테스트가 될 수 없게 되는 것이죠. (뭔말알이시죠?ㅎ)
* 그래서 저 인증 유저 객체도 Mocking 하는게 좋겠다고 생각이 들었습니다. 

<img width="853" alt="스크린샷 2024-03-19 오후 3 42 04" src="https://github.com/mikekks/traces-of-thought/assets/100754581/798aabec-2e92-4acc-8e4a-d0a65413d0f8">

* 위 그림은 저희가 테스트 해야할 메서드이고, 그 중 `@AuthUser`에 대한 mocking이 필요했습니다.
* 그래서 저희는 `@WithUserDetails` 방법을 사용했습니다.
* 해당 과정도 적으려고 했으나 글이 길어져서 제가 참고했던 자료 첨부하고, 이 후 내용은 다음에 적도록 하겠습니다,,,
* (아래 글이 너무 잘 적혀있어서.. 제 설명이 필요할까 싶기도 하네요...)

## 참고자료
https://tecoble.techcourse.co.kr/post/2020-09-30-spring-security-test/
