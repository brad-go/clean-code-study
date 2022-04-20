# 7. Error Handling

## :pushpin: 이슈 리스트

- [예외 감싸기](https://github.com/brad-go/clean-code-study/issues/35)

## :books: 참고

- [자바스크립트 에러 처리(Error Handling) 총정리](https://blogpack.tistory.com/1070)
- [[Node.js] Logging 라이브러리 winston 적용하기](https://velog.io/@ash/Node.js-%EC%84%9C%EB%B2%84%EC%97%90-logging-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-winston-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0)
- [예외 처리](https://radlohead.gitbook.io/typescript-deep-dive/type-system/exceptions)
- [커스텀 에러와 에러 확장](https://ko.javascript.info/custom-errors)

## :clipboard: 정리

### :link: 문서 링크

- [오류 처리 (자바)](./heewhy_java.md)
- [오류 처리 (자바스크립트)](./brad_javascript.md)

### :white_check_mark: 주요 내용

- 적절하게 에러를 발생시키고 전후 상황의 정보를 담은 에러를 전달할 수 있게 하자!
- 에러 처리가 프로그램 논리를 흐리지 않고, 분리될 수 있도록 하자
- 적절한 예외 처리를 통해 에러가 발생해도 프로그램의 코드를 정상흐름으로 돌릴 수 있다.
- 어플리케이션에서 오류를 정의할 때 프로그래머에게 가장 중요한 관심사는 "오류를 잡아내는 방법"이 되어야 한다.
- 프로그래머가 에러 처리를 통해 실제 필요로 하는 것은 언제 어디서 에러가 발생했는지에 대한 정보이다.
- 의존성을 줄이기 위해 분기문을 통해 발생할 수 있는 모든 에러 클래스를 만들고 추가하는 것보다, 추상적인 에러 하나로 에러들을 감사서 처리하는 방법인 예외 감싸기를 사용하자.
- 오류 처리를 프로그램 논리와 분리해 독립적인 추론이 가능하게 하고 코드 유지보수성을 높이자.

## :pray: 회고

#### :bread: brad

#### :sunglasses: yun