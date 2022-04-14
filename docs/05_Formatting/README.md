# 5. Form

## :pushpin: 이슈 리스트

- [함수의 선언 순서](https://github.com/brad-go/clean-code-study/issues/24)

## :clipboard: 정리

### :link: 문서 링크

- [함수 (자바)](./heewhy_java.md)
- [함수 (자바스크립트)](./brad_javascript.md)

### :white_check_mark: 주요 내용

- 코드 형식은 의사소통의 일환이다.
- 버전이 바뀌어도 초기의 스타일과 가독성 수준은 유지보수 용이성과 확장성에 영향을 미친다.
- 적절한 자바 소스 파일의 크기 : 평균 65줄
- 적절한 행 길이 : 평균 45자, 120자 제한

## :pray: 회고

#### :bread: brad

> **형식**: 일을 할 때의 일정한 절차, 방법, 양식 또는 그 한 무리의 물건을 특정짓는, 공통적으로 갖춘 모양

여기서 사용된 형식의 사전적인 의미는 위와 같다. 의미 그대로 올바른 형식을 정의하고 지키는 것은 매우 중요하다. 우리는 여럿이지만, **하나의** 서비스를 만들기 때문이다.
어떤 형식을 정의하든 가장 중요한 것은 **일관성**인 것 같다. 일관적인 코드는 가독성을 높이고 유지보수에 용이하다. 그리고 처음 정의한 형식 수준은 코드의 품질을 좌우하고 이는 서비스의 품질과 직결된다.
올바른 형식을 통해 한 눈에 들어오는 코드를 작성하려고 노력해야 겠다.

#### :sunglasses: yun

막상 코드를 작성할 때 세로나 가로의 길이 제한을 생각하지 못했는데 여러 라이브러리를 보면서 제한을 두는 것이 맞다고 생각이 들었다. 머리 속으로 제한을 걸면 코드는 더 간결해질 것 같다. 그리고 예전의 프로젝트들을 진행하면서 코드를 처음에 지저분하게 작성했던 적이 있는데 초기의 수준이 계속 유지되어 동작만 간신히되고 다른 팀원과의 협업도 어려웠다. 앞으로 프로젝트를 진행할 떄는 팀 내 규칙을 먼저 정하고 해야겠다.