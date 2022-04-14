# 6장 객체와 자료구조

## 목차

- [Getters와 Setters를 사용하라](#getters와-setters를-사용하라)
- [객체가 private/protected members를 가지게 해라](#객체가-privateprotected-members를-가지게-해라)
- [불변성을 선호하라](#불변성을-선호하라)
- [자료 추상화](#자료-추상화)
- [자료/객체 비대칭](#자료객체-비대칭)
- [디미터 법칙](#디미터-법칙)
- [자료 전달 객체(DTO)](#자료-전달-객체)

## Getters와 Setters를 사용하라

객체의 데이터의 접근하기 위해 getters와 setters를 사용하는 것은 단순히 객체의 프로퍼티를 조회하는 것보다 낫다. “**왜?**”라고 물을 수 있다. 이유는 다음과 같다.

- 객체의 프로퍼티를 가져오는 것 이상으로 다른 조작을 하고 싶을 때, 코드에 존재하는 모든 접근자를 찾아보거나 변경할 필요가 없다.
- `set`(setting)을 수행할 때 유효성 검사를 쉽게 추가할 수 있다.
- 내부 표현을 캡슐화 할 수 있다.
- getting과 setting시에 에러처리와 기록 추가(add logging)가 더 쉽다.
- 서버로부터의 getting 조작을 통해, 객체의 프로퍼티를 lazy load 할 수 있다.

### Bad:

```tsx
type BankAccount = {
  balance: number;
};

const value: number = 100;
const account: BankAccount = {
  balance: 0,
};

if (value < 0) {
  throw new Error("Cannot set negative balance.");
}

account.balance = value;
```

<details><summary><b>javascript version</b></summary><div markdown="1">

### javascript version

```tsx
function makeBankAccount() {
  // ...

  return {
    balance: 0,
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

</div></details>

### Good:

```tsx
class BankAccount {
  private accountBalance: number = 0;

  get balance(): number {
    return this.accountBalance;
  }

  set balance(value: number) {
    if (value < 0) {
      throw new Error("Cannot set negative balance.");
    }

    this.accountBalance = value;
  }
}

const account = new BankAccount();
account.balance = 100;
```

`set` 접근자를 통해 검증 논리를 캡슐화 했다. 나중에 사양이 바뀌어 추가 검증 규칙이 필요하게 되면, 모든 의존적인 코드를 변경하지 않고 그대로 둔 채 `setter` 구현만 바꾸면 된다.

<details><summary><b>javascript version</b></summary><div markdown="1">

### javascript version

```tsx
function makeBankAccount() {
  // 이건 private
  let balance = 0;

  // getter, 아래의 반환된 객체를 통해 public으로 만듦(공개)
  function getBalance() {
    return balance;
  }

  // setter, 아래의 반환된 객체를 통해 public으로 만듦(공개)
  function setBalance(amount) {
    // ...balance를 업데이트 하기 전에 검증 로직을 추가할 수 있음
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance,
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

</div></details><br/>

**[⬆ back to top](#목차)**
<br/>

## 객체가 private/protected members를 가지게 해라

타입스크립트는 클래스 멤버들에게 `public`(default), `protected` 와 `private`(비공개) 접근자들을 제공한다.

<details><summary><b>public, private과 protected의 차이</b></summary><div markdown="1">

- **public**: 어디서든지 접근할 수 있으며, 외부 인터페이스를 구성한다.
- **private**: 클래스 내부에서만 접근할 수 있으며, 내부 인터페이스를 구성할 때 쓰인다.
- **protected**: private과 비슷하지만, 자손 클래스에서도 접근이 가능하다. 내부 인터페이스를 구성할 때 쓰인다.

#### public

```tsx
class CoffeMachine {
  waterAmount = 0;

  constructor(power) {
    this.power = power;
    console.log(`전력량이 ${power}인 커피머신을 만듭니다.`);
  }
}

// 커피 머신 생성
let coffeeMachine = new CoffeMachine(100);

// 물 추가
coffeeMachine.waterAmount = 200;
```

현재 프로퍼티 `waterAmount`와 `power`는 public이다. 손쉽게 `waterAmount`와 `power`를 읽고 원하는 값으로 변경하기 쉬운 상태이다.

#### protected

이제 `waterAmount`를 protected로 바꿔서 `waterAmount`를 통제해보자. 예시로 `waterAmount`를 0 미만의 값으로는 설정하지 못하도록 만들어보자.

**protected 프로퍼티 명 앞엔 밑줄(\_)이 붙는다**. 자바스크립트에서 강제한 사항은 아니지만, 밑줄은 프로그래머들 사이에서 외부 접근이 불가능한 프로퍼티나 메서드를 나타낼 때 쓴다.

```tsx
class CoffeMachine {
  _waterAmount = 0;

  set waterAmount(value) {
    if (value < 0) throw new Error("물의 양은 음수가 될 수 없습니다.");
    this._waterAmount = value;
  }

  get waterAmount() {
    return this._waterAmount;
  }

  constructor(power: number) {
    this.power = power;
  }
}

// 커피 머신 생성
let coffeeMachine = new CoffeMachine(100);

// 물 추가
coffeeMachine.waterAmount = -10; // Error: 물의 양은 음수가 될 수 없습니다.
```

**protected 필드는 상속된다.**

`class MegaMachine extends CoffeeMachine`으로 클래스를 상속받으면, 새로운 클래스의 메서드에서 `this._waterAmount`나 `this._power`를 사용해 프로퍼티에 접근할 수 있다. 이렇게 protected 필드는 private 필드와 달리, 자연스러운 상속이 가능하다.

> **getter와 setter 함수**
>
> 위에서는 get, set 문법을 사용해서 getter와 setter 함수를 만들었다.
>
> 하지만 대부분은 아래와 같이 `get.../set...` 형식의 함수가 선호된다.
>
> ```tsx
> class CoffeeMachine {
>   _waterAmount = 0;
>
>   *setWaterAmount(value)* {
>     if (value < 0) throw new Error("물의 양은 음수가 될 수 없습니다.");
>     this._waterAmount = value;
>   }
>
>   *getWaterAmount()* {
>     return this._waterAmount;
>   }
> }
>
> new CoffeeMachine().setWaterAmount(100);
> ```
>
> 다소 길어보이긴 하지만, 이렇게 함수를 선언하면 다수의 인자를 받을 수 있기 때문에 좀 더 유연하다(위 예시에선 인자가 하나뿐이긴 하지만).
>
> 반면 get, set 문법을 사용하면 코드가 짧아진다는 장점이 있다. 어떤걸 사용해야 한다는 규칙은 없으므로 원하는 방식을 선택해서 사용하자.

#### private

private 프로퍼티와 메서드는 `#`으로 시작한다. `#`이 붙으면 클래스 안에서만 접근할 수 있다. 물 용량 한도를 나타내는 private 프로퍼티 `#waterLimit`과 남아있는 물의 양을 확인해주는 private 메서드 `#checkWater`를 구현해보자.

```tsx
class CoffeMachine {
  #waterLimit = 200;

  #checkWater(value) {
    if (value < 0) throw new Error("물의 양은 음수가 될 수 없습니다.");
    if (value > this.#waterLimit) throw new Error("물이 용량을 초과합니다.");
  }
}

let coffeeMachine = new CoffeMachine();

// 클래스 외부에서 private에 접근할 수 없다.
coffeeMachine.#checkWater(); // Error
coffeeMachine.#waterLimit = 1000; // Error
```

`#`은 자바스크립트에서 지원하는 문법으로, private 필드를 의미한다. private 필드는 클래스 외부나 자손 클래스에서 접근할 수 없다.

private 필드는 public 필드와 상충하지 않는다. private 프로퍼티 `#waterAmount`와 public 프로퍼티 `waterAmount`를 동시에 가질 수 있다.

</div></details>

### Bad:

```tsx
class Circle {
  radius: number;

  constructor(radius: number) {
    this.radius = radius;
  }

  perimeter() {
    return 2 * Math.PI * this.radius;
  }

  surface() {
    return Math.PI * this.radius * this.radius;
  }
}
```

### Good:

```tsx
class Circle {
  constructor(private readonly radius: number) {}

  perimeter() {
    return 2 * Math.PI * this.radius;
  }

  surface() {
    return Math.PI * this.radius * this.radius;
  }
}
```

<details><summary><b>javascript version</b></summary><div markdown="1">

### javascript version

```tsx
class Circle {
  constructor(radius) {
    this.#radius = radius;
  }

  // setter를 설정하지 않고 getter만을 설정해서 읽기전용으로 만듦
  get radius() {
    return this.#radius;
  }

  perimeter() {
    return 2 * Math.PI * this.#radius;
  }

  surface() {
    return Math.PI * this.#radius * this.#radius;
  }
}
```

</div></details><br/>

**[⬆ back to top](#목차)**
<br/>

## 불변성을 선호하라

타입스크립트의 타입 시스템은 인터페이스나 클래스의 개별 프로퍼티를 읽기전용으로 표시할 수 있게 해준다. 이는 조금 더 함수형으로 프로그래밍 할 수 있도록 해준다. (예상치 못한 변화는 나쁘기 때문)

조금 더 나아가서 T 유형을 사용하고 매핑된 유형을 사용하여 모든 속성을 읽기 전용으로 표시하는 내장형 읽기 전용 유형도 있다. ([**mapped types**](https://www.typescriptlang.org/docs/handbook/advanced-types.html#mapped-types))

### Bad:

```tsx
interface Config {
  host: string;
  port: string;
  db: string;
}
```

### Good:

```tsx
interface Config {
  readonly host: string;
  readonly port: string;
  readonly db: string;
}
```

배열의 경우 읽기 전용 배열을 `ReadonlyArray<T>`를 이용해 생성할 수 있다. `push()`나 `fill()` 메서드의 사용을 허용하지 않지만, `concat()`이나 `slice()`와 같은 값을 변화시키지 않는 기능은 사용할 수 있다.

### Bad:

```tsx
const array: number[] = [1, 3, 5];
array = []; // error
array.push(100); // 배열이 업데이트 됨
```

### Good:

```tsx
const array: ReadonlyArray<number> = [1, 3, 5];
array = []; // error
array.push(100); // error
```

읽기 전용 인자를 선언할 때 **const assertion**(**상수타입 단언**)을 문자값에 사용하자.

### Bad:

```tsx
const config = {
  hello: "world",
};
config.hello = "world"; // 값이 변화됨

const array = [1, 3, 5];
array[0] = 10; // 값이 변화됨

function readonlyData(value: number) {
  return { value };
}

const result = readonlyData(100);
result.value = 200; // 값이 변화됨
```

### Good:

```tsx
const config = {
  hello: "world",
} as const;
config.hello = "world"; // error

const array = [1, 3, 5] as const;
array[0] = 10; // error

function readonlyData(value: number) {
  return { value } as const;
}

const result = readonlyData(100);
result.value = 200; // error
```

**[⬆ back to top](#목차)**
<br/>

변수를 비공개(private)로 정의하는 이유는 **남들이 변수에 의존하지 않게 만들고 싶어서**다. 그런데 왜 많은 프로그래머가 getter와 setter를 당연히 공개해 비공개 변수를 외부에 노출할까?

## 자료 추상화

- 단순히 변수(filed)사이에 함수(gettor혹은 settor 등)라는 계층을 넣는다고 구현이 감춰지지 않는다. 구현을 감추려면 **추상화**가 필요하다.
- 추상 인터페이스를 제공해 사용자가 구현을 모른채 자료의 핵심을 조작할 수 있어야 진정한 의미의 클래스다.
- **자료를 세세하게 공개하기보다는 추상적인 개념으로 표현하는 편이 좋다**.
- 인터페이스나 조회/설정 함수만으로는 추상화가 이뤄지지 않는다. **개발자는 객체가 포함하는 자료를 표현할 가장 좋은 방법을 심각하게 고민해야 한다.**

### Bad:

```tsx
interface Vehicle {
  getFuelTankCapacityInGallons(): number;
  getGallonsOfGasoline(): number;
}
```

### Good:

```tsx
interface Vehicle {
  getPercentFuelRemaining(): number;
}
```

**[⬆ back to top](#목차)**
<br/>

## 자료/객체 비대칭

객체와 자료구조는 다음과 같은 차이가 있다.

- **객체**: 데이터를 추상화 뒤로 숨기고, 데이터를 다루는 함수만 공개한다.
- **자료구조**: 데이터를 공개하고 별다른 함수를 제공하지 않는다.

객체와 자료구조가 서로 제공하는 것이 상반되는데 이게 어떤 트레이드 오프를 가질지 생각해보자.

### 절차지향적 구현

```tsx
interface Point {
  x: number;
  y: number;
}

class Square {
  topLeft: Point;
  side: number;

  constructor(topLeft: Point, side: number) {
    this.topLeft = topLeft;
    this.side = side;
  }
}

class Rectangle {
  topLeft: Point;
  height: number;
  width: number;

  constructor(topLeft: Point, height: number, width: number) {
    this.topLeft = topLeft;
    this.height = height;
    this.width = width;
  }
}

class Circle {
  center: Point;
  radius: number;

  constructor(center: Point, radius: number) {
    this.center = center;
    this.radius = radius;
  }
}

class Geometry {
  private readonly PI = 3.141592653589783;

  area(shape: object) {
    if (shape instanceof Square) {
      const s = shape;
      return s.side * s.side;
    }
    if (shape instanceof Rectangle) {
      const r = shape;
      return r.height * r.width;
    }
    if (shape instanceof Circle) {
      const c = shape;
      return this.PI * c.radius * c.radius;
    }
    throw new Error("모양에 맞는 객체가 아닙니다.");
  }
}
```

- **절차지향 코드의 이점**
  - **기존 자료 구조를 변경하지 않으면서 새 함수를 추가하기 쉽다.** (`Geometry` 클래스에 새로운 함수를 추가할 때 도형 클래스는 수정하지 않아도 된다.)
- **절차지향 코드의 단점**
  - **새로운 자료 구조를 추가하려면 기존의 모든 함수를 고쳐야 한다.** (새로운 도형을 추가한다면 `Geometry` 클래스의 속한 함수를 모두 고쳐야 한다.)

### 객체지향적 구현

각 도형들을 다형성을 이용해 구현

```tsx
interface Shape {
  area(): number;
}

interface Point {
  x: number;
  y: number;
}

class Square implements Shape {
  private topLeft: Point;
  private side: number;

  constructor(topLeft: Point, side: number) {
    this.topLeft = topLeft;
    this.side = side;
  }

  area(): number {
    return this.side * this.side;
  }
}

class Rectangle implements Shape {
  private topLeft: Point;
  private height: number;
  private width: number;

  constructor(topLeft: Point, height: number, width: number) {
    this.topLeft = topLeft;
    this.height = height;
    this.width = width;
  }

  area(): number {
    return this.height * this.width;
  }
}

class Circle implements Shape {
  private center: Point;
  private radius: number;
  private readonly PI = 3.141592653589783;

  constructor(center: Point, radius: number) {
    this.center = center;
    this.radius = radius;
  }

  area(): number {
    return this.PI * this.radius * this.radius;
  }
}
```

- **객체지향적인 코드의 이점**
  - **기존 코드나 함수를 수정하지 않고 새로운 클래스를 추가하기 쉽다.** (기존 코드 변화없이 새로운 도형을 만들 수 있다.)
- **객체지향적인 코드의 단점**
  - **새로운 함수를 추가하기 위해서 모든 클래스를 고쳐야 한다.** (각 도형들에 새로운 함수를 추가해야 한다.)

**[⬆ back to top](#목차)**
<br/>

## 디미터 법칙

> **모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다. - 실용주의 프로그래머(p.227)**

이는 잘 알려진 휴리스틱으로 소포트웨어 모듈 사이의 결합도를 줄여서 코드의 품질을 높이자는 취지의 가이드라인이다.

- **객체는 조회 함수(getter)로 내부 구조를 공개하면 안 된다.**
- 클래스 `C`의 메서드 `f`는 다음과 같은 객체의 메서드만 호출해야 한다.
  - **클래스 `C`** - 자기 클래스의 메서드
  - **`f`가 생성한 객체** - 자신 내부에서 생성한 객체의 메서드
  - `**f`의 인수로 넘어온 객체** - 인자로 받은 객체의 메서드 (**연관관계\*\*)
  - **`C` 인스턴스 변수에 저장된 객체** - 내부에 변수로 가지고 있는 객체의 메서드 (**합성관계**)

### 기차 충돌

```tsx
const outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```

흔히 위와 같이 메서드체이닝한 코드를 기차 충돌(train wreck)이라 부른다. 위 방법이 안 좋은 이유는 **알 필요가 없는 객체의 메서드까지 접근을 하기 때문**이다. 객체의 내부 구조가 숨겨지지 않고, 디미터 법칙을 위반하게 된다.

위의 경우 ctxt가 option 객체를 불러오고 option에서 directory를 가져오고 dir 정보에서 path를 가져오는 것으로 각가 주체가 달라진다. 그러므로 다음과 같이 나누는 편이 좋다.

```tsx
const opts = ctxt.getOptions();
const scratchDir = opts.getScratchDir();
const outputDir = scratchDir.getAbsolutePath();
```

### 메서드 체이닝

```tsx
class Car {
  make: string;
  model: string;
  color: string;

  constructor(make: string, model: string, color: string) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make: string) {
    this.make = make;
    return this;
  }

  setModel(model: string) {
    this.model = model;
    return this;
  }

  setColor(color: string) {
    this.color = color;
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

위와 같은 메서드 체이닝은 javascript에서 많이 사용하는 패턴이다. 이를 사용할 시 코드가 많이 깔끔해지기 때문에 권장된다. 기차 충돌과 다른 점은 메서드 체이닝은 **체인된 함수들이 전부 본인 자신을 리턴**한다는 점이다.

**[⬆ back to top](#목차)**
<br/>

## 자료 전달 객체

데이터베이스와 통신하거나 소켓에서 받은 메시지의 값을 가져오기 위해 자료구조만 표현하는 형태의 객체가 사용되는데, 이를 DTO(Data Transfer Object)라고 한다.

- 공개 변수만 있고 함수가 없는 클래스
- 데이터를 전달만하며 가공되지 않은 기존 정보를 애플리케이션 코드에서 사용할 객체로 변환하는 과정 중 가장 처음 사용하는 구조체

### 활성 레코드

데이터베이스의 테이블을 직접 변환하여, 기존 DTO와 유사해지면서 find(), save()등의 레코드 작업을 수행하는 형태

주의할 점은 활성 레코드에 비즈니스 규칙 메서드를 추가하면서 잡종 구조가 되버리는 경우가 있으므로 지양하자. 최대한 객체와 자료구조를 구분하여 다룰 것!

**[⬆ back to top](#목차)**
<br/>
