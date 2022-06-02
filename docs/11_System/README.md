# 11. 시스템

## :pushpin: 이슈 리스트

## :books: 참고

- [관심사의 분리(Separation of Concerns)](https://kwangyulseo.com/2015/05/29/%EA%B4%80%EC%8B%AC%EC%82%AC%EC%9D%98-%EB%B6%84%EB%A6%ACseparation-of-concerns/)
- [의존성 주입 in TypeScript (2) - IoC 컨테이너와 Inversify.js](https://velog.io/@server30sopt/IoC-Container)
- [AOP : Aspect Oriented Programming 개념 | Moon](https://gmoon92.github.io/spring/aop/2019/01/15/aspect-oriented-programming-concept.html)
- [Aspect-Oriented Programming in JavaScript](https://blog.bitsrc.io/aspect-oriented-programming-in-javascript-c4cb43f6bfcc)
- [AOSD, 관점지향 소프트웨어 개발](http://aosd.net)
- [DSL, 도메인 특화 프로그래밍 언어](http://en.wikipedia.org/wiki/Domain-specific_programming_language)
- [Fowler, 제어 컨테이너 역전과 의존성 주입 패턴](http://martinfowler.com/articles/injection.html)
- [Spring, Spring 프레임워크](http://www.springsource.org/)

## :clipboard: 정리

### :link: 문서 링크

- [시스템 (자바스크립트)](./brad_javascript.md)
- [시스템 (자바)](./heewhy_java.md)

### :white_check_mark: 주요 내용

시스템은 역시 깨끗해야 한다. 도메인 논리가 흐려지면 제품 품질이 떨어진다. 버그가 숨어들기 쉬워지고, 스토리를 구현하기 어려워지기 때문이다. 기민성이 떨어지면
생산성이 낮아져 TDD가 제공하는 장점이 사라진다.
모든 추상화 단계에서 의도는 명확히 표현해야 한다.

## :pray: 회고

#### :bread: brad

"관심사의 분리" 정말 많이 들은 말이었다. 이번 장에서 조금 더 확실히 알 수 있었는데, 결국 책임을 분리하라는 말과 같다고 생각했다. 각 기능 별로 맡은 일만 수행해야지 다른 일까지 해버리면 안된다는 말이다.
관심사를 넘어 관점지향 프로그래밍인 AOP에 대해서도 공부할 수 있었는데, 약간이라도 코드로 작성해 실행해봤는데, 정말 신기했다. 로깅, 보안 등에 자주 사용된다고 하는데, 조금 더 익혀서 프로젝트에서 사용해봐야겠다.

#### :sunglasses: yun
