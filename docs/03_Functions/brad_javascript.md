# 3장 함수

어떤 프로그램이든 가장 기본적인 단위가 함수다. 함수는 읽기 쉽고 이해하기 쉬워야 한다. 의도를 분명히 표현할 수 있어야 한다. 처음 읽는 사람이 내부를 직관적으로 파악할 수 있어야 한다. 다음의 규칙들을 통해 함수를 잘 만드는 방법에 대해 알아보자.

## 목차

1. [작게 만들어라!](#작게-만들어라)
2. [한 가지만 해라!](#한-가지만-해라)
3. [함수당 추상화 수준은 하나로!](#함수-당-추상화-수준은-하나로)
4. [Switch문](#switch문)
5. [서술적인 이름을 사용하라](#서술적인-이름을-사용하라)
6. [함수 인자(인수)](#함수-인수인자)
7. [반복하지마라!](#반복하지마라)
8. [Object.assing이나 destructuring을 통해 기본 객체를 설정해라](#objectassign-이나-destructuring을-통해-기본-객체를-설정하라)
9. [플래그 인수를 사용하지마라](#플래그-인수를-매개변수로-사용하지-마라)
10. [사이드 이펙트를 피하라](#사이드-이펙트를-피하라)
11. [사이드 이펙트를 피하라2](#사이드-이펙트를-피하라-part2)
12. [전역 함수를 생성하지마라](#전역-함수를-생성하지마라)
13. [명령형 프로그래밍보다 함수형 프로그래밍을 사용하라](#명령형-프로그래밍보다-함수형-프로그래밍을-사용하라)
14. [조건을 캡슐화하라](#조건을-캡슐화하라)
15. [부정 조건을 피하라](#부정-조건을-피하라)
16. [조건을 피하라](#조건을-피하라)
17. [type-checking을 피하라](#type-checking을-피하라)
18. [type-checking을 피하라2](#type-checking을-피하라-only-javascript)
19. [과도하게 최적화하지마라](#과도하게-최적화-하지마라)
20. [죽은 코드를 제거하라](#죽은-코드를-제거하라)
21. [iterator와 generator를 사용해라](#iterator반복자와-generator생성자를-사용해라)

## 작게 만들어라!

> **함수를 만들 때 최대한 작게 만들어라.**

함수를 작게 만들라고 하면 얼마나 작게 만들어야 하는 건데? 라고 의문이 들 수 있다. 각 **함수가 하는 이야기를 명백하게 표현할 수 있을 때까지** 작게 만들어야 한다.

#### 블록과 들여쓰기

if/else 문, while 문 등의 반복문에 들어가는 블록은 한 줄이어야 한다. 각 함수에서 들여쓰기 수준이 1단이나 2단을 넘어서지 않아 중첩구조가 생기지 않고, 블록 안에서 호출하는 함수 이름이 적절하다면 코드를 이해하기 쉬워진다.

[:arrow_up: Back to the top](#목차)
<br />

## 한 가지만 해라!

> **함수는 한 가지를 해야 한다. 그 한가지를 잘해야 한다. 그 한가지만을 해야 한다.**

이것은 소프트웨어 개발에서 **가장 중요한 규칙**이다. 함수가 한 가지 이상을 수행할 때 구성, 테스트 및 추론이 어려워진다. 함수를 하나의 작업으로 분리할 수 있으면, **쉽게 리팩토링할 수 있고 코드가 훨씬 더 깔끔하게 읽힌다**.

지정된 함수 이름 아래에서 **추상화 수준**이 하나인 단계만 수행한다면 그 함수는 한 가지 작업만 하는 것이다. 다른 방법으로는 의미 있는 이름으로 다른 함수를 추출할 수 있다면 그 함수를 추출할 수 있다면 그 함수는 여러 작업을 하는 것이다.

### Bad:

```tsx
const emailClients = (clients: Client[]): void => {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) email(client);
  });
};
v;
```

### Good:

```tsx
const emailClients = (clients: Client[]): void => {
  clients.filter(isActiveClient).forEach(email);
};

const isActiveClient = (client: Client) => {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
};
```

[:arrow_up: Back to the top](#목차)
<br />

## 함수 당 추상화 수준은 하나로!

함수가 확실히 ‘**한 가지**’ 작업만 하려면 함수 내 모든 문장의 **추상화 수준**이 동일 해야 한다. 한 함수 내에 추상화 수준이 섞여있다면 코드를 읽는 사람이 헷갈리게 된다. 추상화 수준이 하나인 함수를 구현하기란 쉽지 않다. 하지만 매우 중요하다. 핵심은 **짧으면서도 ‘한 가지'만 하는 함수**다.

위에서 아래로 코드읽기: **내려가기** 규칙

코드는 위에서 아래로 이야기처럼 읽혀야 좋다. 함수 추상화 수준이 한 번에 한단계씩 내려가는 것이 이상적이다. 두 가지 이상의 추상화를 가진 함수는 너무 많은 일을 한다. **함수를 작게 나누면 재사용성과 테스트가 쉬워진다.**

### Bad:

```tsx
const parseCode = (code: string) => {
  const REGEXS = [
    /* ... */
  ];
  const statements = code.split(" ");
  const tokens = [];

  REGEXS.forEach((regex) => {
    statements.forEach((statements) => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach((token) => {
    // lex..
  });

  ast.forEach((node) => {
    // parse...
  });
};
```

### Good:

```tsx
const REGEXS = [
  /* ... */
];

const parseCode = (code: string) => {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);

  syntaxTree.forEach((node) => {
    // parse...
  });
};

const tokenize = (code: string): Token[] => {
  const statements = code.split(" ");
  const tokens: Token[] = [];

  REGEXS.forEach((regex) => {
    statements.forEach((statement) => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
};

const parse = (tokens: Token[]): SyntaxTree => {
  const syntaxTree: SyntaxTree[] = [];
  tokens.forEach((token) => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
};
```

[:arrow_up: Back to the top](#목차)
<br />

## Switch문

switch문(if/else의 연속도 포함)은 작게 만들기 어렵다. 본질적으로 switch문은 N가지를 처리하기 때문이다. 다형성을 이용하여 switch문을 추상 팩토리에 숨기는 방식으로 해결할 수 있다.

### Bad:

```tsx
const calculatePay = (e: Employee): Money => {
  switch (e.type) {
    case EmployeeRecord.COMMISSIONED:
      return new CommissionedEmployee(r);
    case EmployeeRecord.HOURLY:
      return new HourlyEmployee(r);
    case EmployeeRecord.SALARIED:
      return new SalariedEmployee(r);
    default:
      throw new InvalidEmployee(r.type);
  }
};
```

### Good:

```tsx
abstract class Employee {
  public abstract isPayday(): boolean;
  public abstract calculatePay(): Money;
  public abstract deliverPay(pay: Money): void;
}

interface EmployeeFactory {
  makeEmployee(r: R): Employee;
}

class EmployeeFactoryInterface implements EmployeeFactory {
  makeEmployee(r: R): Employee {
    switch (r.type) {
      case EmployeeRecord.COMMISSIONED:
        return new CommissionedEmployee(r);
      case EmployeeRecord.HOURLY:
        return new HourlyEmployee(r);
      case EmployeeRecord.SALARIED:
        return new SalariedEmployee(r);
      default:
        throw new InvalidEmployee(r.type);
    }
  }
}
```

다형적 객체를 생성하는 코드에서만 사용하는 것이 좋은데, 어쩔 수 없는 경우가 있다.

[:arrow_up: Back to the top](#목차)
<br />

## 서술적인 이름을 사용하라!

> **코드를 읽으면서 짐작했던 기능이 각 루틴이 그대로 수행한다면 깨끗한 코드로 불러도 되겠다.**

함수가 작고 단순할수록 서술적인 이름을 고르기도 쉬워진다. 이름이 길어도 괜찮다. 길고 서술적인 이름이 짧고 어려운 이름보다 좋다. 함수 이름을 정할 때는 여러 단어가 쉽게 읽히는 명명법을 사용해서 짓고, 함수 기능을 잘 표현하는 이름을 선택한다.

**서술적인 이름을 사용하면 개발자 머릿속에서도 설계가 뚜렷해지므로 코드를 개선하기 쉬워진다!** 이름을 붙일 때는 **일관성**이 있어야 한다는 것을 잊지말자.

### Bad:

```tsx
const addToDate = (date: Date, month: number): Date => {
  //..
};

const date = new Date();

// 무엇이 더해졌는지 함수 이름으로부터 유추하기 어렵다.
addToDate(date, 1);
```

### Good:

```tsx
const addMonthToDate = (date: Date, month: number): Date => {
  //..
};

const date = new Date();

addMonthToDate(date, 1);
```

[:arrow_up: Back to the top](#목차)
<br />

## 함수 인수(인자)

함수 매개변수의 수를 제한하는 것은 함수 **테스트를 더 쉽게 만들기 때문**에 매우 중요하다. 3개 이상이면 각각의 개별 인수로 수많은 다양한 사례를 테스트해야 경우의 수가 너무 많아진다.

하나 또는 두 개의 인수가 이상적인 경우이며 가능하면 세 가지 인수를 피해야 한다. 그 이상은 통합되어야 한다. 일반적으로 인수가 2개 이상인 경우 함수가 너무 많은 작업을 수행하려고 한다. 그렇지 않은 경우 대부분의 경우 **객체를 인자로 전달**해서 사용할 수 있다.

많은 인수가 필요한 경우 객체 리터럴 사용을 고려해야 한다.

함수가 기대하는 속성을 명확히 하기 위해 구조 분해 구문을 사용할 수 있으며, 몇 가지 이점이 있다.

1. 다른 사람들이 함수 이름을 보면 어떤 속성이 사용되고 있는지 즉시 알 수 있다.
2. 명명된 매개변수를 직접 사용할 수 있다.
3. **Destructuring(구조 분해)은 함수에 전달된 인수 객체의 지정된 기본 값을 복제**한다. 이것은 **side effect를 예방**하는 데 도움이 될 수 있다.
   - 인자로 받은 객체를 받아 구조 분해된 객체 및 배열은 복제되지 않는다.
4. 타입스크립트는 사용되지 않는 속성에 대해 경고해 주는데, 이는 구조 분해 없이는 불가능하다.

### Bad:

```tsx
const createMenu(title: string, body: string, buttonText: string, cancellable: boolean) => {
  // ...
}

createMenu('Foo', 'Bar', 'Bax', true)
```

### Good:

```tsx
const createMenu(options: { title: string, body: string, buttonText: string, cancellable: boolean}) => {
  // ...
}

createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
})
```

더 나은 코드를 작성하고 싶다면 type-alias를 통해 미리 타입을 지정할 수 있다.

```tsx
type MenuOptions = {
  title: string;
  body: string;
  buttonText: string;
  cancellable: boolean;
};

function createMenu(options: MenuOptions) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true,
});
```

[:arrow_up: Back to the top](#목차)
<br />

## 반복하지마라!

코드가 중복되지 않도록 최선을 다해라. 중복 코드는 로직을 변경해야 할 경우 변경할 곳이 여러 곳이 있다는 것을 의미하기 때문에 좋지 않다.

레스토랑을 운영하면서 재고량을 추적한다고 상상해보자. 토마토, 양파, 마늘, 향신료 등. 만약 이것을 보관하고 있는 리스트가 여러 개 있다면, 토마토가 들어간 요리를 제공할 때 모든 것을 업데이트해야 한다. 목록이 하나뿐이라면 업데이트 할 곳은 하나뿐이다.

두 개 이상의 약간의 공통점들 때문에 코드가 중복되는 경우가 많다. 다만, 그 차이점 때문에 같은 일을 많이 하는 두 개 이상의 다른 기능을 가지고 있을 수 밖에 없다. **중복 코드를 제거하는 것은 하나의 함수, 모듈, 클래스로 여러 가지 작업을 처리할 수 있는 추상화를 만드는 것을** 의미한다.

추상화를 올바르게 파악하는 것이 중요하기 때문에 SOLID 원칙을 따라야 한다. 잘못된 추상화는 중복 코드보다 더 나쁠 수 있다. 그래도 반복하지 말고, 추상화를 잘 할 수 있다면 꼭 하자! 그렇지 않으면 한 가지를 변경하고 싶을 때마다 여러 곳을 업데이트 해야 한다.

### Bad:

```tsx
const showDeveloperList = (developers: Developer[]) => {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();

    const data = {
      expectedSalary,
      experience,
      githubLink,
    };

    render(data);
  });
};

const showManagerList = (managers: Manager[]) => {
  managers.forEach((manager) => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();

    const data = {
      expectedSalary,
      experience,
      portfolio,
    };

    render(data);
  });
};
```

### Good:

```tsx
class Developer {
  // ...
  getExtraDetails() {
    return {
      githubLink: this.githubLink,
    };
  }
}

class Manager {
  // ...
  getExtraDetails() {
    return {
      portfolio: this.portfolio,
    };
  }
}

const showEmployeeList = (employee: Developer | Manager) => {
  employee.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();
    const extra = employee.getExtraDetails();

    const data = {
      expectedSalary,
      experience,
      extra,
    };

    render(data);
  });
};
```

코드 중복에 대해 비판적이어야 한다. **때로는 중복된 코드와 불필요한 추상화를 도입함으로써 복잡성이 증가하는 트레이드오프가 발생할 수 있다**. 2개의 다른 모듈로부터의 2개의 실행이 비슷해보이지만, 다른 도메인에 존재하는 경우, 공통 코드를 추출하는 것보다 복제가 받아들여질 수도 있다. 이 경우, 공통 코드는 2개의 모듈 사이에 간접적인 **의존관계**를 가지게 한다.

[:arrow_up: Back to the top](#목차)
<br />

## Object.assign 이나 destructuring을 통해 기본 객체를 설정하라

### Bad:

```tsx
type MenuConfig = {
  title?: string;
  body?: string;
  buttonText?: string;
  cancellable?: boolean;
};

const createMenu = (config: MenuConfig) => {
  config.title = config.title || "Foo";
  config.body = config.body || "Bar";
  config.buttonText = config.buttonText || "Baz";
  config.cancellable = config.cancellable !== undefined;
  // ...
};

createMenu({ body: "Bar" });
```

### Good:

```tsx
type MenuConfig = {
  title?: string;
  body?: string;
  buttonText?: string;
  cancellable?: boolean;
};

const createMenu = (config: MenuConfig) => {
  const menuConfig = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true,
    },
    config
  );
  // ...
  return menuConfig;
};

createMenu({ body: "Bar " });
```

마찬가지로 구조 분해를 사용할 수 있다.

```tsx
type MenuConfig = {
  title?: string;
  body?: string;
  buttonText?: string;
  cancellable?: boolean;
};

const createMenu = ({
  title = "Foo",
  body = "Bar",
  buttonText = "Baz",
  cancellable = false,
}) => {
  // ...
};

createMenu({ body: "Bar " });
```

명시적으로 undefined와 null 값을 전달해서 사이드 이펙트와 예상하지 못한 동작을 방지하기 위해, 타입스크립트 컴파일러 설정을 느슨하게 하면 안된다. `—strictNullChecks` 옵션을 확인해보자.

[:arrow_up: Back to the top](#목차)
<br />

## 플래그 인수를 매개변수로 사용하지 마라

플래그 인수는 사람들에게 함수가 하나 이상의 기능을 한다고 말하는 것과 같다. 함수들은 오직 한 가지 기능을 해야 한다. 만약 **boolean 값에 의해서 다른 코드를 실행하는 함수라면 나누자**.

### Bad:

```tsx
const createFile = (name: string, temp: boolean) => {
  if (temp) {
    fs.create(`./temp${name}`);
  } else {
    fs.create(name);
  }
};
```

### Good:

```tsx
const createTempFile = (name: string) => {
  create(`./temp/${name}`);
};

const createFile = (name: string) => {
  fs.create(name);
};
```

[:arrow_up: Back to the top](#목차)
<br />

## 사이드 이펙트를 피하라

함수는 값을 가져와서 다른 값을 반환하는 것 이외의 다른 작업을 수행할 경우 사이드 이펙트가 생기게 된다. 사이드 이펙트로는 파일을 작성하거나, 글로벌 변수를 수정하거나, 실수로 모든 돈을 낯선 사람에게 송금하는 것들이 있다.

때때로 프로그램에서 사이드 이펙트가 필요할 때가 있다. 위의 예시처럼 파일을 작성하는 경우다. 관련 있는 코드들을 모아두고 싶을 수도 있다. 하지만, 특정 파일을 작성하기 위해 몇가지 함수나 클래스들을 만들 필요도 없다. 한 가지만 만들면 된다. 단 하나.

핵심은 **특정한 구조 없이 객체 간에 상태를 공유**하거나, **무엇이든 쓸 수 있는 가변 데이터 유형을 사용**하거나, **사이드 이펙트가 발생할 수 있는 곳을 집중시키지 않는 것**과 같은 **흔한 위험을 피하는 것**이다.

### Bad:

```tsx
// 아래 함수에 참조되는 전역 변수
let name = "Robert C. Martin";

const toBase64 = () => {
  name = btoa(name);
};

toBase64();
// 이제 이 name 변수를 사용하는 다른 함수가 있었다면 Base64 값이 되버린다.

console.log(name);
```

### Good:

```tsx
const name = "obert C. Martin";

const toBase64 = (text: string): string => {
  return btoa(text);
};

const encodedName = toBase64(name);
console.log(name);
```

[:arrow_up: Back to the top](#목차)
<br />

## 사이드 이펙트를 피하라 (part2)

자바스크립트에서는 변경할 수 없는 값(immuatable)과 변경할 수 있는 값(mutable)이 있다. 객체나 배열의 경우, 가변값이기 때문에 함수에 파라미터로 전달할 때는 신중하게 취급하는 것이 중요하다. 자바스크립트 함수는 객체의 속성을 변경하거나 배열의 내용을 변경하는 등, 다른 곳에서 버그를 일으키기 쉽다.

또, 단순 요소들은 값에 의해 전달되고, 객체나 배열은 참조에 의해 전달된다. 한 함수가 구매할 상품을 담는 것으로 쇼핑 카트 배열을 변화시킨다고 예를 들어 생각해보자. 그러면 이 `cart` (쇼핑 카트) 배열에 의존하는 다른 함수들도 추가적으로 영향을 받게 될 것이다. 좋을 수도 있지만, 나쁠 수도 있다.

**다음 상황을 한 번 상상해보자:**

사용자가 네트워크 요청을 생성하고 카트 어레이를 서버로 보내는 구매 기능을 호출하는 버튼인 ‘구매'를 클릭한다. 네트워크 접속 불량으로 인해 구매 기능은 요청을 계속 재시도 하려고 한다. 그 사이에 사용자가 실제로 원하지 않는 상품의 “카트에 담기" 버튼을 클릭하면 어떻게 될까? 이 경우 네트워크 요청이 시작되면 `addItemToCart` 함수가 불필요한 아이템을 추가하여 변경한 쇼핑카트 배열을 참조하기 때문에 실수로 추가된 아이템이 해당 구매 함수에 의해 전송된다.

`addItemCart` 함수에 대한 최고의 해결책은 항상 카트를 복사한 후에 수정하고 복사본을 반환하는 것이다. 이것은 다른 함수들이 쇼핑 카트 참조에 의해 영향을 받아 변하는 것을 방지해준다.

**이 접근에 대해 언급해야 할 두 가지 주의 사항:**

1. 실제로 입력 객체를 수정하고 싶은 경우도 있지만, 클린 코드 규칙을 따라가다보면 그러한 경우는 매우 드물 것이다. 대부분의 코드들은 사이드 이펙트가 부작용이 없도록 리팩토링 할 수 있다. ([순수 함수](https://jeong-pro.tistory.com/23))
2. 큰 객체를 복사하는 것은 성능적으로 비용이 많이 들 수 있다. 다행히 이러한 프로그래밍 접근 방식은 객체나 배열을 수동으로 복제할 때처럼 메모리를 많이 사용하지 않고, 빠르게 실행할 수 있는 훌륭한 라이브러리가 있기 때문에 실제로 큰 문제가 되지 않는다.

### Bad:

```tsx
const addItemToCart = (cart: CartItem[], item: Item): void => {
  cart.push({ item, date: Date.now() });
};
```

### Good:

```tsx
const addItemToCart = (cart:CartItem[], item: Item): void => {
 return [...cart, { item, date: Date.now() })];
};
```

[:arrow_up: Back to the top](#목차)
<br />

## 전역 함수를 생성하지마라

전역 공간을 오염시키는 것은 자바스크립트에서 나쁜 관습이다. 다른 라이브러리와 충돌하거나 API의 사용자가 프로덕션에서 오류가 날 때까지 전혀알 수 없기 때문이다. 한 가지 예시를 생각해보자. 당신이 자바스크립트의 기본 배열 방식을 확장해서 두 배열 간의 차이를 나타낼 수 있는 `diff` 메서드를 사용하고 싶다면 어떻게 해야할까? 새로운 함수를 배열의 프로토타입(Array.prototype)에 쓸 수 있지만, 동일한 작업을 시도하고 있는 다른 라이브러리와 충돌할 수 있다. 다른 라이브러리가 `diff` 메서드를 배열의 첫번째와 마지막 요소의 차이를 찾기 위해 사용하고 있다면 어떻게 되겠는가? 따라서 클래스를 사용해서 `Array` 배열을 전역으로 확장하는 것이 좋다.

### Bad:

```tsx
declare global {
  interface Array<T> {
    diff(other: T[]): Array<T>;
  }
}

if (!Array.prototype.diff) {
  Array.prototype.diff = function <T>(other: T[]): T[] {
    const hash = new Set(other);
    return this.filter((elem) => !hash.has(elem));
  };
}
```

### Good:

```tsx
class MyArray<T> extends Array<T> {
  diff(other: T[]): T[] {
    const hash = new Set(other);
    return this.filter((elem) => !hash.has(elem));
  }
}
```

[:arrow_up: Back to the top](#목차)
<br />

## 명령형 프로그래밍보다 함수형 프로그래밍을 사용하라

### Bad:

```tsx
const contributions = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500,
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500,
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150,
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000,
  },
];

let totalOutput = 0;

for (let i = 0; i < contributions.length; i++) {
  totalOutput += contributions[i].linesOfCode;
}
```

### Good:

```tsx
const contributions = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500,
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500,
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150,
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000,
  },
];

const totalOutput = contributions.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

[:arrow_up: Back to the top](#목차)
<br />

## 조건을 캡슐화하라

### Bad:

```tsx
if (subscription.isTrial || account.balance > 0) {
  // ...
}
```

### Good:

```tsx
const canActivateService = (subscription: Subscription, account: Account) {
	return subscription.isTrial || acount.balance > 0;
}

if (canActivateService(subscription, account)) {
	// ...
}
```

[:arrow_up: Back to the top](#목차)
<br />

## 부정 조건을 피하라

### Bad:

```tsx
function isEmailUsed(email: string): boolean {
  // ...
}

if (!isEmailUsed(email)) {
  // ...
}
```

### Good:

```tsx
function isEmailNotUsed(email: string): boolean {
  // ...
}

if (isEmailNotUsed(email)) {
  // ...
}
```

[:arrow_up: Back to the top](#목차)
<br />

## 조건을 피하라

이것은 불가능으로 보일 수도 있다. 이것을 처음 들었을 때, 대부분의 사람들은 “`if`문 없이 내가 할 수 있는 것은 아무것도 없어.” 라고 생각한다. 정답은 많은 경우 **다형성을 사용하여 동일한 작업을 수행할 수 있다**는 것이다. 두 번째는 다음과 같다. “좋은 방법이지만 내가 왜 그래야돼?” 정답은 앞에서 배운 클린 코드 개념이다. **함수는 한 가지 일만 수행**한다. if 문장이 있는 클래스 및 함수가 있는 경우 사용자에게 함수가 여러 가지 작업을 수행한다는 것을 알린다는 것이 기억날거다. 기억하자. 오직 한 가지만 한다.

### Bad:

```tsx
class Airplane {
  private type: string;
  // ...

  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
      default:
        throw new Error("Unknown airplane type.");
    }
  }

  private getMaxAltitude(): number {
    // ...
  }
}
```

### Good:

```tsx
abstract class Airplane {
  protected getMaxAltitude(): number {
    // shared logic with subclasses ...
  }

  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

[:arrow_up: Back to the top](#목차)
<br />

## type-checking을 피하라

자바스크립트는 타입이 없다. 이 말은 함수는 아무 인자나 받을 수 있다는 것이다. 때때로 이 자유 때문에 함수의 기능에서 타입 유효성 검사를 하고 싶어진다. 이러한 상황을 피하기 위한 많은 방법이 있다. 첫번째는 **일관된 API**를 고려하는 것이다.

타입스크립트의 경우 자바스크립트의 강력한 문법적 확대집합이고 언어에 선택적 정적 타입 검사를 추가한다. 항상 변수, 파라미터 및 반환값의 타입을 지정하여 타입스크립트의 기능을 최대한 활용하자. 리팩토링이 쉬워질 것이다.

### Bad:

```tsx
const travelToTexas = (vehicle: Bicycle | Car) => {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location("texas"));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location("texas"));
  }
};
```

### Good:

```tsx
type Vehicle = Bicycle | Car;
cosnt travelToTexas = (vehicle: Vehicle) => {
  vehicle.move(this.currentLocation, new Location("texas"));
}
```

[:arrow_up: Back to the top](#목차)
<br />

## type-checking을 피하라 (only javascript)

문자열이나 정수 등의 기본(primitive) 값을 사용하여 작업할 때, 다형성을 사용할 수 없지만 여전히 타입 검사가 필요하다고 느끼면 타입스크립트를 사용하자. 일반 자바스크립트를 수동으로 체크할 때의 문제는 제대로 하려면 너무 많은 전문 용어가 필요하기 때문에 가짜 ‘type-safety'로 인해 가독성을 잃어버리게 되기 때문이다. 자바스크립트를 깔끔하게 유지하고, 좋은 테스트를 작성하고, 좋은 코드 리뷰를 작성하자. 그렇지 않다면 타입스크립트를 사용해라.

### Bad:

```tsx
function combine(val1, val2) {
  if (
    (typeof val1 === "number" && typeof val2 === "number") ||
    (typeof val1 === "string" && typeof val2 === "string")
  ) {
    return val1 + val2;
  }

  throw new Error("Must be of type String or Number");
}
```

### Good:

```tsx
function combine(val1, val2) {
  return val1 + val2;
}
```

[:arrow_up: Back to the top](#목차)
<br />

## 과도하게 최적화 하지마라

최근의 브라우저들은 런타임 시에 대량의 최적화를 한다. 최적화를 위해 시간을 많이 낭비할 필요가 없다. 어디가 최적화가 부족한 부분인지 보여주는 좋은 [리소스](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)가 있다. 이것이 고칠 때까지, 최적화 목표를 정해보자.

### Bad:

```tsx
// 낡은 브라우저들에게 캐시되지 않은 반복문의 길이 연산은 비용이 많이 들었다.
// because of `list.length` recomputation. In modern browsers, this is optimized.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

### Good:

```tsx
for (let i = 0; i < list.length; i++) {
  // ...
}
```

[:arrow_up: Back to the top](#목차)
<br />

## 죽은 코드를 제거하라

죽은 코드는 중복 정도로 나쁘다. 코드 상에 계속 남겨둘 이유가 없다. 사용되지 않는다면 지워버려라! 필요한 경우 버전 내역에 안전하게 남아있을 것이다.

### Bad:

```tsx
function oldRequestModule(url: string) {
  // ...
}

function requestModule(url: string) {
  // ...
}

const req = requestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

### Good:

```tsx
function requestModule(url: string) {
  // ...
}

const req = requestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

[:arrow_up: Back to the top](#목차)
<br />

## iterator(반복자)와 generator(생성자)를 사용해라

stream과 같은 유사 배열객체를 작업할 때는 반복자와 생성자를 사용하자.

- lazy execution(늦은 실행), 요소들은 필요에 의해 전해진다.
- `for-of` 구문을 사용한 요소 반복에 대한 기본 제공 지원
- 반복이 가능하므로 최적화된 반복기 패턴을 구현할 수 있다.

### Bad:

```tsx
function fibonacci(n: number): number[] {
  if (n === 1) return [0];
  if (n === 2) return [0, 1];

  const items: number[] = [0, 1];
  while (items.length < n) {
    items.push(items[items.length - 2] + items[items.length - 1]);
  }

  return items;
}

function print(n: number) {
  fibonacci(n).forEach((fib) => console.log(fib));
}

// 처음 10개의 피보나치 수를 출력한다.
print(10);
```

### Good:

```tsx
// 피보나치 수의 무한 스트림을 생성
// generator가 모든 숫자의 배열을 유지하지는 않는다.
function* fibonacci(): IterableIterator<number> {
  let [a, b] = [0, 1];

  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

function print(n: number) {
  let i = 0;
  for (const fib of fibonacci()) {
    if (i++ === n) break;
    console.log(fib);
  }
}

// 처음 10개의 피보나치 수를 출력한다.
print(10);
```

[:arrow_up: Back to the top](#목차)
<br />

## 퀴즈

1. 함수를 작게 나누고 한 가지 기능만 수행하는 것은 정말 중요합니다. 그런데 이렇게 함으로써 어떤 이익이 있을 까요?

<details><summary><b>정답</b></summary><div markdown="1">

- 재사용성이 증가하고, 테스트가 쉬워집니다. 함수내 추상화 수준도 하나로 유지시키는 거 잊지 않기!

<div></details>

2. 함수의 인자로 최대 2개까지가 이상적이라고 했습니다. 하지만 함수를 만들다보면 불가피하게 인자를 많이 사용해야 하는 경우가 있습니다. 그럴 땐 [ - - ]를 사용하라고 하는데, 이것이 무엇이고 오히려 이것을 사용함으로써 얻는 이점이 뭘까요?

```ts
type MenuOptions = {
  title: string;
  body: string;
  buttonText: string;
  cancellable: boolean;
};

function createMenu(options: MenuOptions) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true,
});
```

<details><summary><b>정답</b></summary><div markdown="1">

- 객체
- 객체를 만들어 전달함으로써 전달하는 인자의 개념을 표현하게 되는 이점이 있다.

<div></details>

<details><summary><b>추상클래스</b></summary><div markdown="1">

```ts
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
    if (this.constructor === Animal) {
      throw new Error("추상 클래스 인스턴스를 생성하였습니다.");
    }
  }

  move() {
    throw new Error("추상 메서드는 꼭 오버라이딩 되어야 합니다.");
  }
}

class Dog extends Animal {
  constructor(name: string) {
    super(name);
  }

  move() {
    console.log("멍멍!");
  }
}

// const animal = new Animal("동물");
const dog = new Dog("개");
dog.move();
```

</div></details>

[:arrow_up: Back to the top](#목차)
<br />
