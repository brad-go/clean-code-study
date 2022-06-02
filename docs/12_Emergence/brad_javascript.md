# 13장 창발성

## 목차

1. [창발적 설계로 깔끔한 코드를 구현하자](#창발적-설계로-깔끔한-코드를-구현하자)
2. [모든 테스트를 실행하라](#모든-테스트를-실행하라)
3. [중복을 없애라 (리팩토링)](#중복을-없애라-리팩토링)
4. [의도를 표현하라](#의도를-표현하라)
5. [클래스와 메서드 수를 최소로 줄여라](#클래스와-메서드-수를-최소로-줄여라)
   <br />

**창발**이란 “떠오름" 이라는 사전적 의미를 가지고 있으며, **상위 수준의 특성 중에서 그것을 이루는 하위 수준의 특성들 속에 존재하지 않는 새로운 환원 불가능한 특성이 나타나는 현상**을 의미한다.

이렇게만 보면 이해하기 쉽지 않을 수 있다. 그렇다면 예시를 들어보겠다. 우리가 흔히 볼 수 있는 개미가 있다. 이 흔한 개미 한 마리는 그처럼 복잡하고 다양한 분업을 이루어내기는커녕 이해하지도 못한다. 그러나 이 개미들이 수만, 수십만 마리가 모이면서부터 점차 뭔가 체계가 잡히고 조직화가 시작된다. 개미마다 역할이 정해지고 업무가 세분화되는 것이다. 한 마리의 개미 수준에서는 관찰되지 않던 특성이, 개미 집단의 수준에서는 비로소 관찰되는 것이다.

즉, **한 부분에서는 존재하지 않던 고차원적 질서가 전체에서 발휘되는 것** 또는 **자기 자신을 조직화하여 무의식 중에 더 높은 차원의 질서를 만들어내는 것**이라 할 수 있다. 이는 **처음 가지고 있지 않은 능력이 주변 환경에 의해서 자발적으로 생기거나 숨겨진 잠재성이 드러나는 현상**으로 볼 수 있다.

## 창발적 설계로 깔끔한 코드를 구현하자

위에서 볼 수 있듯이 창발성은 주변 환경에 영향을 받는다고 볼 수 있다. 그렇다면 어떠한 규칙이나 환경을 통해 창발성을 얻을 수 있을까? 여기 우리가 더 나은 혹은 창발적인 코드를 작성하기 위한 주변 환경을 만들어줄 단순한 규칙 네 가지가 있다.

- **모든 테스트를 실행한다.**
- **중복을 없앤다.**
- **프로그래머의 의도를 표현한다.**
- **클래스와 메서드 수를 최소로 줄인다.**

[⬆ Back to top](#목차)
<br />

## 모든 테스트를 실행하라

의도한 대로 돌아가는 시스템을 설계했지만, 이를 검증할 간단한 방법이 없다면 가치를 제대로 인정받을 수 없다. 우리는 그렇기에 테스트를 철저히 거쳐 모든 테스트 케이스를 항상 통과하는 ‘**테스트 가능한 시스템**'을 만들어야 한다.

다행히도 테스트가 가능한 시스템을 만들다보면 크기가 작은 클래스가 나오게 되고, 테스트 케이스가 많을 수록 테스트가 쉽게 코드를 작성하게 된다. 따라서 철저한 테스트가 가능한 시스템을 만들면 더 나은 설계가 얻어진다.

결합도가 높은 시스템은 테스트하기 어려운데, 이를 해결하기 위해 테스트 케이스를 많이 작성해야한다. 테스트 케이스가 많아질 수록 `SRP`, `DIP`, `의존성 주입`, `인터페이스`, `추상화` 등과 같은 도구를 사용하게 되고 설계 품질은 더욱 높아진다.

이처럼 **“테스트 케이스를 만들고 계속 돌려라”라는 간단한 규칙을 따르면 시스템은 낮은 결합도와 높은 응집력을 갖는 객체 지향 방법론의 목표를 달성한 코드가 나오게 된다**. 즉, 테스트 케이스를 작성하면 설계 품질이 높아진다.

### Bad:

```tsx
interface AddressValidator {
  isValidate(userAddress: Address): boolean;
}

// 응집도 - 모듈 내부 요소들이 밀접한 연관관계를 지니고 있는지를 나타내는 것
class Address {
  constructor(public addressDetail: string, public zipCode: number) {}
}

// Order 클래스가 address를 추가하는 부분을 구현해야 할까?
class Order {
  constructor(
    private userAddress: Address,
    private addressValidator: AddressValidator
  ) {}

  setAddress(address: string, zipcode: number): void {
    this.userAddress.addressDetail = address;
    this.userAddress.zipCode = zipcode;
    if (!this.addressValidator.isValidate(this.userAddress)) {
      throw new Error("주소를 다시 확인해주세요");
    }
  }
}

// User 클래스가 address를 추가하는 부분을 구현해야 할까?
class User {
  constructor(
    private name: string,
    private address: Address,
    private addressValidator: AddressValidator
  ) {}

  setAddress(addressDetail: string, zipCode: number): void {
    this.address.addressDetail = addressDetail;
    this.address.zipCode = zipCode;
    if (!this.addressValidator.isValidate(this.address)) {
      throw new Error("주소를 다시 확인해주세요");
    }
  }
}

// DeliveryMan이 address의 내용을 알고 있다. 그러므로 결합도가 높은 코드
class DeliveryMan {
  constructor(private name: string, private address: Address) {}

  setAddress(addressDetail: string, zipCode: number) {
    this.address.addressDetail = addressDetail;
    this.address.zipCode = zipCode;
  }
}
```

### Good:

```tsx
interface AddressValidator {
  isValidate(userAddress: userAddress): boolean;
}

interface userAddress {
  addressDetail: string;
  zipCode: number;
}

// 애시 당초 이 과정은 주소 정보를 통해 검증과정을 거치는 것이므로 Address 클래스가 진행해야 할 일이었다.
class Address {
  constructor(
    private addressDetail: string,
    private zipCode: number,
    private addressValidator: AddressValidator
  ) {}

  setAddress(address: userAddress): void {
    if (!this.addressValidator.isValidate(address)) {
      throw new Error("주소를 다시 확인해주세요");
    }
    this.addressDetail = address.addressDetail;
    this.zipCode = address.zipCode;
  }
}

class Order {
  constructor(private userAddress: Address) {}

  setAddress(newAddress: userAddress): void {
    this.userAddress.setAddress(newAddress);
  }
}

class User {
  constructor(private name: string, private userAddress: Address) {}

  setAddress(newAddress: userAddress): void {
    this.userAddress.setAddress(newAddress);
  }
}

class DeliveryMan {
  constructor(private name: string, private address: Address) {}

  setAddress(newAddress: userAddress) {
    this.address.setAddress(newAddress);
  }
}
```

[⬆ Back to top](#목차)
<br />

## 중복을 없애라 (리팩토링)

테스트를 모두 작성했다면 이제 코드와 클래스를 정리해야 한다. 그러므로 **점진적인 리팩토링**을 해나갈 단계이다. 이미 **테스트 케이스가 있기 때문에, 코드를 새로 추가하고 정리하면서 시스템이 깨질까 걱정할 필요가 없다.**

리팩터링 단계에서 다음과 같은 소프트웨어 설계 품질을 높이는 다양한 기법을 적용할 수 있다.

- 응집도 높이기
- 결합도 낮추기
- 관심사 분리하기
- 시스템 관심사를 모듈로 나누기
- 함수와 클래스 크기를 줄이기
- 더 나은 이름을 선택하기

하지만 우수한 설계에서 가장 커다란 적 중 하나는 중복이다. **중복은 추가 작업, 추가 위험, 불필요한 복잡도를 의미**하기 때문이다. 똑같은 코드는 당연히 중복이며, 비슷한 코드는 더 비슷하게 고쳐주면 리팩토링이 쉬워진다. 구현 중복도 중복의 한 형태이다.

**함수 내의 중복되는 로직은 새 함수로 만들어준다**. 단 몇 줄이라도 중복을 제거하겠다는 의지가 있어야 깔끔한 시스템을 만들 수 있다. 이렇게 하면 다른 팀원이 새 함수를 좀 더 추상화해서 다른 맥락에서 재사용할 기회를 포착할 수도 있다. 이러한 **소규모 재사용은 시스템 복잡도를 극적으로 줄여준다.**

### Bad:

```tsx
const deleteStudent = (id: number) => {
  const student = studentRepository.findById(id);

  if (!student) throw new Error("학생이 존재하지 않습니다.");

  // 학생 삭제 로직
};

const updateStudent = (id: number) => {
  const student = studentRepository.findById(id);

  if (!student) throw new Error("학생이 존재하지 않습니다.");

  // 학생 업데이트 로직
};
```

### Good:

```tsx
const deleteStudent = (id: number) => {
  getStudent(id);

  // 학생 삭제 로직
};

const updateStudent = (id: number) => {
  getStudent(id);

  // 학생 업데이트 로직
};

const getStudent = (id: number): Student => {
  const student: Student = studentRepository.findById(id);

  if (!student) throw new Error("학생이 존재하지 않습니다.");
};
```

고차원 중복을 제거할 목적으로 자주 사용하는 기법인 **`TEMPLATE METHOD 패턴`**을 사용할 수도 있다. 이 템플릿 메서드 패턴은 **어떤 작업을 처리하는 일부분을 서브 클래스로 캡슐화해 전체 일을 수행하는 구조는 바꾸지 않으면서 특정 단계에서 수행하는 내역을 바꾸는 패턴**이다. 즉, 거의 동일한 두 클래스에서 중복되는 부분을 합치고, 차이가 있는 부분만 별도의 하위 클래스에서 작성하는 방법이다.

### Bad:

```tsx
class VacationPolicy {
  accrueUSDivisionVacation(): void {
    // 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
    // ...
    // 휴가 일수가 미국 최소 법정 일수를 만족하는지 확인하는 코드
    // ...
    // 휴가 일수를 급여 대장에 적용하는 코드
    // ...
  }

  accrueEUDivisionVacation(): void {
    // 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
    // ...
    // 휴가 일수가 유럽연합 최소 법정 일수를 만족하는지 확인하는 코드
    // ...
    // 휴가 일수를 급여 대장에 적용하는 코드
    // ...
  }
}
```

### Good:

```tsx
abstract class VactionPolicy {
  accrueVaction(): void {
    this.calculateBaseVactionHours();
    this.alterForLegalMinimums();
    this.applyToPayroll();
  }

  private calculateBaseVactionHours(): void {
    /* ... */
  }
  protected abstract alterForLegalMinimums(): void;
  private applyToPayroll(): void {
    /* ... */
  }
}

class USVacationPolicy extends VactionPolicy {
  protected alterForLegalMinimums(): void {
    // 미국 최소 법정 일수를 사용한다.
  }
}

class EUVacationPolicy extends VactionPolicy {
  protected alterForLegalMinimums(): void {
    // EU 최소 법정 일수를 사용한다.
  }
}
```

[⬆ Back to top](#목차)
<br />

## 의도를 표현하라

자신이 이해하는 코드를 짜기는 쉽다. 코드를 짜는 동안에는 문제에 푹 빠져 코드를 구석구석 이해하기 때문이다. 하지만 나중에 코드를 유지보수할 사람이 코드를 짜는 사람만큼이나 문제를 깊이 이해할 가능성은 희박하다.

그러므로 **코드는 개발자의 의도를 분명히 표현해야 한다**. **개발자가 코드를 명백하게 짤수록 다른 사람이 그 코드를 이해하기 쉬워진다. 그래야 결함이 줄어들고 유지보수 비용이 적게 든다**.

1. **좋은 이름을 선택한다.**

   : 이름과 기능이 완전히 딴판인 클래스나 함수로 유지보수 담당자를 놀라게 해서는 안 된다.

2. **함수와 클래스 크기를 가능한 줄인다.**

   : 작은 클래스와 작은 함수는 이름짓기도 쉽고, 구현하기도 쉽고, 이해하기도 쉽다.

3. **표준 명칭을 사용한다.**

   : 해법 영역, 도메인 영역에서 가지고 온 명칭을 사용하자. 그래야 다른 개발자가 설계 의도를 이해하기 쉬워진다.

4. **단위 테스트 케이스를 꼼꼼히 작성한다.**

   : 이해하지 못하게 짜놓은 테스트 코드는 오히려 수정을 복잡하게 만든다. 테스트는 예제로 보여주는 문서다. 잘 만든 테스트 케이스를 통해 코드가 한 눈에 들어오게 하자.

[⬆ Back to top](#목차)
<br />

## 클래스와 메서드 수를 최소로 줄여라

중복을 제거하고, 의도를 표현하고, SRP를 준수한다는 기본적인 개념도 극단으로 치달으면 득보다 실이 많아진다. SRP를 위해 무조건 클래스나 함수를 쪼개다보면 클래스나 함수가 수없이 많아지게 된다. 이건 클린 코드가 아니다. 적절한 추상화를 통해 전체적인 시스템의 크기도 작아야 한다. 그러므로 **함수와 클래스의 수를 가능한 줄이자.**

무의미하고 독단적인 정책, 견해, 규칙 때문에 클래스와 메서드를 불필요하게 늘리는 경우도 있는데, 이러한 것을 멀리하고 실용적인 방식을 택하자. 목표는 **함수와 클래스 크기를 작게 유지하면서 동시에 시스템 크기도 작게 유지하는 것**이다.

[⬆ Back to top](#목차)
