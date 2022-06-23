# 13. 동시성

## :pushpin: 이슈 리스트

## :books: 참고

- [[JS]자바스크립트의 동시성](https://velog.io/@minn602/JS%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%9D%98-%EB%8F%99%EC%8B%9C%EC%84%B1)
- [[JavaScript] JavaScript 파헤치기 - 동작 원리](https://tristy.tistory.com/51)

## :clipboard: 정리

### :link: 문서 링크

- [동시성 (자바스크립트)](./brad_javascript.md)
- [동시성 (자바)](./heewhy_java.md)

### :white_check_mark: 주요 내용

#### 자바스크립트

- 자바스크립트에서 동시성이 지원 가능한 이유는 런타임 덕분이다.
- 싱글 스레드인 자바스크립트가 멀티 스레드 환경인 런타임의 힘을 빌려 동시성 지원이 가능해진다.
- 콜백보다는 프로미스를 프로미스보다는 async, await문법을 사용하자

## :pray: 회고

#### :bread: brad

자바스트립트는 싱글 스레드 언어이기 때문에, 비동기 동작을 통해 지원한다. 평소에 프로젝트 진행 시, api를 통해 데이터를 가져올 때 자주 사용하던 방식이다. 그러나 이번 장을 통해 어떻게 동작하는지 다시 한 번 공부할 수 있었고, 조금 더 다양한 방식으로 사용될 수 있다는 것을 알게되었다.

#### :sunglasses: yun
