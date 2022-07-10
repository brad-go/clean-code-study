# 15. Testing Tools

본래의 책의 15단원은 'JUnit 들여다보기' 입니다. 그러나 저희는 JavaScript와 Java를 다루는 클린코드 스터디를 하고 있기 때문에, Java의 경우 그대로 'JUnit 들여다보기'를 진행하고 JavaScript는 본 스터디의 목적에 어긋날 수 있지만, NHN 강동우 님의 '실용적은 프론트엔드 테스트 전략'을 토대로 어떤 테스트 도구를 통해 더 나은 테스트를 진행할 수 있는지에 다루기로 결정했습니다.

## :books: 참고

- [[2019] 실용적인 프런트엔드 테스트 전략](https://www.youtube.com/watch?v=q9d631Nl0_4)
- [Jest + React testing library vs Cypress 컴포넌트 테스트 비교](https://cocoder16.tistory.com/67)
- [React Testing Library를 이용한 선언적이고 확장 가능한 테스트](https://ui.toast.com/weekly-pick/ko_20210630)

## :clipboard: 정리

### :link: 문서 링크

- [실용적인 프론트엔드 테스트 전략 (자바스크립트)](./brad_javascript.md)
- [Junit 들여다보기 (자바)](./heewhy_java.md)

### :white_check_mark: 주요 내용

#### JavaScript

- 프론트엔드 테스트는 입/출력 데이터가 데이터로써 모호하기 때문에 테스트가 어렵다.
- Storybook, Cypress를 적극 활용하자.
- 테스트는 비용이다. 불필요한 테스트를 최소화하고 한 기능을 수행하는 모듈들의 통합 테스트를 우선적으로 검토하자.

#### Java

## :pray: 회고

#### :bread: brad

이번 파트는 NHN 강동우님의 영상을 참고해서 작성했다. 사실 테스트를 더 깔끔하게 작성하는 방법이나, 본문에 맞게 리팩토링하는 방법에 대해 알아보는 것이 더 이 스터디의 주제에 맞다고 생각한다. 하지만 실제 프로젝트에서 테스트를 다루면서 **어떤 테스트 도구를 사용하고, 어떤 방식으로 테스트를 진행해야 하는지가 우선적인 고민으로 다가왔고, 그것을 다루는 게 더 나은 공부방법이라고 생각했기에 이러한 주제를 선택하게 되었다**.

영상을 보고 한가지 고민이 되던 것은 cypress만으로 기능 개발이 다 맏길 수 있느냐이다. cypress를 사용해보지 않았지만, 어떤 방식으로 동작하고 테스트에 어떠한 도움을 주는지는 알고 있다. 그래서 개발 시에 테스트에 어떠한 도움을 줄지 알 수 있다. 나는 영상이 만들어진, 2019년에 비해 jest나 testing-library가 발전했다고 생각한다. 그러므로 나는 다음과 같은 방법으로 테스트를 진행해야 한다고 생각한다.

- **Jest + Testing-library를 통한 단위 및 통합 테스트**
- **Cypress로 E2E 테스트**

#### :sunglasses: yun
