## 이유

1. 컨트롤러에 인수가 너무 많아서 좀 지저분해 보인다…ㅋㅋ 뿐만 아니라 저거 인수 하나하나 넣는 것도 크진 않지만 리소스라고 생각한다…
<img width="936" alt="스크린샷 2024-02-29 오후 9 34 55" src="https://github.com/mikekks/traces-of-thought/assets/100754581/7a13a4d2-bddd-4803-b04f-ef0a91cfb38b">  

<br><br/>
  
2. 매퍼에 그냥 dto하나만 넣어도 되는데 그럴 경우 아래 문제가 발생한다.
    1. enum 변환 과정에서 `IllegalArgumentException` 이 발생함. 하지만 해당 에러는 개발자가 통제할 수 없는 에러가 아니다. 최대한 예외는 개발자가 만든 `CustomException` 으로 잡아내는 것이 가장 이상적이다.
    2. Dto 내의 변수 이름과 엔티티의 변수 이름이 똑같아야 구현 인터페이스(`MapperImpl`)에서 같은 것임을 인식할 수 있다. 그 말은 즉슨 Dto의 유연성이 많이 떨어짐을 반증한다.
