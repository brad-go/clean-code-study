# 3. Functions

## :pushpin: 이슈 리스트

- [시간적인 결합](https://github.com/brad-go/clean-code-study/issues/16)

## 참고

- [Temporal coupling](https://enterprisecraftsmanship.com/posts/temporal-coupling-and-immutability/)
- [SOLID 원칙](https://www.nextree.co.kr/p6960/)
- [순수 함수란?](https://jeong-pro.tistory.com/23)
- [type-alias](https://www.typescriptlang.org/docs/handbook/advanced-types.html#interfaces-vs-type-aliases)
- [Object.assign](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)
- [Optimization killers](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)
- [Iterator & Generator](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Iterators_and_Generators)

## :clipboard: 정리

### :link: 문서 링크

- [함수 (자바)](./heewhy_java.md)
- [함수 (자바스크립트)](./brad_javascript.md)

### :white_check_mark: 주요 내용

> 작고 한 가지만 하는 함수를 만들어라!

- 프로그램의 가장 기본적인 단위는 함수이다.
- 함수가 **하는 일을 명확히 알 수 있게 작게 만들어라**!
- 함수는 **한가지**만 해야한다.
  - 소프트웨어 개발에서 가장 중요한 규칙이며, 쉽게 **리팩토링**할 수 있고, 더 나은 **가독성**을 가지게 한다.
- 일관적이고 서술적인 이름을 사용해서 **함수의 기능을 유추할 수 있게** 한다.
- 함수의 **인자는 2개 이하**가 이상적이다.
- 반복하지 않는다. (중복 배제)
  - 중복 코드를 제거하는 것은 하나의 함수, 모듈, 클래스로 여러 가지 작업을 처리할 수 있는 추상화로 만드는 것.
  - SOLID 원칙을 따라 추상화를 하며 **함수 내 추상화 수준은 하나로 통일**한다.
- 플래그 인수를 매개 변수로 사용하지 않는다.
  - boolean 값에 의해 다른 코드를 실행하는 함수라면 나눈다!
- **사이드 이펙트(부수 효과)를 일으키지 마라**.
  - 함수는 값을 가져와서 다른 값을 반환하는 것 이외의 다른 작업을 수행하는 것은 사이드 이펙트를 일으킨다.
- 조건을 피하라 (switch문)
  - 많은 경우에 if문 없이도 **다형성을 사용하여 동일한 작업을 수행할 수 있다**.

## :pray: 회고

#### :bread: brad

자바와는 프로그래밍 방식이 다르기 때문에 책을 이해하기 조금 어려운 챕터였다. 자바는 클래스를 떠나 존재하는 것은 있을 수 없기에 함수는 없고, 메서드만이 존재한다는 것을 처음 알았다.
다른 의미로 자바스크립트가 프로토타입 기반의 함수형 프로그래밍 언어이기 때문에, 정말 중요한 챕터였다. 그만큼 내용도 많고, 알아야 할 것도 많았다. 그래도 내가 생각한 좋은 함수를 요약을 하자면 다음과 같다.

> 이름을 통해 함수의 기능을 유추할 수 있고, 작아서 한 가지 기능만 하며, 인자는 2개 이하로 가지고 반복되지 않는 함수가 좋은 함수다!

이외에도 더 다양한 좋은 함수를 작성하는 방법이 있었지만 위의 내용을 지킨다면 어느 정도는 나머지도 지켜질 것 같다. 정말 중요한 함수, 이것만큼은 잘하고 싶다!

#### :sunglasses: yun
가장 와닿았던 것은 추상화 수준이라는 단어가 와닿았다. 나도 함수를 구현할 때에는 구조를 생각하고 코딩을 하다보면 갑자기 함수의 레벨이 틀리는 경우가 있었는데 추상화 수준이라는 단어를 듣고 다시 생각해보니 추상화의 수준이 안맞았던 것 같다. 앞으로 함수의 구조를 생각하며 계획할 때 추상화 수준을 고려해야겠다.