# 9장 단위 테스트

테스트는 정말 중요하다. 만약 테스트를 하지 않거나 부적절한 테스트를 한다면, 코드를 작성하거나 변경할 때마다 버그가 발생하지 않을 것이라는 확신을 할 수 없을 것이다. 적절한 양의 테스트를 결정하는 것은 팀에 달려 있지만, **100% 커버리지(모든 문장과 지점)를 유지**하는 것을 매우 높은 신뢰도와 개발자들에게 마음의 안정을 준다. 즉, 훌륭한 **테스트 도구**뿐만 아니라 우수한 [적용 범위 도구](https://github.com/gotwarlost/istanbul)도 사용해야 한다.

테스트를 작성하지 않을 이유가 없다. 좋은 [JavaScript 테스트 프레임워크](http://jstherightway.org/#testing-tools)가 많이 있으므로, 팀에서 선호하는 프레임워크를 찾아서 사용하자. 팀에 적합한 것을 찾으면 **항상 도입하는 모든 새로운 기능/모듈에 대한 테스트를 작성하는 것을 목표**로 하자. 선호하는 방법이 TDD(Test Driven Development)라면 더 좋지만, 중요한 것은 **기능을 시작하거나 기존 기능을 리팩토링하기 전에 서비스 범위 목표에 도달했는지 확인하는 것**이다.

## 목차

1. [테스트란?](#테스트란)
2. [TDD의 세가지 원칙](#tdd의-세가지-원칙)
3. [깨끗한 테스트 코드 유지하기](#깨끗한-테스트-코드-유지하기)
4. [F.I.R.S.T 규칙](#first-규칙)
5. [테스트 당 개념 하나만 테스트하라](#테스트-당-개념-하나만-테스트하라)
6. [테스트의 이름으로 의도를 나타내라](#테스트의-이름으로-의도를-드러내라)
7. [테스트 코드의 가독성을 높여라](#테스트-코드의-가독성을-높여라)
8. [테스트 환경에서의 표준을 사용하라(이중 표준)](#테스트-환경에서의-표준을-사용하라-이중-표준)

## 테스트란?

**프로그램을 실행하여 오류와 결함을 검출하고 애플리케이션이 요구사항에 맞게 동작하는지 검증하는 절차**를 의미한다.

예를 들어 다음의 예시들은 모두 테스트이다.

- 프론트엔드 개발자가 디자인을 보고 마크업을 한 뒤, 브라우저에서 렌더링 결과를 확인하는 일련의 과정
- 새로운 기능을 추가한 뒤 제대로 동작하는지 브라우저에서 확인하는 것
- 기존 코드를 리팩토링한 후에 영향을 받는 다른 모듈이 기존과 동일하게 동작하는지 확인하는 것

위의 예시들로 알 수 있듯이 사실 개발자들은 테스트와 개발을 동시에 하고, 어쩌면 테스트를 하는데 더 많은 시간을 사용한다.

테스트의 궁극적인 목표는 애플리케이션의 **안정성과 유지 보수성 향상**이다. ‘사용자 관점'에서 최대한 사용자가 직접 사용하는 것처럼 시나리오를 작성하여 검증하는 것이 중요하다!

### 용어 정리

- **테스트(Test)**: 한 개 이상의 테스트 케이스의 집합
- **테스트 케이스(Test Case)**: **특정 목적 또는** **테스트 조건의 확인을 위해 개발된** **입력값, 실행 사전 조건, 예상 결과 및 실행 사후 조건 등을 포함한 내용의 집합**을 테스트 케이스라고 한다.
- **테스트 슈트(Test Suite)**: 테스트 케이스들을 하나로 묶은 것. 특정한 클래스나 메서드들을 선택하여 단위 테스트를 수행할 수 있다.
- **테스트 시나리오(Test Scenario)**: 테스트 실행을 위한 일련의 활동을 구체적으로 기술해둔 문서. 테스트 스크립트라고도 불린다.

**[⬆ back to top](#목차)**
<br />

## TDD의 세가지 원칙

TDD의 세가지 법칙을 지키고 30초 주기로 묶어서 개발을 진행한다.

1. 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
3. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

> _테스트 코드와 실제 코드가 함게 나올뿐더러 테스트 코드가 실제 코드보다 불과 몇초 전에 나온다._

하지만 실제 코드와 맞먹을 정도로 방대한 테스트 코드는 심각한 관리 문제를 유발할 수도 있다.

**[⬆ back to top](#목차)**
<br />

## 깨끗한 테스트 코드 유지하기

테스트 코드가 깨끗하지 않을 경우 아래와 같은 일어날 수 있다.

1. 새 버전을 출시할 때마다 팀이 테스트 케이스를 유지하고 보수하는 비용이 늘어남
2. 개발자 사이에서 테스트 코드가 가장 큰 불만으로 자리 잡는다.
3. 관리자가 예측값이 너무 큰 이유를 물어보면 팀은 테스트 코드를 비난한다.
4. 테스트 슈트를 폐기한다.
5. **테스트 코드의 부재로 개발자는 수정한 코드가 제대로 도는지 확인할 방법이 없다.**
6. 결함율이 높아진다.
7. 의도하지 않은 결함 수가 많아지면 개발자는 변경을 주저한다.
8. 변경하면 득보다 해가 크다 생각해 더 이상 코드를 정리하지 않는다.
9. 코드가 망가진다.
10. 테스트 슈트도 없고, 얼기설기 뒤섞인 코드에, 좌절한 고객과, 테스트에 쏟아 부은 노력이 허사였다는 실망감만 남는다.

> **\*테스트 코드는** **실제 코드 못지 않게 중요하다.** 테스트 코드는 사고와 설계의 주의를 기울여 **실제 코드 못지 않게 깨끗하게 짜야 한다**.\*

### 테스트는 유연성, 유지보수성, 재사용성을 제공한다.

- 테스트 코드를 깨끗하게 유지하지 않으면 결국 잃게 된다.
  : 테스트 코드가 지저분하면 코드를 변경하는 능력이 떨어지며, 코드 구조를 개선하는 능력도 떨어진다. 즉, 테스트 코드가 지저분할수록 실제 코드도 지저분해지며, 결국 테스트 코드를 잃어버리고 실제 코드도 망가진다.
- 테스트 케이스가 없으면 실제 코드를 유연하게 만드는 버팀목이 사라지게 된다.
  : 테스트 케이스가 없다면 모든 변경이 잠정적인 버그다.
- 테스트 커버리지가 높을 수록 변경에 대한 공포는 줄어든다.
- 실제 코드를 점검하는 자동화된 단위 테스트 슈트는 설계와 아키텍처를 최대한 깨끗하게 보존하는 열쇠다.

> **\*즉**, **테스트 케이스는 개발에 유연성, 유지보수성, 재사용성을 제공한다. 테스트 케이스가 있으면 변경이 쉬워지기 때문이다.\***

**[⬆ back to top](#목차)**
<br />

## F.I.R.S.T 규칙

깨끗한 테스트는 다음의 다섯가지 규칙을 따른다:

- **Fast**(**빠르게**): **테스트는 빨라야 한다**. 테스트를 자주 돌려서 문제를 찾아낼 수 있어야 하는데, 테스트가 느리면 자주 돌리기 힘들기 때문이다.
- **Independent**(**독립적인**): **각 테스트는 서로 의존하면 안된다**. 독립적으로 실행하든 어떤 순서로 실행하든 동일한 출력을 제공해야 한다. 테스트가 서로 의존하면 테스트 실패 원인을 진단하기 어려워진다.
- **Repeatable**(**반복 가능한**): **테스트는 어떤 환경에서도 반복 가능해야 하며 실패하는 이유가 없어야 한다.** 실제 환경, QA 환경, 네트워크에 연결되지 않은 노트북 환경에서도 실행할 수 있어야 한다.
- **Self-Validating**(**자가 검증하는**): **테스트는 Passed 또는 Failed(bool값)로 응답해야 한다.** 테스트 통과 여부를 알기 위해, 로그 파일을 비교하게 만들어서는 안된다. 테스트가 스스로 성공과 실패를 가늠하지 않는다면 판단은 주관적이 되며, 지루한 수작업 평가가 필요하게 된다.
- **Timely**(**적시에**): 테스트는 적절한 시기에 작성해야 한다. **단위 테스트는 테스트하려는 실제 코드를 구현하기 직전에 구현**한다. 실제 코드를 구현한 이후에 테스트르 작성하면 테스트 작성이 어렵다는 사실을 발견할하거나, 테스트가 불가능하도록 실제 코드를 설계할 수도 있기 때문이다.

**[⬆ back to top](#목차)**
<br />

## 테스트 당 개념 하나만 테스트하라

테스트도 단일 책임의 원칙을 따라야 한다. 단위 테스트 당 하나의 `assert`문만 사용하는 것이 좋다. 하지만 이는 하나의 테스트 함수 안에 작성할 수 있는 여러 개의 `assert`문을 분리했다는 뜻이고, 중복되는 코드가 발생할 가능성이 높다.

따라서 ‘단일 `assert`문'이 훌륭한 지침이지만 중복 코드를 발생시킬 수 있기에 `**assert`문을 최대한 줄이는 것을 지향\*\*하는 것이 좋다.

### Bad:

```tsx
import dayjs, { Dayjs } from "dayjs";

describe("DayJS", () => {
  it("handles date boundaries", () => {
    let date: Dayjs;

    date = dayjs("1/1/2022");
    expect(date.add(30, "day").format("M/D/YYYY")).toBe("1/31/2022");

    date = dayjs("2/1/2016");
    expect(date.add(28, "day").format("M/D/YYYY")).toBe("2/29/2016");

    date = dayjs("2/1/2015");
    expect(date.add(28, "day").format("M/D/YYYY")).toBe("3/1/2015");
  });
});
```

### Good:

```tsx
import dayjs, { Dayjs } from "dayjs";

describe("DayJS", () => {
  it("handles 30-day months", () => {
    const date = dayjs("1/1/2022");
    expect(date.add(30, "day").format("M/D/YYYY")).toBe("1/31/2022");
  });

  it("handles leap year", () => {
    const date = dayjs("2/1/2020");
    expect(date.add(28, "day").format("M/D/YYYY")).toBe("2/29/2020");
  });

  it("handles non-leap year", () => {
    const date = dayjs("2/1/2015");
    expect(date.add(28, "day").format("M/D/YYYY")).toBe("3/1/2015");
  });
});
```

**[⬆ back to top](#목차)**
<br />

## 테스트의 이름으로 의도를 드러내라

테스트가 실패할 때, 그 이름은 무엇이 잘못되었을 수 있는지 나타내는 첫번째 지표이다.

### Bad:

```tsx
describe("Calender", () => {
  it("2/29/2020", () => {
    // ...
  });

  it("throws", () => {
    // ...
  });
});
```

### Good:

```tsx
describe("Calender", () => {
  it("should handle leap year", () => {
    // ...
  });

  it("should thorw when format is invalid", () => {
    // ...
  });
});
```

**[⬆ back to top](#목차)**
<br />

## 테스트 코드의 가독성을 높여라

가독성은 실제 코드보다 테스트 코드에 더더욱 중요하다. 저자는 깨끗한 테스트 코드를 만들기 위한 세가지 규칙으로 모두 가독성을 뽑았다. 가독성을 높이기 위해서 **명료성**, **단순성**, **풍부한 표현력**이 필요하다. 또, **테스트 코드는 최소의 표현으로 많은 것을 나타내야 한다**.

### BUILD-OPERATE-CHECK 패턴

테스트 케이스에 테스트와 무관한 코드가 많다면(불필요한 사항이 많다면) 이 패턴을 이용해서 해결할 수 있다. 이 패턴은 테스트 구조를 세 부분으로 나눈다.

1. **BUILD**: 테스트 자료 만들기
2. **OPERATE**: 테스트 자료를 조작(전처리)
3. **CHECK**: 조작한 결과가 올바른지 확인

잡다하고 세세한 코드를 거의 없애는 것에 주목하자. **테스트 코드는 진짜 필요한 자료 유형과 함수만 사용해야 한다.** 코드를 읽는 사람이 잡다하고 세세한 코드에 주눅들고 헷갈릴 필요없이 코드가 수행하는 기능을 재빨리 이해할 수 있게 해야 한다.

<details><summary><b>car 클래스 구현 보기</b></summary><div markdwon="1">

```tsx
interface Car {
  doorState: boolean;
  battery: number;
  engine: boolean;
  display: boolean;
  break: boolean;
  engineOilPump: boolean;
  changeDoorStatus(status: string): void;
  checkBattery(): number;
  pushBreak(): void;
  turnOnEngine(): void;
  isEngineOn(): boolean;
  isEngineOilPumpOn(): boolean;
  isDisplayOn(): boolean;
}

class GasoineCar implements Car {
  doorState: boolean;
  battery: number;
  engine: boolean;
  display: boolean;
  break: boolean;
  engineOilPump: boolean;

  constructor() {
    this.doorState = true;
    this.battery = 100;
    this.engine = false;
    this.display = false;
    this.break = false;
    this.engineOilPump = false;
  }

  changeDoorStatus(status: string): void {
    if (status === "open") this.doorState = false;
    if (status === "close") this.doorState = true;
  }

  checkBattery(): number {
    return this.battery;
  }

  pushBreak(): void {
    this.break = !this.break;
  }

  turnOnEngine(): void {
    if (this.break && this.checkBattery() > 50 && !this.doorState) {
      this.engine = true;
      this.engineOilPump = true;
      this.display = true;
    }
  }

  isEngineOn() {
    return this.engine;
  }

  isEngineOilPumpOn() {
    return this.engineOilPump;
  }

  isDisplayOn() {
    return this.display;
  }
}
```

</div></details>

### Bad:

```tsx
describe("BuildOperateCheckPatternTest", () => {
  it("drivable test before refactoring", () => {
    let car: Car;
    let engineType: string = "gasoline";

    if (engineType === "gasoline") car = new GasoineCar();
    if (engineType === "diesel") car = new DiesselCar();
    if (engineType === "electirc") car = new ElectricCar();

    car!.changeDoorStatus("open");
    car!.checkBattery();
    car!.pushBreak();
    car!.turnOnEngine();

    expect(car!.isEngineOn).toBeTruthy();
    expect(car!.isEngineOilPumpOn).toBeTruthy();
    expect(car!.isDisplayOn).toBeTruthy();
  });
});
```

### Good:

```tsx
describe("BuildOperateCheckPatternTest", () => {
  it("drivable test after refactoring", () => {
    // BUILD
    let car: Car = buildCar("gasoline");
    // OPEREATE
    operateCarDrivable(car);
    // CHECK
    assertCarIsDrivable(car);
  });

  const buildCar = jest.fn((engineType: string): Car => {
    let car: Car | null = null;

    if (engineType === "gasoline") car = new GasoineCar();
    if (engineType === "diesel") car = new DiesselCar();
    if (engineType === "electirc") car = new ElectricCar();

    return car as Car;
  });

  const operateCarDrivable = jest.fn((car: Car): void => {
    car.changeDoorStatus("open");
    car.checkBattery();
    car.pushBreak();
    car.turnOnEngine();
  });

  const assertCarIsDrivable = jest.fn((car: Car): void => {
    expect(car.isEngineOn()).toBeTruthy();
    expect(car.isEngineOilPumpOn()).toBeTruthy();
    expect(car.isDisplayOn()).toBeTruthy();
  });
});
```

**[⬆ back to top](#목차)**
<br />

## 테스트 환경에서의 표준을 사용하라 (이중 표준)

테스트 API 코드에 적용하는 표준은 실제 표준과 엄연히 다르다. **단순하고, 간결하고, 표현력이 풍부해야 하지만, 실제 코드만큼 효율적인 필요는 없다**. 실제 환경이 아닌 테스트 환경에서 돌아가기 때문이다.

실제 환경과 달리 테스트 환경은 자원이 제한적일 가능성이 낮다. 이것이 **이중 표준**의 본질이다. 실제 환경에서는 절대 안 되지만 테스트 환경에서 전혀 문제없는 방식이 있다. 대개 메모리나 CPU 효율과 관련 있는 경우다. 코드의 깨끗함과는 철저히 무관하다.

하지만 **엄격한 것과 효율적인 것의 기준은 사람마다 다른 것으로 생각하기 때문에 팀에서 적절히 합의해서 사용**하자!!

### Bad:

```tsx
describe("turn on low temperature alarm at threshold", () => {
  hw.setTemp(WAY_TOO_COLD);
  controller.tic();
  expect(hw.heaterState()).toBeTruthy();
  expect(hw.blowerState()).toBeTruthy();
  expect(hw.coolerState()).toBeFalsy();
  expect(hw.hiTempAlarm()).toBeFalsy();
  expect(hw.lowTempAlarm()).toBeTruthy();
});
```

### Good:

이 방식은 클린 코드의 그릇된 정보를 피하라(자신만 아는 약어 사용 등) 규칙을 위반한다. 하지만 테스트 코드에서 이렇게 작성하게 된다면, 가독성이 크게 높아진다. 따라서 Mock 객체를 만들어 getState() 를 정의하고 위와 같은 형태로 비교하는 테스트로 리펙토링 할 수 있다.

```tsx
describe("controller test", () => {
  it("turn on low temperature alarm at threshold", () => {
    wayTooCold();
    // "HBchL" -> heater, blower, cooler, hi-temp-alarm, low-temp-alarm 순, 대문자면 켜진 것
    expect(hw.getState()).toBe("HBchL");
  });
});
```

**[⬆ back to top](#목차)**
<br />
