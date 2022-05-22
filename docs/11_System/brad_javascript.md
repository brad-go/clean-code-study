# 11장 시스템

> _“복잡성은 죽음이다. 개발자에게서 생기를 앗아가며, 제품을 계획하고 제작하고 테스트하기 어렵게 만든다.”_ > _- Ray Ozzie, Microsoft CTO_

모든 **소프트웨어 개발의 핵심은 복잡성을 극복하는 것**이다. 프로그램은 작은 프로그램의 조합으로 만들어지는데, 다른 엔지니어링과 달리 이런 조합에 물리적인 제약이 존재하지 않기 때문에 훨씬 쉽게 복잡해지는 경향이 있다. 예를 들어, 초고층 건물이나 우주 비행선은 물리 법칙의 제약 때문에 더 복잡해지기 어렵지만, 소프트웨어는 이러한 제약이 없다.

**깨끗한 코드는 낮은 추상화 수준에서 관심사를 분리하기 쉽게 해준다. 그렇기에 높은 추상화 수준, 시스템 수준에서 깨끗함을 유지하는 것이 중요**하다.

## 목차

1. [시스템 제작과 시스템 사용을 분리하라](#시스템-제작과-시스템-사용을-분리하라)
   1-1. [선언부와 실행부 분리](#1-선언부와-실행부-분리하기)
   1-2. [팩토리 패턴 사용](#2-팩토리-패턴-사용하기)
   1-3. [의존성 주입](#3-의존성-주입-di-dependency-injection)
2. [확장](#확장)
3. [AOP](#aop-aspect-oriented-programming)
4. [테스트 주도 시스템 아키텍처 구축](#테스트-주도-시스템-아키테처-구축)
5. [의사 결정을 최적화하라](#의사-결정을-최적화하라)
6. [명백한 가치가 있을 때 표준을 현명하게 사용하라](#명백한-가치가-있을-때-표준을-현명하게-사용하라)
7. [시스템은 도메인 특화 언어가 필요하다](#시스템은-도메인-특화-언어가-필요하다)

## 시스템 제작과 시스템 사용을 분리하라

> _소프트웨어 시스템은 준비과정(어플리케이션 객체를 제작하고 의존성을 서로 연결하는)과 런타임 로직(준비 과정 이후에 이어지는)을 분리해야 한다._

시작 단계라는 **관심사(concern)** 는 지나치기 쉽지만, 모든 어플리케이션은 시작 단계의 **관심사 분리**가 필요하다.

그렇다면 시작 단계를 분리하지 않은 **초기화 지연(Lazy Initializaition)** 혹은 **계산 지연 기법**에 관한 코드를 보자.

```tsx
getService() {
  if (this.service === undefined) this.service = new MyServiceImpl(...);
  return this.service;
}
```

위 코드가 초기화 지연 기법의 일반적인 형태이다. 해당 기법은 불필요한 초기화 비용의 최적화, `null` 또는 `undefined` 방지 등의 이점을 가지는 코드이다. 하지만 이 코드는 위 초기화 코드에 매우 의존적이며, 테스트도 어렵다.

이러한 생성 및 사용의 분산은 모듈성을 저해하고 코드의 중복을 가져오므로 **잘 정돈된 견고한 시스템을 만들기 위해서는 전역적이고 일관된 의존성 해결 방법을 통해 위와 같은 작은 편의 코드들이 모듈성의 저해를 가져오는 것을 막아야 한다.**

### 1. 선언부와 실행부 분리하기

```tsx
// index.js

import App from "./App.js";

const $target = document.querySelector(".App");
new App($target);
```

```tsx
// App.js

export default function App() {
  // ...
}
```

자바스크립트에는 자바, C등의 컴파일러 기반 언어처럼 우선적으로 실행하는 main 함수를 가지고 있지 않다. 그래서 자바스크립트는 일반적으로 `index.js`를 진입 경로로 설정해 가장 먼저 실행되게 만들어준다.

왜 굳이 두개를 나눠서 사용할까? App.js에 모두 작성해도 될 것 같은데? 라고 생각할 수 있다. 그러나 이렇게 하는 이유가 있다.

**컴포넌트의 선언부와 실행부를 분리**하기 위해서다. 분리의 목적은 App컴포넌트를 여러개 생성할 수 있다는 것과 유지보수 측면에서 둘을 분리하는게 **코드의 흐름을 따라가는데 유리**하기 때문이다. 코드가 단순한 경우 큰 문제가 안되지만, 복잡하고 고도화된 코드에서 선언부와 실행부가 뒤섞여 있으면 복잡도가 증가하게 되고, 이는 유지보수를 어렵게 만든다.

[⬆ Back to top](#목차)

### 2. 팩토리 패턴 사용하기

**객체가 생성되는 시점**을 애플리케이션이 결정할 필요가 생긴다면 **추상 팩토리(Abstract Factory) 패턴**을 사용한다. 객체 생성에 대해서 내부 로직을 추상화하고, 그것에 대해서 어플리케이션은 호출만 하기 때문에 실제 구현을 알지 못하고 생성 시점만 결정하게 할 수 있다.

<details><summary><b>추상 팩토리 패턴 예시 코드</b></summary><div markdown="1">

```tsx
// 서로 다른 abstract products를 반환하는 메서드 집합 선언
export interface AbstarctFactory {
  createProductA(param?: any): AbstractProductA;
  createProductB(): AbstractProductB;
}

// 구체적인 팩토리. 추상 팩토리를 구현하고 인스턴스화할 수 있는 클래스
// 구체적인 팩토리 메서드는 추상 프로덕트를 반환한다고 명시되어있지만, 실질적으로 구체화된 프로덕트를 반환한다.
export class ConcreteFactory1 implements AbstarctFactory {
  createProductA(param?: any): AbstractProductA {
    return new ProductA1();
  }

  createProductB(): AbstractProductB {
    return new ProductB1();
  }
}

// 각 구체화된 팩토리는 해당 product의 변형을 가진다.
export class ConcreteFactory2 implements AbstarctFactory {
  createProductA(param?: any): AbstractProductA {
    return new ProductA2();
  }

  createProductB(): AbstractProductB {
    return new ProductB2();
  }
}

export interface AbstractProductA {
  methodA(): string;
}

export class ProductA1 implements AbstractProductA {
  methodA = (): string => {
    return "This is methodA of ProductA1";
  };
}

export class ProductA2 implements AbstractProductA {
  methodA = (): string => {
    return "This is methodA of ProductA2";
  };
}

export interface AbstractProductB {
  methodB(): number;
}

export class ProductB1 implements AbstractProductB {
  methodB = (): number => {
    return 1;
  };
}

export class ProductB2 implements AbstractProductB {
  methodB = (): number => {
    return 2;
  };
}

// 클라이언트 코드는 추상적 유형을 통해서만 팩토리 및 프로덕트와 함께 작동한다.
// 이렇게 하면 팩토리 또는 프로덕트 하위 클래스를 손상시키지 않고 클라이언트 코드에 전달할 수 있다.
// 클라이언트는 원하는 아무 팩토리 클래스를 이용해 작업할 수 있다.
const clientCode = (facotry: AbstarctFactory) => {
  const productA = facotry.createProductA();
  const productB = facotry.createProductB();

  console.log(productA.methodA());
  console.log(productB.methodB());
};

clientCode(new ConcreteFactory1());
clientCode(new ConcreteFactory2());
```

</div></details>
<br />

[⬆ Back to top](#목차)

### 3. 의존성 주입 (DI, Dependency Injection)

**제어 역전(Inversion of Control, IoC)**

의존성 주입은 **제어역전(IoC)** 기법을 의존성 관리에 적용한 방법이다. 제어 역전에서는 한 객체가 맡은 보조 책임을 새로운 객체에게 전적으로 떠넘긴다. 새로운 객체는 넘겨받은 책임만 맡으므로 단일 책임 원칙을 지키게 된다.

IoC는 프로그램의 제어 흐름, 즉 프로그램의 메인 스트림을 미리 작성되었던 코드(프레임워크)에 의해 제어되게 하고, 개발자가 작성한 코드(어플리케이션 코드)는 프레임워크에 의해 제어되게 한다. 프로그래머가 짠 코드가프레임워크에 의해 제어되는 제어권의 역전을 활용하여 프로그램 작성에 일관성을 부여하고 유지보수가 가능하게 하는 코드를 작성하는데 도움을 줄 수 있게 하는 것이다.

즉 제어역전은 **객체를 생성하는 역할을 가지는 객체를 정의**하여 프로그래머가 직접 생성자를 호출하여 생성하는 것이 아니라 그저 **의존관계만 설정하면 자동으로 객체를 주입하게 하여 어플리케이션 코드에서 객체 생성/생명주기 관리의 책임을 덜 수 있도록 도와준다**.

```tsx
// inversify.js를 통해 의존성 주입을 '부분적으로' 구현한 기능

const penalty = container.get<PenaltyManager>(SERVICE_IDENTIFIER.PENALTY);
```

자바스크립트에서는 IoC를 위해 inversify.js를 사용할 수 있다.

**진정한 의존성 주입**

진정한 의존성 주입은 클래스가 의존성을 해결하려 시도하지 않는다. 클래스는 수동적이며, 의존성을 주입하는 방법으로 `설정자(setter)`나 `생성자 인수`를 (혹은 둘 다를) 제공한다. DI 컨테이너는 필요한 객체의 인스턴스를 만든 후 생성자 인수나 설정자 메서드를 사용해 의존성을 설정한다.

```tsx
// 배터리 일체형
// 생성자에서만 의존성을 주입해주는 상황이라 배터리가 떨어지게 되면 다른 배터리로 교체하지 못하고 새로 사야함.
class ElectronicCar {
  private battery: Battery = new NormalBattery();
}

// 배터리 분리형 1
// setter 이용해서 외부에서 주입해주는 상황은 외부에서 배터리를 교체해줄 수 있기 때문에 일체형보다 유연함
class ElectronicRobot {
  private battery: Battery | undefined;

  setBattery(battery: Battery) {
    this.battery = battery;
  }
}

// 배터리 분리형 2
// DI 컨테이너는 필요한 객체의 인스턴스를 만든 후 생성자 인자나 설정자 메서드를 사용해 의존성 설정 가능
class ElectronicRadio {
  private battery: Battery | undefined;

  constructor(battery: Battery) {
    this.battery = battery;
  }

  setBattery(battery: Battery) {
    this.battery = battery;
  }
}
```

[⬆ Back to top](#목차)
<br />

## 확장

군락은 마을, 마을은 도시로 성장한다. 하지만 누가 마을의 성장을 고려해 미리 8차선 고속도로를 지으려 할까? 처음부터 시스템을 제대로 만든다는 것은 미신일 뿐이다. 대신 우리는 오늘 주어진 사용자 요구에 맞춰 시스템을 구현해야 한다. 내일은 내일의 요구에 맞춰 시스템을 조정하고 확장한다.

이것이 반복적이고 점진적인 애자일 방식의 핵심이다. **테스트 주도 개발(TDD), 리팩터링으로 얻어지는 깨끗한 코드는 코드 수준에서 시스템을 조정하고 확장하기 쉽게 만든다**.

소프퉤어 시스템은 물리적인 시스템(도시의 성장 등)과는 다르다. **관심사를 적절히 분리해 관리한다면 소프트웨어 아키텍처는 점진적으로 발전할 수 있다.**

[⬆ Back to top](#목차)
<br />

## AOP (Aspect oriented Programming)

관점 지향 프로그래밍(AOP)은 컴퓨터 패러다임의 일종으로 **대상 로직을 수정하지 않고, 기존 기능이나 객체에 코드를 주입할 수 있는 방법을 제공**한다. AOP는 새로운 행동을 추가하고 외부에서 기존 행동을 수정한다. AOP를 사용하면 다른 구성 요소에 대한 지식 없이 또는 코드의 추가 라이브러리 종속성 없이 구성 요소 간의 연결을 만들 수 있다. DI가 의존성을 주입하고 낮은 결합도를 위한 것이라면, AOP는 로직을 주입하고 높은 응집도를 위한 것이다.

삽입된 코드는 필수는 아니지만 로깅 기능 추가, 메타데이터 디버깅 또는 부가적인 기능을 하는 횡단 관심사를 가지지만 원본 코드에 영향을 주지 않고 추가 동작을 주입할 수 있다.

예를 들어, 비즈니스 로직을 작성했지만 이제 로깅을 위한 코드가 없다는 것을 깨달았다고 가정해보자. 이에 대한 일반접근 방식은 로깅 정보를 추가하는 기능을 통해 새로운 모듈 내에서 로깅 로직을 집중시키는 것이다.

그러나 동일한 로거를 잡고 로그하려는 모든 메서드에 코드 한 줄로 실행하는 동안 특정 지점에서 로거를 주입할 수 있다면 이는 분명 많은 가치를 제공하는 것이다. AOP가 그러한 역할을 도와주며, 이는 **부가적인 공통 코드들을 효율적으로 관리하게 해주는 방식**이란 것을 알 수 있다.

> **횡단 관심사항의 기능을 모듈화하여 중복을 최소화하면서, 핵심 관심사항에 집중하도록 하는 프로그래밍 기법**

### 관심 분리(Seperation of Concerns

![https://gmoon92.github.io/md/img/aop/concerns-relation.png](https://gmoon92.github.io/md/img/aop/concerns-relation.png)

AOP의 메커니즘은 프로그램을 관심사(Concerns) 기준으로 크게 핵심 관심사와 횡단 관심사로 분류한다.

- **핵심 관심사(Core Concerns)**: 프로그램의 핵심 가치와 목적이 그대로 드러난 관심 영역을 뜻한다.
  - 프로그램의 비즈니스 로직 등
- **횡단 관심사(Cross-cutting Concerns)**: 비즈니스 기능과는 다른 관심 영역을 뜻한다.
  - **보안, 자료수집, 로깅, 트랜잭션** 등 요구사항에 따라 다수의 비즈니스 포함되는 부가 기능들
  - 각 모듈들이 가지는 부가적인 공통된 관심사 정도로 생각할 수 있다.
  - 비즈니스 기능과는 별개의 영역이지만 필연적으로 대다수의 비즈니스 기능에 분포되어 있다. 이러한 관계는 비즈니스 로직의 가독성을 떨어뜨리고 중복된 코드가 생겨날 가능성이 크다.

### AOP의 3가지 개념

AOP의 3가지 개념에 대해 알아보자.

- **Aspect**(What): 대상 코드에 주입하려는 ‘aspect(측면)' 또는 **동작**. 자바스크립트에서는 **추가하려는 동작을 캡슐화한 함수**를 의미한다. (횡단 관심사의 모듈화)
- **Advice**(When): aspect 코드가 **실행되기를 원하는 순간**을 지정한다. ‘before’, ‘after’, ‘around’, ‘whenThrwoing’ 등이 있으며 이들은 코드의 실행과 관련된 시간을 나타낸다.
- **Pointcut**(Where): 대상 코드에서 aspect를 주입하려는 **위치**를 참조한다. 이론적으로 코드가 실행되기를 원할 때, 대상 코드의 아무 곳이나 가능하다.

### AOP로 횡단 관심사 해결하기

```tsx
// index.ts

class MyBusinessLogic {
  add(a: number, b: number) {
    console.log("Calling add");
    return a + b;
  }

  concat(a: string, b: string) {
    console.log("Calling concat");
    return a + b;
  }

  power(a: number, b: number) {
    console.log("Calling power");
    return a ** b;
  }
}
```

위와 같은 로직이 있다고 생각해보자. 우리는 위의 모든 메서드에게 두 가지 aspect를 주입하고 싶다. 하나는 받은 속성을 기록하고, 다른 하나는 반환값을 분석하고 유형을 기록하는 것이다.

이 비즈니스 로직들의 횡단 관심사인 로깅 로직을 어떻게 모듈화하고 분리해낼 수 있을까? 코드를 통해서 알아보자.

```tsx
// aop.ts

interface Obj {
  [key: string]: any;
}

// 개체의 모든 메서드를 가져오는데 사용되는 함수
const getMethods = (obj: Obj) =>
  Object.getOwnPropertyNames(Object.getPrototypeOf(obj)).filter(
    (item) => typeof obj[item] === "function"
  );

// advice에 의해 지정된 순간에 aspect를 호출하는 커스텀 함수로 원래 메서드를 대체해준다.
const replaceMethod = (
  target: Obj,
  methodName: string,
  aspect: Obj,
  advice: string
) => {
  // 기존 메서드 저장
  const originalCode = target[methodName!];
  // 메서드 오버라이딩
  target[methodName!] = (...args: any[]) => {
    if (["before", "around"].includes(advice)) {
      console.log(`===== Calling ${methodName} =====`);
      // 이 메서드와 받은 인자를 그대로 전달
      aspect.apply(target, args);
    }
    const returnedValue = originalCode.apply(target, args);
    if (["after", "around"].includes(advice)) {
      aspect.apply(target, args);
    }
    if ("afterReturning" === advice)
      return aspect.apply(target, [returnedValue]);
    return returnedValue;
  };
};

class AOP {
  // 타겟 객체에 필요한 순간과 위치에 aspect를 주입한다.
  static inject = (
    target: Obj,
    aspect: Obj,
    advice: string,
    pointcut: string,
    method: string | null = null
  ) => {
    if (pointcut === "method") {
      if (method != null) replaceMethod(target, method, aspect, advice);
      else
        throw new Error(
          "Trying to add an aspect to a method, but no method specified"
        );
    }
    if (pointcut === "methods") {
      const methods = getMethods(target);
      methods.forEach((methodName) =>
        replaceMethod(target, methodName, aspect, advice)
      );
    }
  };
}

export default AOP;
```

aop를 위한 구현이 끝났다. 이제 위 함수가 마법처럼 우리의 횡단 관심사를 해결해줄 것이다.

```tsx
// index.ts
import AOP from "./aop";

class MyBusinessLogic {
  add(a: number, b: number) {
    return a + b;
  }

  concat(a: string, b: string) {
    return a + b;
  }

  power(a: number, b: number) {
    return a ** b;
  }
}

const o = new MyBusinessLogic();

const loggingAspect = (...args: any[]) => {
  console.log("Arguments received: " + args);
};

const printTypeOfReturnedValueAspect = (value: any) => {
  console.log("Returned type: " + typeof value);
};

AOP.inject(o, loggingAspect, "before", "methods");
AOP.inject(o, printTypeOfReturnedValueAspect, "afterReturning", "methods");

o.add(2, 2);
o.concat("hello", "goodbye");
o.power(2, 3);
```

출력 결과를 보면 다음과 같다.

```tsx
===== Calling add =====
Arguments received: 2,2
Returned type: number
===== Calling concat =====
Arguments received: hello,goodbye
Returned type: string
===== Calling power =====
Arguments received: 2,3
Returned type: number
```

[⬆ Back to top](#목차)
<br />

## 테스트 주도 시스템 아키테처 구축

AOP 관점에서 설계를 해서 관심사를 분리한다면 위력이 막강하다. 어플리케이션 도메인 논리를 **POJO**로 작성할 수 있다면, 즉 코드 수준에서 **아키텍처 관심사를 분리할 수 있다면, 진정한 테스트 주도 아키텍처 구축이 가능해진다**.

처음부터 큰 그림을 그리고 디자인, 설계, 개발, 테스트를 진행한다면 중간에 큰 문제가 발생했을때, 고치는 것에 심리적인 압박감을 느낀다. 그렇기 때문에 **단순하면서도 관심사가 잘 분리된 아키텍처를 가지고 지속적인 확장을 해나가도 괜찮다.**

- 관심사 별로 모듈화
- 최소한의 결합도와 높은 응집도

[⬆ Back to top](#목차)
<br />

## 의사 결정을 최적화하라

큰 시스템에서는 한 사람이 모든 결정을 내릴 수 없다.

모듈을 나누고 관심사를 분리하면 지엽적인 관리와 결정이 가능해진다. 결정에 대해 가장 적합한 사람이 책임을 담당하는게 좋지만, 해당 **결정은 최대한 미루는 것이 좋다**. 많은 정보를 얻고 최선의 선택을 할 수 있기 때문이다.

[⬆ Back to top](#목차)
<br />

## 명백한 가치가 있을 때 표준을 현명하게 사용하라

**표준에 과도하게 집착해 고객을 위한 가치 창출이라는 목표를 잃으면 안된다**. 표준을 사용하면 다음과 같은 이점이 있다. 그러나 표준을 만드는 시간이 너무 오래 걸려 업계가 기다리지 못하거나, 표준을 재정한 목적을 잊어버리기도 한다.

- 아이디어와 컴포넌트를 재사용하기 쉽다.
- 적절한 경험을 가지는 사람을 구하기 쉽다.
- 좋은 아이디를 캡슐화 하기 쉽다.
- 컴포넌트들의 연결을 쉽게 해준다.

> **확실한 이득을 가져올 경우 표준을 사용하라**

[⬆ Back to top](#목차)
<br />

## 시스템은 도메인 특화 언어가 필요하다

DSL은 간단한 스크립트 언어나 표준 언어로 구현한 API를 가리킨다. 개념을 언어 구문으로 만들 수 있는 특정 범주의 지식에 한정된 언어로 **어떤 목적이 있고 그 목적만 달성할 수 있는 언어**를 가리킨다.

- 좋은 DSL은 도메인 개념과 그 개념을 구현한 코드 사이에 존재하는 의사소통 간극을 줄여준다.
- DSL을 효과적으로 사용한다면 추상화 수준을 코드 관용구나 디자인 패턴 이상으로 끌어올릴 수 있다.
- 적절한 수준에서 코드 의도를 표현할 수 있게 해준다.

[⬆ Back to top](#목차)
<br />
