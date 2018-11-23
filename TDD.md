# **TDD(Test-driven Development)**<br>테스트 주도 개발 방법론이란?
TDD 를 알아보기 전에 먼저 일반적인 개발 프로세스를 알아보자.  
## 일반적인 개발 프로세스
일반적으로 개발 절차는 먼저 어떻게 개발할지 디자인(설계)하고 디자인을 바탕으로 실제 코드를 작성하고 최종적으로 테스트를 작동시켜보면서 해보는 과정이었다.(디자인->개발->테스트)  
  
이제 새로운 방식인 TDD로 넘어가보자.  
## TDD의 정의  
**Test Driven Development**  
테스트 주도 개발: 테스트가 개발을 이끌어 나간다.  
테스트를 먼저 만들고 테스트를 통과하기 위한 것을 짜는 것 즉, 만드는 과정에서 우선 테스트를 작성하고 그걸 통과하는 코드를 만들고를 반복하면서 제대로 동작하는지에 대한 피드백을 적극적으로 받는 것이다.  
## TDD 프로세스  
* RED : 실패하는 테스트를 만들기  
* GREEN : 테스트에 통과할 만한 코드를 작성하기.  
* REFACTOR : 반복되는 코드, 긴 메소드, 큰 클래스, 긴 매개변수 목록 등등 코드를 좀 더 효율적으로 바꾸기.  
## TDD의 장단점  
1. TDD를 하면 개발 시간이 늘어난다.  
    * 개발 시간이 TDD를 하지않을 때에 비해 대략 10~30%가 늘어난다.  
2. TDD를 하면 결함이 줄어든다.  
    * 결함이 1/2 ~ 1/10 까지 줄어든다.  
    * SW를 개발하면서 예상하지 못했던 시간을 많이 소요하는 것은 대부분이 버그때문이다.TDD를 하면 이런 버그를 줄일 수 있다.  
3. TDD를 하면 코드 복잡도가 떨어진다.  
    * 엔트로피(Entropie)가 낮아진다.  
    * 깨끗한 코드 가 나온다.
    * 유지보수 비용이 낮아진다.