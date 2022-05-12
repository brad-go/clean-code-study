# 10-1. SOLID

## 목차

1. [단일 책임의 원칙(SRP)](#단일-책임의-원칙single-responsibility-principle-srp)
2. [개방 폐쇄의 원칙(OCP)](#개방-폐쇄의-원칙open-closed-principle-ocp)
3. [리스코프 치환의 원칙(LSP)](#리스코프-치환의-원칙liskov-substitution-principle-lsp)
4. [인터페이스 분리의 원칙(ISP)](#인터페이스-분리의-원칙interface-segregation-principle-isp)
5. [의존성 역전의 원칙(DIP)](#의존성-역전의-원칙dependency-inversion-principle-dip)

## 단일 책임의 원칙(Single Responsibility Principle, SRP)

클린 코드에 명시된 바와 같이, “**클래스는 책임, 즉 변경할 이유가 하나여야 한다.**”는 클래스나 모듈을 변경할 이유가 두가지 이상일 수 없다는 원칙이다. 비행기에 여행 가방을 하나만 챙겨갈 수 있듯이, 클래스에 많은 기능을 부여하는 것은 유혹적이다. 그러나 이것은 클래스가 개념적으로 **응집**되지 못할 것이고, **변경**해야할 많은 이유가 생길 것이다.

클래스 변경에 필요한 시간을 최소화하는 것이 중요하다. 한 클래스에 너무 많은 기능이 있는데, 그 중 일부를 수정할 시에 다른 종속 모듈에 어떤 영향을 미치는지 이해하기 어려울 수 있기 때문이다.

자잘한 단일 책임 클래스가 많아지면 큰 그림을 이해하기 어려워진다고 우려하는 사람들도 있지만, 큰 다목적 클래스 몇개로 이뤄진 시스템은 변경 시에 당장 알 필요가 없는 사실까지 알아야 하는 어려움을 가져다 준다.

그러므로 **체계적인 정리**를 통해 개발자가 무엇이 어디있는지 쉽게 찾을 수 있게 하고, 변경 시에 직접 영향이 미치는 컴포넌트만 이해할 수 있게 만들어야 한다. 즉 ,**큰 클래스 몇 개가 아니라 작은 클래스 여럿으로 이루어진 시스템이 더 바람직하다. 작은 클래스는 각자 맡은 책임이 하나며, 변경할 이유가 하나며, 다른 작은 클래스와 협력해 시스템에 필요한 동작을 수행한다.**

### Bad:

```tsx
class UserSettings {
  constructor(private readonly user: User) {}

  // 유저 정보 변경
  changeSettings(settings: UserSettings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  // 유저 권한 검증
  verifyCredentials() {
    // ...
  }
}
```

위 코드는 두 가지 책임을 가지고 있다.

- 유저의 권한 검증
- 유저의 정보 변경

이러한 설계는 이슈를 불러온다. 어플리케이션이 유저의 권한을 검증하는 부분에 영향을 주도록 변경된다면, 변경사항에 맞춰 `verifyCredentials`를 건드릴 수 밖에 없고, `changeSettings`도 마찬가지로 수정해야 한다. 이렇게 **하나의 클래스가 여러 책임을 지게 되면 수정해야 할 이유도 많아지고, 이러한 이유가 많아질수록 이슈가 발생할 확률이 높아진다.**

### Good:

```tsx
class UserAuth {
  constructor(private readonly user: User) {}

  verifyCredentails() {
    // ...
  }
}

class UserSettings {
  private readonly auth: UserAuth;

  constructor(private readonly user: User) {
    this.auth = new UserAuth(user);
  }

  changeSettings(settings: UserSettings) {
    if (this.auth.verifyCredentails()) {
      // ...
    }
  }
```

[⬆ Back to top](#목차)
<br />

## 개방 폐쇄의 원칙(Open-Closed Principle, OCP)

Bertrand Meyer가 언급한 대로, “소프트웨어 엔티티(클래스, 모듈, 함수 등)는 확장을 위해 개방되어야 하지만 수정을 위해 폐쇄되어야 한다.” 이게 무슨 뜻일까? 이 원칙은 기본적으로 **사용자가 기존 코드를 변경하지 않고, 새 기능을 추가할 수 있도록 허용해야 한다는 원칙**이다.

새 기능을 수정하거나 기존 기능을 변경할 때, 건드릴 코드가 최소인 시스템 구조가 바람직하다. 이상적인 시스템이라면 새 기능을 추가할 때, 시스템을 확장할 뿐 기존 코드를 변경하지 않는다.

### Bad:

```tsx
class AjaxAdapter extends Adapter {
  constructor() {
    super();
  }
  // ...
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
  }
  // ...
}

class HttpRequester {
  constructor(private readonly adapter: Adapter) {}

  async fetch<T>(url: string): Promise<T> {
    if (this.adapter instanceof AjaxAdapter) {
      const response = await makeAjaxCall<T>(url);
      // response 변경 및 반환하는 코드
    } else if (this.adapter instanceof NodeAdapter) {
      const response = await makeHttpCall<T>(url);
      // response 변경 및 반환하는 코드
    }
  }
}

function makeAjaxCall<T>(url: string): Promise<T> {
  // 요청을 보내고 프로미스를 반환하는 코드
}

function makeHttpCall<T>(url: string): Promise<T> {
  // 요청을 보내고 프로미스를 반환하는 코드
}
```

### Good:

```tsx
abstract class Adapter {
  abstract request<T>(url: string): Promise<T>;
  // 하위 클래스에서 공유할 코드 작성
}

class AjaxAdapter extends Adapter {
  constructor() {
    super();
  }

  async request<T>(url: string): Promise<T> {
    // 요청을 보내고 프로미스를 반환하는 코드
  }

  // ...
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
  }

  request<T>(url: string): Promise<T> {
    // 요청을 보내고 프로미스를 반환하는 코드
  }

  // ...
}

class HttpRequester {
  constructor(private readonly adpater: Adapter) {}

  async fetch<T>(url: string): Promise<T> {
    const response = await this.adpater.request<T>(url);
    // response 변경 및 반환하는 코드
  }
}
```

[⬆ Back to top](#목차)
<br />

## 리스코프 치환의 원칙(Liskov Substitution Principle, LSP)

이 원칙은 매우 단순한 개념을 가리키지만 무서운 용어다. 공식적으로 “S가 T의 하위 유형이라면, T형의 객체는 해당 프로그램의 바람직한 특성(정확성, 작업 수행 등)을 변경하지 않고 S형의 객체로 대체될 수 있다.”라고 정의되어있다.

이는 “**상위 타입의 객체(클래스)를 하위 타입의 객체(클래스)로 치환해도 상위 타입을 사용하는 프로그램은 정상적으로 동작해야 한다**”는 의미다. 리스코프 치환 원칙을 지키는 것은 중요하다. 이유는 다음과 같다.

이 원칙을 지키지 않으면 개방 폐쇄의 원칙도 지키지 않게되므로 기능 확장을 위해 더 많은 부분을 수정해야 한다. 이것은 **확장을 어렵게 한다**. 따라서, 우리는 상속을 잘 정의하여 치환 가능성을 위배되지 않도록 설계해야 한다.

가장 대표적인 예로 ‘직사각형-정사각형'문제가 있다. 우리는 직사각형은 정사각형이 아니지만, 정사각형은 직사각형이라는 사실을 알고 있다. 그러나 ‘is-a’ 관계를 이용해 모형화하면 금세 문제가 생기는 것을 알 수 있다.

### Bad:

```tsx
class Rectangle {
  constructor(protected width: number = 0, protected height: number = 0) {}

  render(area: number) {
    console.log(area);
  }

  setWidth(width: number): this {
    this.width = width;
    return this;
  }

  setHeight(height: number): this {
    this.height = height;
    return this;
  }

  getArea(): number {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width: number): this {
    this.width = width;
    this.height = width;
    return this;
  }

  setHeight(height: number): this {
    this.width = height;
    this.height = height;
    return this;
  }
}

const renderLargeRectangles = (rectangles: Rectangle[]) => {
  rectangles.forEach((rectangle) => {
    const area = rectangle.setWidth(4).setHeight(5).getArea(); // Sqaure는 20이 아닌 25를 반환한다.
    rectangle.render(area);
  });
};

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

위 코드를 실행해보면 리스코프 원칙을 위반한다는 것을 알 수 있다. 이 원칙에 의하면 하위 클래스 정사각형도 같은 반환 값인 20을 반환해야 하는데, 25를 반환한다.

이를 어떻게 처리할 수 있을까? 도형이 정사각형인 경우 예외처리를 통해 코드를 수정할 수도 있다. 그러나 이것은 개방 폐쇄의 원칙에 어긋난다. `renderLargeRectangles`가 확장에 열려있지 않기 때문이다. 따라서, 정사각형은 직사각형 클래스를 상속받으면 안된다.

### Good:

```tsx
abstract class Shape {
  render(area: number) {
    console.log(area);
  }

  abstract getArea(): number;
}

class Rectangle extends Shape {
  constructor(private readonly width = 0, private readonly height = 0) {
    super();
  }

  getArea(): number {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(private readonly length: number) {
    super();
  }

  getArea(): number {
    return this.length * this.length;
  }
}

const renderLargeShapes = (shapes: Shape[]) => {
  shapes.forEach((shape) => {
    const area = shape.getArea();
    shape.render(area);
  });
};

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

[⬆ Back to top](#목차)
<br />

## 인터페이스 분리의 원칙(Interface Segregation Principle, ISP)

인터페이스 분리 원칙은 “**클라이언트가 사용하지 않는 인터페이스에 의존하도록 강요해서는 안된다**.”라고 명시되어 있다. 이 원칙은 단일 책임의 원칙과 매우 관련있는 원칙이다. 인터페이스는 그 인터페이스를 사용하는 클라이언트 기준으로 분리해야하며, **클라이언트 입장에서 사용하는 기능만 제공하도록 인터페이스를 분리해야 한다**는 의미다.

- 한 기능에 대한 변경의 여파를 최소화할 수 있게 된다.
- 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.
- 인터페스를 분리하게 되면 인터페이스가 명확해지고, 대체 가능성이 높아진다.

### Bad:

```tsx
interface SmartPrinter {
  print(): void;
  fax(): void;
  scan(): void;
}

class AllInOndePrinter implements SmartPrinter {
  print(): void {
    // ...
  }
  fax(): void {
    // ...
  }
  scan(): void {
    // ...
  }
}

class EconomicPrinter implements SmartPrinter {
  print(): void {
    // ...
  }
  fax(): void {
    throw new Error("팩스 지원 안됨.");
  }
  scan(): void {
    throw new Error("스캔 지원 안됨.");
  }
}
```

### Good:

```tsx
interface SmartPrinter {
  print(): void;
}

interface Fax {
  fax(): void;
}

interface Scanner {
  scan(): void;
}

class AllInOndePrinter implements SmartPrinter, Fax, Scanner {
  print(): void {
    // ...
  }
  fax(): void {
    // ...
  }
  scan(): void {
    // ...
  }
}

class EconomicPrinter implements SmartPrinter {
  print(): void {
    // ...
  }
}
```

[⬆ Back to top](#목차)
<br />

## 의존성 역전의 원칙(Dependency Inversion Principle, DIP)

이 원칙은 두 가지 기본 사항을 명시한다.

1. 상위 레벨의 모듈은 하위 레벨 모듈에 의존해서는 안된다. 둘 다 추상화에 의존해야 한다.
2. 추상화는 세부 사항에 의존해서는 안된다. 세부 사항은 추상화에 따라 달라져야 한다.

다시 말해 의존 관계를 맺을 때, 변하기가 어려운 것에 의존해야 한다. 즉, **구체화된 클래스에 직접적인 의존 관계를 맺지 않고, 인터페이스나 추상 클래스에 의존관계를 맺는 것**이다.

처음에 이해하기 어려울 수 있지만, DIP는 상위 레벨 모듈이 하위 레벨 모듈의 세부 정보를 알고 설정하는 것을 방지한다. **의존성 주입(DI)** 을 통해 이를 해결할 수 있는데, 모듈간의 결합을 줄일 수 있다는 것이 큰 장점이다. 강한 커플링(높은 결합도)은 코드를 리팩터링하기 어렵게 만들기 때문에 매우 나쁜 개발 패턴이다.

DIP는 일반적으로 **IoC(Inverstion of Control, 컨트롤 반전)** 컨테이너를 사용함으로 달성된다. 타입스크립트용 IoC 컨테이너의 예로는 [InversifyJs](https://www.npmjs.com/package/inversify)가 있다.

예를 들어 램프를 하나 구매했다고 가정하자. 우리가 흔히 이 램프를 사용하는 것은 램프에 달린 스위치를 이용해서 램프를 켜고 끄는 것이다. 그러나 시간이 지나 스마트폰으로도 램프를 켜고 끌 수 있는 램프가 출시되었다. 기존의 버튼만을 활용하는 램프는 새로 나온 램프의 기능을 추가할 수 없을 것이다. 만약 추가한다고 하더라도 Lamp의 일부분을 뜯어 고쳐서 개조해야 한다.

하지만 만약에 램프를 만들 때부터 “키고 끄는 기능"을 따로 모듈화 했다면? 현재는 버튼을 통해 구현했지만, 이후 더 좋은 기능이 나온다면 이는 리모콘, 스마트폰으로도 램프를 키고 끄는 기능을 동작시킬 수 있을 것이다.

### Bad:

```tsx
class Button {
  turnOn(): void {
    console.log("turn on by Button");
  }
  turnOff(): void {
    console.log("turn off by Button");
  }
}

class Lamp {
  private readonly _switch = new Button();

  turnOn(): void {
    this._switch.turnOn();
  }

  turnOff(): void {
    this._switch.turnOff();
  }
}

const lamp = new Lamp();
lamp.turnOn(); // turn on by Button
```

### Good:

```tsx
interface Switchable {
  turnOn(): void;
  turnOff(): void;
}

class Button implements Switchable {
  turnOn(): void {
    console.log(`turn on by ${this.constructor.name}`);
  }
  turnOff(): void {
    console.log(`turn off by Button ${this.constructor.name}`);
  }
}

class SmartPhone implements Switchable {
  turnOn(): void {
    console.log(`turn on by ${this.constructor.name}`);
  }
  turnOff(): void {
    console.log(`turn off by Button ${this.constructor.name}`);
  }
}

class Lamp {
  // 이 부분이 의존성 주입(Dependency Injection)이라고 할 수 있다.
  constructor(private readonly _switch: Switchable) {}

  turnOn(): void {
    this._switch.turnOn();
  }

  turnOff(): void {
    this._switch.turnOff();
  }
}
```

주석이 달린 부분을 보자. 위와 같은 방식으로 **외부에서 생성받은 객체(의존성)를 받아오는 것을 의존성 주입(Dependency Injection)** 이라고 한다. 이와 같이 구현을 하게 되는 경우 객체를 생성할 때, 어떤 의존성을 활용할 것인지 ‘직접' 지정하지 않고도 특정 서비스를 사용할 수 있게 된다.

[⬆ Back to top](#목차)
<br />
