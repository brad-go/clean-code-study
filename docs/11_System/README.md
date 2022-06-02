# 11. 시스템

## :pushpin: 이슈 리스트

## :books: 참고

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

#### :sunglasses: yun

최근 스프링을 배우면서 시스템의 구조나 구성을 배웠는데, 정말 좋은 내용인 것 같다. 시스템 단위에서의 관심사 분리로 클린 코드를 만든다니 확실히 유지보수의 비용이 줄어들어서 편할 것 같다.
시스템이 깨끗해지면 확장성이 매우 뛰어나지는 것 같다. 예전 프로젝트를 진행하면서 동작을 추가하거나 수정할 때에 막막했는데 테스트케이스와 모듈로 분리를 해놓는다면 확장성면에서 정말 얻는 것이 많을 것 같다. 나도 깨끗한 시스템을 만드는 것에 노력하고 의존성 주입같은 여러 결합도를 낮추고 응집도를 높이는 기법을 많이 익혀야겠다.
