# 5장 형식 맞추기

형식은 주관적이다. 이번 챕터에서 다룰 많은 규칙들이 있지만, 반드시 따라야 하는 규칙은 없다. 요점은 다음과 같다.

- **개인이든 팀이든 규칙을 정하고 그 규칙을 따라 일관적인 코드를 작성한다.**
- **형식을 맞추기 위해 논쟁할 필요가 없다. 자동화 도구를 사용하자.**

## 목차

1. [형식을 맞추는 목적](#형식을-맞추는-목적)
2. [일관된 대문자 사용](#일관된-대문자-사용)
3. [호출하는 함수와 호출되는 함수를 가까이 배치](#호출하는-함수와-호출되는-함수를-가까이-배치)
4. [세미콜론](#세미-콜론)
5. [imports 정렬](#imports-정렬하기)
6. [절대 경로를 사용하자](#절대-경로를-사용하자)
7. [세로 정렬](#세로-정렬)
8. [가로 길이](#가로-길이)
9. [가로 공백과 밀집도](#가로-공백과-밀집도)
10. [들여쓰기](#들여쓰기)
11. [중첩 레벨](#중첩-레벨)

## 형식을 맞추는 목적

> 코드 형식은 **의사 소통**의 일환이며, 이는 전문 개발자의 일차적인 의무이다.

‘돌아가는 코드'가 개발자의 일차적인 의무처럼 느껴질지라도 다시 생각해보자. **오늘 구현한 코드의 가독성은 앞으로도 바뀔 코드의 품질에 지대한 영향을 미친다.**

맨 처음 잡아놓은 구현 스타일과 가독성 수준은 유지보수 용이성과 확장성에 계속 영향을 미친다. 결국 이 말은 우리의 코딩 스타일이 회사의 비즈니스에 영향을 미친다는 뜻이다. 형식은 정말 중요하다!

**[⬆ back to top](#목차)**
<br />

## 일관된 대문자 사용

대문자 사용은 변수, 기능 등에 대해 많은 것을 알려준다. 이런 규칙은 주관적이므로 팀이 원하는 대로 선택할 수 있다. 핵심은 무엇을 선택하든 **일관성**을 유지하는 것이다.

### Bad:

```tsx
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

### Good:

```tsx
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

const eraseDatabase = () => {};
const restoreDatabase = () => {};

class Animal {}
class Alpaca {}
```

**[⬆ back to top](#목차)**
<br />

## 호출하는 함수와 호출되는 함수를 가까이 배치

함수가 다른 함수를 호출하는 경우 해당 함수를 소스 파일에서 수직으로 가깝게 유지한다. 이상적으로는 호출하는 함수를 호출되는 함수 바로 위에 두는 것이다. 우리는 신문처럼 위에서 아래로 코드를 읽는다. 그러므로 **코드를 위에서 아래로 읽히도록 작성**한다.

### Bad:

```tsx
class PerformanceReview {
  constructor(private readonly employee: Employee) {}

  private lookupPeers() {
    return db.lookup(this.employee.id, "peers");
  }

  private lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  private getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  review() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();

    // ...
  }

  private getManagerReview() {
    const manager = this.lookupManager();
  }

  private getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.review();
```

### Good:

```tsx
class PerformanceReview {
  constructor(private readonly employee: Employee) {}

  review() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
    // ...
  }

  private getPeerReviews() {
    const peers = this.lookupPeers();
  }

  private getManagerReview() {
    const manager = this.lookupManager();
  }

  private getSelfReview() {
    // ...
  }

  private lookupPeers() {
    return db.lookup(this.employee.id, "peers");
  }

  private lookupManager() {
    return db.lookup(this.employee, "manager");
  }
}
```

**[⬆ back to top](#목차)**
<br />

## 세미 콜론

자바스크립트에서는 다른 언어와 달리 문장의 끝에 세미 콜론을 붙이지 않아도 잘 동작한다. 자동으로 세미 콜론을 삽입해주는 기능이 있기 때문이다.

그래서 세미 콜론을 붙여야 한다, 말아야 한다라는 상반대는 주장이 대립되고는 한다. [이 글](https://www.codingem.com/javascript-semicolon-usage/)을 읽어보면 알 수 있겠지만, 세미 콜론을 붙이지 않는 것보다 **세미 콜론을 붙이는 것이 더 좋은 이유가 많다**.

팀에서 사용하지 않기로 결정했다면, 그 방식에 따른다. 하지만 중요한 것은 **일관성**이다. 붙이지 않을거면 확실히 붙이지 않고, 붙일거면 제대로 붙여줘야 한다.

### Bad:

```tsx
const s = "World";
const ab = "Hello" + s[3].forEach((n) => console.log(n)); // TypeError s[3].forEach is not a function at <anonymous>:4:5
```

### Good:

```tsx
const s = "World";
const ab = "Hello" + s;

[3].forEach((n) => console.log(n)); // 3
```

**[⬆ back to top](#목차)**
<br />

## imports 정렬하기

깔끔하고 가독성이 좋은 `import` 문의 상태는 현재 코드의 의존성을 빠르게 확인할 수 있게 해준다. 다음의 글을 보고 `import` 문을 정리하자.

- `import` 문은 알파벳 순으로 그룹화한다.
- 사용하지 않는 `import` 문은 제거한다.
- 다음 순서를 준수한다.
  - 노드 내장 모듈 및 라이브러리 - `import fs from ‘fs’;`
  - 외부 모듈 및 라이브러리 - `import styled from ‘styled-components’;`
  - 내부 모듈 - `import { UserSevice } from ‘src/services/userService’;`
  - 상위 디렉토리의 모듈 - `import foo from ‘../foo’;`
  - 동일하거나 같은 레벨의 디렉토리의 있는 모듈 - `import bar from ‘./bar’;`

### Bad:

```tsx
import { TypeDefinition } from "../types/typeDefinition";
import { AttributeTypes } from "../model/attribute";
import { ApiCredentials, Adapters } from "./common/api/authorization";
import fs from "fs";
import { ConfigPlugin } from "./plugins/config/configPlugin";
import { BindingScopeEnum, Container } from "inversify";
import "reflect-metadata";
```

### Good:

```tsx
import "reflect-metadata";

import fs from "fs";
import { BindingScopeEnum, Container } from "inversify";

import { AttributeTypes } from "../model/attribute";
import { TypeDefinition } from "../types/typeDefinition";

import { ApiCredentials, Adapters } from "./common/api/authorization";
import { ConfigPlugin } from "./plugins/config/configPlugin";
```

**[⬆ back to top](#목차)**
<br />

## 절대 경로를 사용하자

import 구문을 더 깔끔하게 만드는 방법으로 `tsconfig.json` 파일의 paths와 baseUrl을 속성을 compilerOptions 부분을 설정해서 절대 경로를 사용하는 방법이 있다.

긴 상대 경로의 사용을 피하자

### Bad:

```tsx
import { UserService } from "../../../services/UserService";
```

### Good:

```tsx
import { UserService } from "@services/UserService";
```

```tsx
// tsconfig.json
...
  "compilerOptions": {
    ...
    "baseUrl": "src",
    "paths": {
      "@services": ["services/*"]
    }
    ...
  }
...
```

**[⬆ back to top](#목차)**
<br />

## 세로 정렬

빈 행을 논리 블록 사이에 넣어 코드를 분리해야 한다. 빈 행은 새로운 개념을 시작한다는 시각적 단서이다. 변수 선언, 반복문, 리턴문 등의 사이에 세로로 빈 행을 넣어 코드를 분리해주자.

### Bad:

```tsx
const pow(x: number, n: number): number {
	let result: number = 1;
	for (let i = 0; i < n; i++){
		result *= x;
	}
	return result;
}
```

### Good:

```tsx
const pow(x: number, n: number): number {
	let result: number = 1;

	for (let i = 0; i < n; i++){
		result *= x;
	}

	return result;
}
```

**[⬆ back to top](#목차)**
<br />

## 가로 길이

가로로 길게 늘어진 코드를 읽는 걸 좋아하는 개발자는 없다. 코드의 가로 길이가 길어진다면 여러 줄로 나눠서 작성하자. 일반적으로 80 - 120자 정도를 사용한다.

### Bad:

```tsx
if (id === 123 && moonPhase === "Waning Gibbous" && zodiacSign === "Libra") {
  letTheSorceryBegin();
}
```

### Good:

```tsx
if (id === 123 && moonPhase === "Waning Gibbous" && zodiacSign === "Libra") {
  letTheSorceryBegin();
}
```

**[⬆ back to top](#목차)**
<br />

## 가로 공백과 밀집도

공백을 사용해 밀접한 개념과 느슨한 개념을 표현한다. 예를 들어 할당문 같은 경우 공백을 넣어 두 요소가 나뉘는 개념이라는 것을 알리고, 함수 이름과 괄호 사이에는 공백을 넣지 않는다. 함수와 인수는 서로 밀접하기 때문이다.

**공백을 넣으면 한 개념이 아니라 별개로 보인다**. 함수의 인자는 공백으로 분리한다. 쉼표를 강조해 인수가 별개라는 사실을 보여주기 위해서이다.

### Bad:

```tsx
const pow(x:number,n:number):number {
	let result:number=1;

	for (let i=0; i<n; i++){
		result*=x;
	}

	return result;
}
```

### Good:

```tsx
const pow(x: number, n: number): number {
	let result: number = 1;

	for (let i = 0; i < n; i++){
		result *= x;
	}

	return result;
}
```

**[⬆ back to top](#목차)**
<br />

## 들여쓰기

들여쓰기를 통해 우리는 코드 내에서 각 코드의 범위(스코프)를 표현할 수 있다. 들여쓰기 정도는 각 코드 블록이 영향을 미칠 범위의 정도에 비례한다. 별거 아닌 것 같지만, 들여쓰기가 없다면 사람이 코드를 읽는 것은 거의 불가능할 것이다.

### Bad:

```tsx
class PerformanceReview { constructor(private readonly employee: Employee) {}
review() { this.getPeerReviews(); this.getManagerReview(); this.getSelfReview();
// ... } private getPeerReviews() { const peers = this.lookupPeers(); private
getManagerReview() { const manager = this.lookupManager(); } private getSelfReview() {
// ... } private lookupPeers() { return db.lookup(this.employee.id, "peers"); }
private lookupManager() { return db.lookup(this.employee, "manager"); }}
```

### Good:

```tsx
class PerformanceReview {
  constructor(private readonly employee: Employee) {}

  review() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
    // ...
  }

  private getPeerReviews() {
    const peers = this.lookupPeers();
  }

  private getManagerReview() {
    const manager = this.lookupManager();
  }

  private getSelfReview() {
    // ...
  }

  private lookupPeers() {
    return db.lookup(this.employee.id, "peers");
  }

  private lookupManager() {
    return db.lookup(this.employee, "manager");
  }
}
```

**[⬆ back to top](#목차)**
<br />

## 중첩 레벨

가능하면 너무 깊은 중첩문은 사용하지 않는다. 반복문을 사용할 때 중첩문의 깊이가 깊어지면 `early return` 이나 `continue` 문을 통해 중첩 레벨을 줄여 코드의 가독성을 높일 수 있다.

### Bad:

```tsx
function pow(x, n) {
  if (n < 0) {
    alert("'n'은 음수가 될 수 없습니다.");
  } else {
    let result = 1;

    for (let i = 0; i < n; i++) {
      result *= x;
    }

    return result;
  }
}
```

### Good:

```tsx
function pow(x, n) {
  if (n < 0) {
    alert("'n'은 음수가 될 수 없습니다.");
    return;
  }

  let result = 1;

  for (let i = 0; i < n; i++) {
    result *= x;
  }

  return result;
}
```

**[⬆ back to top](#목차)**
