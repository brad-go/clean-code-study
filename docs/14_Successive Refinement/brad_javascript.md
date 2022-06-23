# 14장 점진적인 개선

## 목차

1. [점진적으로 개선하라](#점진적으로-개선하라)
2. [리팩터링이란?](#리팩터링이란)
3. [리팩터링을 해야하는 이유?](#리팩터링을-해야하는-이유)
4. [언제 리팩터링을 해야할까?](#언제-리팩터링을-해야할까)
5. [코드의 악취를 없애라](#코드의-악취를-없애라)
6. [테스트를 통해 시스템의 동작을 보장하라](#테스트를-통해-시스템의-동작을-보장하라)
7. [기본적인 리팩토링 방법](#기본적인-리팩터링-방법)
8. [관심사를 분리하라](#관심사를-분리하라)

## 점진적으로 개선하라

**“진짜 개발은 리팩터링부터 시작된다”** 라는 말이 있다. 한번에 위에서 아래로 읽히고, 코드가 어떤 역할을 하는지 이해할 수 있으며, OCP를 지키는 등의 좋은 코드를 짜는 것은 거의 불가능에 가깝다. 우리가 접하는 잘 짠 코드들은 대부분 점진적인 개선을 통해 이뤄낸 결과물이다. 깨끗한 코드를 짜려면 지저분한 코드를 짠 뒤에 정리해야 한다. **단계적인 개선, 즉 리팩터링이 필요**하다.

[⬆ Back to top](#목차)
<br />

## **리팩터링이란?**

개발을 하다보면 리팩터링이란 말을 많이 접하게 된다. 단순히 코드를 수정하는 것이라고 생각할 수 있다. 그러나 리팩터링이란 **소프트웨어를 잘 이해할 수 있도록 가독성을 향상시키고, 유지보수의 용이성과 재사용성을 높이기 위해 행하는 코드 개선 활동**이다. 단, 소프트웨어의 기능이 동작하지 않거나 성능이 저하되면 안된다.

- **소프트웨어의 동작은 그대로 유지해야 한다.**
- 리팩터링의 목적은 프로그램의 성능을 높이는 것이 아닌 코드를 이해하고 수정하기 쉽게 만드는 것!

[⬆ Back to top](#목차)
<br />

## 리팩터링을 해야하는 이유?

수명이 짧은 소프트웨어도 있지만, 어느 정도 규모가 되는 소프트웨어는 후임 개발자에게 물려주거나 다른 협업자들이 생기기 마련이다. 다른 이들이 이해하기 힘든 코드를 짠다면 어떻게 될까? 그 코드를 이해하는데만 많은 시간이 필요하고, 회사의 입장에선 비용으로 간주될 것이다. 거기다 작업 기간이 길어지다보면 내가 짠 코드도 내가 이해하지 못하는 상황에 이르기도 한다.

**리팩터링이 모든 소프트웨어의 문제를 해결해주는 `마법`은 아니지만, 코드를 건강하게 유지시켜주는 `약`이다**. 이 약은 다음과 같은 효능이 있다.

1. **소프트웨어 설계가 좋아진다.**
   - 중복 코드가 줄어들면 모든 코드가 언제나 고유한 일을 수행함이 보장될 수 있고, 이는 바람직한 설계의 핵심이다.
2. **소프트웨어를 이해하기 쉬워진다.**
   - 코드의 목적이 더 잘 드러나게끔 개선시킨다.
3. **버그를 쉽게 찾을 수 있다.**
   - 코드를 이해하기 쉬운만큼 버그를 쉽게 찾을 수 있다.
4. **프로그래밍 속도를 높일 수 있다.**
   - 위의 효능들로 인해 개발 속도가 높아진다.

모든 약이 주의사항이 있듯이 이 `약`도 다음과 같은 주의사항이 있다.

1. **새 기능의 개발 속도가 저하될 수 있다.**
   - 장기적으로 보았을 때 **리팩터링의 궁극적인 목적은 개발 속도를 높여서 더 적은 노력으로 더 많은 가치를 창출하는 것**이다.
2. **코드 소유권이 없는 부분이 포함되어있을 때, 작업이 어려울 수 있다.**
   - 일부분의 소유권이 없다고 리팩터링 할 수 없는 것은 아니지만, 느슨한 코드 소유 정책을 갖는 것이 좋다.
3. **동시 작업자가 많을 때 브랜치 관리가 어려울 수 있다.**
   - 브랜치 통합 주기를 가능한 짧게, 더 짧게 가져가야 한다.
4. **테스트 코드 작성을 통해 리팩터링 과정 중에 발생하는 오류들을 재빨리 잡아야 한다.**

고전적 관점에서 “소프트웨어 아키텍처는 개발되기 전에 설계하는 것이고 개발이 시작된 후로는 아키텍처가 변경될 수 없다.”라는 관점을 가지고 있었지만, 리팩터링 개념이 활성화 되고 난 후로는 “수년간 운영되고 있는 소프트웨어라도 아키텍처를 변경 할 수 있다.”는 관점으로 변했다.

이렇게 변한 관점으로 인해 소프트웨어 아키텍처는 지속적인 리팩터링으로 현재까지 파악한 요구사항들에 대해서 최선의 설계를 하는 점진적 설계, YAGNI(You aren’t going to need it)를 하는 방식으로 많이 변경되었습니다.

[⬆ Back to top](#목차)
<br />

## 언제 리팩터링을 해야할까?

> **< 3의 법칙 >**
>
> 1. 처음에는 그냥 한다.
> 2. 비슷한 일을 두번째 하게 되면 일단 진행한다.
> 3. 비슷한 일을 세번째 하게 되면 리팩터링한다.

**언제 리팩터링을 해야할까?** 하고 의문이 들 수 있다. 이것에 대한 답은 **언제나 리팩터링을 해야한다**이다. 리팩터링은 수시로 진행해야 하며, 작업 흐름에 자연스럽게 녹아들 수 있어야 한다. ‘리팩터링’의 저자 마틴 파울러에 의하면 기능을 추가하거나, 버그를 수정할 때, 코드 리뷰를 할 때 리팩터링이 권장된다.

이러한 상황들에 바로 더 개선된 코드로 수정할 수 있다면 좋겠지만, 초보 개발자의 입장에서 쉽지 않을 수 있다. 그러므로 우리는 장인 정신이 필요하다. 작은 것부터 하나씩 수정해나가면서 꾸준히 고쳐나가야 한다. 아래와 같은 상황에서는 리팩터링을 해보자.

### 리팩토링을 해야할 때

1. **기능을 새로 추가하기 전**
   - 기능을 추가하기 쉽게 만드는 것이 리팩터링의 핵심이다.
   - 기능을 추가하면서 중복 코드가 생길만한 부분을 함수화 시킨다.
2. **코드를 이해하기 어려울 때**
   - 코드를 이해하기 어렵다면 이해를 위한 리팩터링을 진행한다.
   - 코드만 보더라도 이해를 쉽게 할 수 있도록 변수와 함수의 이름을 변경한다.
   - 코드를 이해하기 쉽게 만드는 것은 협업하기에 좋고, 코드를 오래 보존 할 수 있게 된다.
3. **불필요한 코드를 발견했을 때**
   - 코드가 비효율적으로 수행되는 것을 발견했다면 리팩터링한다.
   - 코드가 쓸데없이 복잡하거나 불필요한 코드를 발견했다면 보이스카우트 규칙을 떠올리자.
   - 원래 하려던 작업 시간을 뺏길 수 있으니 간단한 일이라면 바로 처리하고, 시간이 좀 걸릴 것 같으면 `TODO`를 남겨둔다.
4. **계획된 리팩터링**
   - 미리 새 기능을 추가할 수 있도록 코드를 개선해둔다.
   - 코드가 이미 깔끔하다면 리팩터링 하기에도 더 쉽다.
5. **코드 리뷰 시**
   - 팀원들과의 코드리뷰를 할 때 리팩터링을 진행한다.
   - 다른 사람들이 찾아준 리팩터링 포인트를 개선한다.
   - 리팩터링 포인트에 대한 코드리뷰 시에는 코드에 대한 이해 후 몇 가지 개선 방향을 제시한다.
   - 작성자와 리뷰자가 함께 리팩터링을 진행하면서 자연스러운 페어프로그래밍을 진행하면 좋다.

### 리팩터링을 하지 말아야 할 때

1. **굳이 지금, 수정할 필요가 없을 때**
   - 단순히 호출해서 사용하고 있는 코드의 경우 그냥 둔다.
   - 실제 내부 동작을 이해해야 할 시점에 리팩터링 하는 것이 효과적이다.
2. **새로 작성하는 것이 쉬울 때**

   - 리팩터링보다 새로 코드를 작성하는 쉬운 코드의 경우 그냥 둔다.
   - 어떤 코드가 리팩터링보다 새로 만드는 게 쉬운지에 대한 판단은 많은 경험이 뒷받침 되어야 한다.

[⬆ Back to top](#목차)
<br />

## 코드의 악취를 없애라

어디를 리팩터링 해야할까? 리팩터링 포인트를 찾는 일은 결국 코드 내의 악취(`Code-smell`)을 찾는 일이다. 아래와 같은 상황들은 일반적으로 찾을 수 있는 리팩터링 포인트들이다.

1. **기이한 이름**
   - 함수, 변수, 클래스 등 코드에서 사용되는 모든 이름은 명료해야 한다.
   - 마땅한 이름이 떠오르지 않는다면, 설계와 같은 근본적인 원인이 있을 수도 있다.
   - 단순히 혼란스러운 이름을 정리하기 위해 리팩터링을 진행 하고나면, 코드가 훨씬 간결해질 때가 많다.
2. **중복 코드**
   - 똑같은 코드 구조가 반복될 때, 하나로 통합하여 더 나은 프로그램을 만들 수 있다.
   - 중복 내용이 있다면 함수로 추출하여 사용하기
   - 중복 코드가 있으면 차이점을 살펴봐야 하는 부담이 생기며 변경 시에도 다른 비슷한 코드를 확인해보고 수정해야 한다.
   - 서브 클래스에 중복 내요이 있다면 상위로 위치를 올려보자.
3. **긴 함수**
   - 코드를 이해하고, 공유하고, 선택하기 쉬워진다는 장점은 짧은 코드에서 나온다.
   - 끝없이 위임하고, 함수 이름을 잘 지어야 한다.
   - 주석을 달만한 부분은 함수로 만들어 의도가 드러나게 만든다.
4. **긴 매개변수 목록**
   - 매개변수가 많아지면 그 자체로 이해하기 어려울 때까 많다.
   - 객체를 통째로 넘기거나 매개변수 객체를 만들어 넘기자.
   - 여러 함수를 클래스로 묶는다면 매개변수를 줄일 수 있다.
5. **전역 데이터**
   - 전역 데이터는 누가 어디서 바꿨는지 찾아낼 수 없어 버그가 발생할 수 있다.
   - 함수 스코프에 넣어주자.
6. **가변 데이터**
   - 가변 데이터의 유효 범위가 넓어질수록 문제를 일으킬 가능성이 높아진다.
   - 가변 데이터 사용시 유효 범위를 제한 시켜주어야 한다.
   - 변수 캡슐화를 통해 접근 범위를 최소로 줄이는 것이 좋다.
7. **뒤엉킨 변경**
   - 하나의 코드가 여러가지 이유들에 의해 변경되는 경우 문제가 있다.
   - 하나의 모듈이 여러가지 기능을 담당하는, 즉 단일책임원칙(SRP)이 제대로 지켜지지 않아서 발생한다.
8. **산탄총 수술**
   - 코드를 수정할 때마다 다른 여러가지 코드들을 같이 수정해야하는 경우 문제가 있다.
   - 여러 개의 모듈이 한가지 기능을 담당하는, 즉 단일책임원칙(SRP)이 제대로 지켜지지 않아서 발생한다.
9. **기능 편애**
   - 다른 모듈의 함수나 데이터와 상호작용을 더 많이 하고 있다면 문제가 있다.
   - 해당 모듈의 위치를 변경해주는 것을 고려해야 한다.
10. **데이터 뭉치**
    - 자주 같이 사용되는 데이터들은 별도의 의미가 있을 수 있다.
    - 하나의 오브젝트로 묶어주는 것이 좋다.
11. **기본형 집착**
    - 기본형만 고집하지 말고 클래스로 만들어서 사용하자.
12. **반복되는 스위치문**
    - 중복된 스위치 문을 사용하는 경우 조건문 추가 시 다른 케이스에서도 조건을 모두 추가해줘야 한다.
13. **반복문**

    - 가능하다면 파이프라인으로 변경한다.
    - 데이터가 변경되는 흐름을 명확하게 논리적 흐름대로 볼 수 있다.
    - 파이프라인 함수를 사용하면 변수를 줄일 수 있다.

    ```tsx
    // BAD: loop
    for (let pipe of pipes) {
      if (pipe.type === "metal") {
        pipe = { ...pipe, color: "grey" };
        console.log(pipe);
      }
    }

    // GOOD: pipe line function
    pipes
      .filter(({ type }) => type === "metal")
      .map((pipe) => ({ ...pipe, color: "grey" }))
      .forEach((pipe) => console.log(pipe));
    ```

14. **불필요한 요소**
    - 불필요한 요소들(클래스, 함수, 변수 등)은 제거해주는 것이 좋다.
15. **추측성 일반화**
    - 나중의 작업을 위해 작업해둔, 현재는 사용되지 않는 코드들은 제거해주는 것이 좋다.
    - 하는 일이 없는 클래스는 합쳐버리고, 인라인 코드 활용으로 걸리적거리는 코드를 치워버린다.
16. **임시 필드**
    - 특정 상황에서만 값이 설정되는 필드가 있다면 해당 필드의 존재 이유를 파악하기 쉽지 않다.
    - 클래스를 분리하던가, 해당 필드를 함수에 넣는 등의 방법으로 필드의 위치를 변경해주는 것이 좋다.
17. **메서드 체인**
    - 객체 요청이 꼬리를 물고 이어지는 코드는 객체 네비게이션에 종속되었음을 의미한다.
    - 함수화를 통해 객체 체이닝을 숨기는 것이 좋다.
18. **중재자**
    - 단순히 중간 역할만 하고 있는 함수가 많다면 쓸모없는 코드들이 많은 것일 수 있다.
    - 불필요한 중재자 코드를 제거하고, 직접 소통할 수 있도록 하자.
19. **내부자 거래**
    - 모듈 간의 데이터 조회가 많다면 결합도가 높다는 의미이다.
    - 최소로 줄이고 투명하게 처리한다.
20. **거대한 클래스**
    - 클래스에 필드가 많으면 중복 코드가 생기기 쉽다.
    - 너무 많은 클래스가 생기는 것도 혼동을 일으키기 쉽다.
    - 최대한 중복을 줄이고, 클래스를 나누자.
21. **서로 다른 인터페이스의 대안 클래스들**
    - 클래스의 장점은 필요할 때 교체가 가능한 것이다.
    - 교체가 가능하도록 인터페이스를 잘 선언해줘야 한다.
22. **데이터 클래스 혹은 자료구조**
    - 데이터 클래스(필드와 getter/setter로 구성된 클래스)는 캡슐화를 통해 필드를 잘 숨겨야 한다.
    - Immutable한 VO의 경우 필드를 직접 공개해도 된다.
23. **상속 포기**
    - 부모 클래스의 메서드 혹은 데이터가 필요없을 수 있다.
    - 하지만 구현을 따르지 않을 수 있지만, 인터페이스를 따르지 않는 것은 문제가 있을 수 있다.
24. **주석**
    - 장황한 주석은 코드가 잘못되었기 때문일 확률이 높다.
    - 주석대신 함수로 코드를 명확하게 표현한다.

[⬆ Back to top](#목차)
<br />

## 테스트를 통해 시스템의 동작을 보장하라

잘못된 코드는 리팩터링이 필요하다. 그러나 리팩터링 이후에 모든 프로그램이 동일하게 동작하지 않거나 중단될 수도 있다. 이것을 피하기 위해 TDD가 필요하다.

TDD 기법을 통해 항상 시스템이 돌아가야 한다는 원칙을 따라야 한다. 개선이라는 이름 아래 구조를 크게 바꾸는 경우가 있다. 어떤 프로그램은 개선에서 결코 회복하지 못한다. 그러나 TDD는 시스템을 망가뜨리는 변경을 허용하지 않는다. 자동화된 테스트 슈트를 통해 변경을 가한 후에도 시스템이 똑같이 동작한다는 것을 알 수 있다.

- 테스트를 완전히 자동화해야한다(결과 체크까지).
- **테스트 코드가 있으면 새로운 기능 추가로 인해 발생할 버그를 예방할 수 있다.**
- 리팩터링하려면 테스크 코드가 필요하다.

[⬆ Back to top](#목차)
<br />

## 기본적인 리팩터링 방법

위에서 리팩터링에 대한 개념과 언제, 어디서 해야하는지에 대해 알게 되었고, 테스트의 필요성도 알게되었다. 그렇다면 어떻게 리팩터링 하는지 방법을 알아보자.

### 1. 함수 추출하기

목적과 구현에 따른 함수로 코드 조각을 추출한다.

- 세부 사항을 읽지 않고도 코드가 무엇을 하는지 알 수 있다.
- 짧은 기능이 더 읽기 쉽다.

**Bad:**

```tsx
const checkValidation = async (encryptedParam: string) => {
  const { validationCode } = await parseEncrypted({ encryptedParam });
  const validCode = VALID_CODE[validationCode];

  if (validCode === "error") goMain();
  else if (validCode === "expired") toast("만료되었습니다.");
  else if (validCode === "different") toast("명의가 일치하지 않습니다.");
};
```

**Good:**

```tsx
const checkValidation = async (encryptedParam: string) => {
  const { validationCode } = await parseEncrypted({ encryptedParam });
  handleInvalid(VALID_CODE[validationCode]);
};

const handleInvalid = (validCode: string) => {
  if (validCode === "error") goMain();
  else if (validCode === "expired") toast("만료되었습니다.");
  else if (validCode === "different") toast("명의가 일치하지 않습니다.");
};
```

### 2. 함수 인라인화하기

본문 코드가 함수명만큼이나 명확하거나 간접 호출이 과하게 많을 경우 함수를 제거한다.

**Bad:**

```tsx
const showExistToast = () => {
  const message = isLastStep(step) ? "저장하고 이탈" : "저장하지 않고 이탈";
  toast(message);
};

const isLastStep = (step: string) => step === "4";
```

**Good:**

```tsx
const showExistToast = () => {
  const message = step === "4" ? "저장하고 이탈" : "저장하지 않고 이탈";
  toast(message);
};
```

### 3. 변수 추출하기

표현식에 이름을 추가해서 더 알기 쉽게 만들어준다.

**Bad:**

```tsx
const getTimerFormat = (time: number) =>
  `${Math.trunc(time / 60)
    .toString()
    .padStart(2, "0")}:${(time % 60).toString().padStart(2, "0")}`;
```

**Good:**

```tsx
const getTimerFormat = (time: number) => {
  const minutes = Math.trunc(time / 60)
    .toString()
    .padStart(2, "0");
  const seconds = (time % 60).toString().padStart(2, "0");

  return `${minutes}:${seconds}`;
};
```

### 4. 변수 인라인화하기

변수명이 원래 표현식과 다를 바 없을 때 변수를 제거하고 인라인으로 사용한다.

**Bad:**

```tsx
const isEnd = next > 10;
return isEnd;
```

**Good:**

```tsx
return next > 10;
```

### 5. 함수 선언 바꾸기

함수의 이름을 재정의하고, 매개변수 목록을 변경한다.

- 가끔 캡슐화보다 더 낫고, 재사용하기도 쉽다.

**Bad:**

```tsx
const circum = (radius) => { ... }
```

**Good:**

```tsx
const circumference = (radius) => { ... }
```

### 6. 변수를 캡슐화하기

일부 자료 구조에 대한 참조를 캡슐화한다.

- 접근 범위가 넓은 데이터를 캡슐화해서 내부 변수로 만들어준다.
- 데이터를 변경하고 사용하는 코드에 대한 전/후 검증 및 로직을 쉽게 끼워넣을 수 있다.
- 불변 데이터는 수정이 불가하기 때문에 캡슐화할 이유가 없다.

**Bad:**

```tsx
const defaultOwner = {
  firstName: "Martin",
  lastName: "Fowler",
};
```

**Good:**

```tsx
class Owner {
  static DEFAULT_OWNER = {
    firstName: "Martin",
    lastName: "Fowler",
  };

  constructor(private _owner = DEFAULT_OWNER) {}

  get owner() {
    return this._owner;
  }

  set owner(owner) {
    this._owner = owner;
  }
}
```

### 7. 변수 이름 바꾸기

변수 이름이 목적과 다르거나 명확하지 않은 경우 변경한다.

- 변수는 의도를 표현해주나 이는 이름을 잘 지었을 때만 해당한다.
- 사용범위가 큰 변수일수록 이름이 중요하다.
- 변수명만으로 코드가 하는 일을 파악하기 쉬워진다.

**Bad:**

```tsx
const a = width * height;
```

**Good:**

```tsx
const area = width * height;
```

### 8. 매개변수 객체 만들기

데이터가 여러 함수로 함께 몰려다니는 경우 여러 데이터 구조를 하나로 모아준다.

- 데이터 사이의 관계가 명확해진다.
- 매개변수 수가 줄어들고 관련 함수들의 일관성도 높여준다.
- 궁극적으로 코드를 더 근본적으로 바꿔줄 수 있는 발판을 마련해준다.
- 이 데이터 구조가 문제 영역을 간결하게 나타내는 추상 영역으로 간주되어 코드의 개념적인 그림을 다시 설계할 수 있다.

**Bad:**

```tsx
const station = {
  name: "ZB1",
  readings: [
    { temp: 47, time: "2022-6-16 09:10" },
    { temp: 53, time: "2022-6-16 09:20" },
    { temp: 58, time: "2022-6-16 09:30" },
    { temp: 53, time: "2022-6-16 09:40" },
    { temp: 51, time: "2022-6-16 09:50" },
  ],
};

const operationPlan = {
  temperatureFloor: 50,
  temperatureCeiling: 55,
};

const readingOutsideRange = (station: Station, min: number, max: number) => {
  return station.readings.filter(
    ({ temp }: Reading) => temp < min || temp > max
  );
};

const alerts = readingOutsideRange(
  station,
  operationPlan.temperatureFloor,
  operationPlan.temperatureCeiling
);
```

**Good:**

```tsx
class TempRange {
  private _data: Minmax;
  constructor(min: number, max: number) {
    this._data = { min, max };
  }

  get min() {
    return this._data.min;
  }

  get max() {
    return this._data.max;
  }

  contains(temp: number) {
    return this.min <= temp && temp <= this.max;
  }
}

const station = {
  name: "ZB1",
  readings: [
    { temp: 47, time: "2022-6-16 09:10" },
    { temp: 53, time: "2022-6-16 09:20" },
    { temp: 58, time: "2022-6-16 09:30" },
    { temp: 53, time: "2022-6-16 09:40" },
    { temp: 51, time: "2022-6-16 09:50" },
  ],
};

const operationPlan = {
  temperatureFloor: 50,
  temperatureCeiling: 55,
};

const range = new TempRange(
  operationPlan.temperatureFloor,
  operationPlan.temperatureCeiling
);

const readingOutsideRange = (station: Station, range: TempRange) => {
  return station.readings.filter(({ temp }: Reading) => !range.contains(temp));
};

const alerts = readingOutsideRange(station, range);
```

### 9. 여러 함수를 클래스로 묶기

공통 데이터를 중심으로 긴밀하게 작동하는 함수들이 발견되는 경우 클래스로 묶어준다.

- 함수들이 공유하는 공통 환경을 명확하게 표현할 수 있다.
- 각 함수에 전달되는 인수를 줄여서 객체 내부에서의 함수 호출을 간결하게 만들 수 있다.

**Bad:**

```tsx
const base = (reading) => { ... };
const taxableCharge = (reading) => { ... };
const calculateBaseCharge = (reading) => { ... }
```

**Good:**

```tsx
class Reading {
	base() { ... }
	taxableCharge() { ... }
	calculateBaseCharge() { ...}
}
```

### 10. 여러 함수를 변환 함수로 묶기

데이터를 입력받아 여러 가지 정보를 도출하는 로직이 분산된 경우 함수 하나로 묶어줄 수 있다. 위의 클래스로 묶기를 사용하도 괜찮다. 단, 원본 데이터를 갱신하는 내용이 있다면 함수를 클래스로 묶어준다.

- 검색과 갱신을 일관된 장소에서 처리할 수 있고, 로직 중복도 막을 수 있다.
- 도출 과정을 검토할 일이 생겼을 때, 변환 함수만 살펴보면 된다.

**Bad:**

```tsx
const base = (aReading) => {...}
const taxableCharge = (aReading) => {...}
```

**Good:**

```tsx
const base = (reading) => { ... };
const taxableCharge = (reading) => { ... };

const enrichReading = (reading) => {
  const newReading = _.cloneDeep(argReading);
  newReading.baseCharge = base(newReading);
  newReading.taxableCharge = taxableCharge(newReading);
  return newReading
}
```

### 11. 단계 쪼개기

여러 단계로 분리하면 좋을만한 코드를 발견했을 때, 두 대상을 동시에 생각할 필요없이 하나에만 집중하기 위해 별개 모듈로 나누는 작업을 진행한다. 단순히 동작을 연이은 두 단계로 쪼개거나, 필요시 다루기 편한 형태로 먼저 가공한다.

- 하나의 목적에만 집중할 수 있어서 코드 수정이 쉬워진다.

**Bad:**

```tsx
const priceOrder = (
  product: Product,
  quantity: number,
  shippingMethod: ShippingMethod
) => {
  const basePrice = product.basePrice * quantity;
  const discount =
    Math.max(quantity - product.discountThreshold, 0) *
    product.basePrice *
    product.discountRate;
  const shippingPerCase =
    basePrice > shippingMethod.discountThreshold
      ? shippingMethod.discountFee
      : shippingMethod.feePerCase;
  const shippingCost = quantity * shippingPerCase;
  const price = basePrice - discount + shippingCost;
  return price;
};
```

**Good:**

```tsx
const priceOrder = (
  product: Product,
  quantity: number,
  shippingMethod: ShippingMethod
) => {
  const priceData = calculatePricingData(product, quantity);
  const price = applyShipping(priceData, shippingMethod);

  return price;
};

const calculatePricingData = (product: Product, quantity: number) => {
  const basePrice = product.basePrice * quantity;
  const discount =
    Math.max(quantity - product.discountThreshold, 0) *
    product.basePrice *
    product.discountRate;

  return { basePrice, quantity, discount };
};

const applyShipping = (
  priceData: PriceData,
  shippingMethod: ShippingMethod
) => {
  const shippingPerCase =
    priceData.basePrice > shippingMethod.discountThreshold
      ? shippingMethod.discountFee
      : shippingMethod.feePerCase;
  const shippingCost = priceData.quantity * shippingPerCase;

  return priceData.basePrice - priceData.discount + shippingCost;
};
```

[⬆ Back to top](#목차)
<br />

## 관심사를 분리하라

소프트웨어 설계는 분할만 잘해도 품질이 크게 높아진다. 관심사를 분리하면 코드를 이해하고 보수하기가 훨씬 더 쉬워진다.

단순히 돌아가는 코드에 만족하는 프로그래머는 전문가 정신이 부족하다. 나쁜 코드보다 더 오랫동안 더 심각하게 개발 프로젝트에 악영향을 미치는 요인도 없다.

물론 나쁜 코드도 깨끗한 코드로 개선할 수 있다. 그러나 오래된 의존성을 찾아내 깨려면 상당한 시간과 인내심이 필요하다. 반면 처음부터 코드를 깨끗하게 유지하기란 상대적으로 쉽다. 그러므로 코드는 언제나 최대한 깔끔하고 단순하게 정리해야 한다.

[⬆ Back to top](#목차)
<br />
