# 8장 경계

개발을 하다보면 외부 API를 사용하게 된다. 이러한 인터페이스는 수많은 기능을 제공하지만, 위험도 클 수 있다. 어떤 식으로든 이 외부 코드를 우리 코드에 깔끔하게 통합해야 한다. 소프트웨어 경계를 깔끔하게 처리하는 기법을 알아보자.

## 목차

- [라이브러리 vs API](#라이브러리-vs-api)
- [외부 코드 사용하기](#외부-코드-사용하기)
- [경계 살피고 익히기](#경계-살피고-익히기)
- [학습 테스트는 공짜 이상이다](#학습-테스트는-공짜-이상이다)
- [아직 존재하지 않는 코드를 사용하기](#아직-존재하지-않는-코드를-사용하기)
- [깨끗한 경계](#깨끗한-경계)
- [팀 프로젝트에서 경계 다루기](#팀-프로젝트에서-경계-다루기)

## 라이브러리 vs API

### API

응용 프로그램을 작성할 때, 필요한 매개체를 뜻한다. 실무 개발에서는 프로그램의 크기가 커서 처음부터 끝까지 모두 개발할 수 없기 때문에 이미 만들어져 있는 소프트웨어들을 결합해서 만드는 경우가 많은데, 라이브러리도 그 중 하나이다. 이런 자원들을 결합하기 위한 매개체가 바로 API다.

> 예) React.js, RESTful API, 기상청 날씨 API

### 라이브러리

소프트웨어를 개발할 때 사용하는 자원의 모임. 기능들을 함수 단위로 만들어 제공된다. 어떻게 사용할 지는 사용자가 직접 결정하고 소스 코드는 공개하지 않는다.

> 예) Moment.js, uuid, winston 등

### API와 라이브러리의 차이

API가 단순히 컴퓨터공학 분야의 추상적인 **개념**이라면 라이브러리는 이러한 API들을 기반으로 개발자에게 기능을 제공할 수 있도록 실제 구현된 **구현체**라고 볼 수 있다.

대개의 경우 독립된 응용 프로그램(Application) 간의 상호작용은 '이미 구현된 코드'의 재사용이기 때문에 라이브러리는 다시 쓰기 위해 미리 짜놓은 코드 뭉치들을 의미하는 것이고, API를 기반으로 구현되었다고 볼 수 있다.

[프레임워크](https://namu.wiki/w/%ED%94%84%EB%A0%88%EC%9E%84%EC%9B%8C%ED%81%AC)는 그것을 기반으로 명확하게 정의된 대량의 라이브러리가 있다는 점에서 API와 비슷하다. 하지만 일반적인 API는 전체 제어 구조를 호출하는 쪽에서 원하는대로 진행할 수 있지만, 프레임워크에서는 그럴 수 없는 경우가 많다는 점이 다르다.

**[⬆ back to top](#목차)**
<br />

## 외부 코드 사용하기

소프트웨어 경계(시스템 경계)란 **인터페이스 제공자와 사용자 사이에서 발생하는 입장차이로 인해 문제가 발생할 수 있는 부분**(**경계**)를 의미한다.

- 제공자는 최대한 **범용성**을 넓히려 애쓴다.
- 사용자는 **자신의 요구에 집중**하는 인터페이스를 바란다.

자바스크립트의 `Map` 객체를 살펴보자. `Map`은 자바스크립트의 `object`와 유사하고, 자바의 `Map` 인터페이스처럼 많은 기능을 제공한다.

#### 메서드 목록

- **clear()**: `Map` 객체의 모든 키-값 쌍을 제거한다.
- **delete(key)**: `Map`객체에 키가 존재하면 그것을 제거하고 `true`를 반환, 없다면 `false` 반환
- **get(key)**: 키에 해당하는 값을 반환하고, 없다면 `undefined`를 반환
- **has(key)**: `Map`객체에 해당하는 키가 있는지 `boolean`값 반환
- **set(key, value)**: `Map`객체에 `value`와 `key`를 설정하고, `Map` 객체를 반환
- **keys()**: `Map` 객체의 각 요소에 대한 키를 삽입 순서로 포함하는 `Iterator`(반복자)를 반환
- **values()**: `Map` 객체의 각 요소에 대한 값을 삽입 순서로 포함하는 `Iterator`를 반환
- **entries()**: `Map` 객체의 각 요소에 대해 `[key, value]` 배열을 삽입 순서로 포함하는 `Iterator`를 반환

<details><summary><b>자바스크립트로 구현한 자바 Map</b></summary><div markdown="1">

```js
interface EntryType {
  getKey(): Key;
  getValue(): Value;
  setValue(value: Value): Value;
}

export class Entry implements EntryType {
  private key: Key;
  private value: Value;

  constructor(key: Key, value: Value) {
    this.key = key;
    this.value = value;
  }

  getKey(): Key {
    return this.key;
  }

  getValue(): Value {
    return this.value;
  }

  setValue(value: Value): Value {
    this.value = value;
    return this.value;
  }
}

export class HashMap extends Map<Key, Value> {
  // export class HashMap extends Map {
  constructor() {
    // get, set, has, size, clear, delete, entries, keys, values
    super();
  }

  // map이 비어있는지 확인
  isEmpty(): boolean {
    return this.size === 0;
  }

  getClass(): Object {
    return this;
  }

  // 특정한 키를 가지고 있는지 확인
  containsKey(key: Key): boolean {
    return this.get(key) !== undefined;
  }

  // 특정한 값이 있는지 확인
  containsValue(value: Value): boolean {
    // [1, 2, 3]과 같은 배열인 값을 확인하려고 하면 메모리 값이 다르기 때문에, false를 반환
    // 그래서 직렬화를 통해서 비교해준다.
    const output = [...this.values()].some(
      (one) => JSON.stringify(one) === JSON.stringify(value)
    );
    return output;
  }

  // entry 객체로 가진 키, 값쌍을 반환
  entrySet(): Set<EntryType> {
    const entry: EntryType[] = [];
    for (const [key, value] of this) {
      entry.push(new Entry(key, value));
    }
    return new Set(entry);
  }

  // 두 객체의 내용이 같은 검사한다.
  equals(obj: Object): boolean {
    return JSON.stringify(this) === JSON.stringify(obj);
  }
}

// 사용
const hash = new HashMap();
hash.set("1", 700);
hash.set("2", [1, 2, 3]);
hash.set("3", "This is a string");
hash.set("4", () => console.log("Hello world"));
console.log(hash);
```

</div></details>

Map객체는 유용하지만 그만큼 위험도 클 수 있다.

예를 들어:

1. `Map`을 만들어 인수나 반환으로 이리 저리 넘길 경우 `clear()` 메서드 사용으로 어디선가 예상치 않게 지울 수 있다.
2. Map에 특정 객체 유형만 저장하기로 결정했을 때, Map은 유형을 제한하지 않기 때문에 사용자가 예상치 않은 객체를 추가할 위험도 있다.
   - 타입스크립트의 interface를 통해 타입을 지정한다면 이 문제는 해결할 수 있다.
3. Map이 변경될 경우 수정할 코드가 너무 많아질 수 있다.

위의 문제들을 **객체를 감싸는 방법**으로 해결이 가능하다.

- Sensors 사용자는 외부 라이브러리를 어떤 식으로 사용하는지 신경쓰지 않아도 된다.
- 예를 들어 `clear()`와 같은 필요하지 않은 인터페이스를 제공하지 않을 수 있다.

가장 중요한 것은 `Map`과 같은 경계 인터페이스를 여기저기 넘기지 않는 것이 가장 중요하다. **공개 API의 인수로 넘기거나 반환값으로 사용하지 않도록 주의**해야 한다. 즉, 이러한 **인터페이스를 이용하는 클래스를 밖으로 노출되지 않도록 주의**해야 한다. 하지만 피치 못하게 노출될 경우 캡슐화를 통해 설계 규칙을 지키도록 강제해주는 것이 좋다.

### Bad:

```tsx
const sensors: Map<string, Sensor> = new HashMap();
const s = sensors.get(sensorId);
```

### Good:

```tsx
class Seonsors {
  private seonsors = new HashMap();

  getById(id: string) {
    return this.seonsors.get(id); // sensors가 가진 요소 중 키에 일치하는 값을 반환
  }

  // 아래와 같이 set을 오버라이드해서 sensors에 키-값 쌍을 추가할 수 있다.
  // set(key: any, value: any) {
  //   this.seonsors.set(key, value);
  // }
}
```

**[⬆ back to top](#목차)**
<br />

## 경계 살피고 익히기

타사 라이브러리를 처음 가져왔을 때, 사용법이 분명치 않다고 가정하면 다음과 같은 과정을 겪는다.

1. 하루나 이틀(혹은 더 오랜시간) 문서를 읽으며 사용법을 결정
2. 코드를 작성해 라이브러리가 예상대로 동작하는지 확인
3. 반복적인 디버깅

하지만 **간단한 테스트 케이스를 작성해 외부 코드 익힌다**면 어떨까? 이를 **학습테스트**라고 부르며, 학습 테스트는 프로그램에서 사용하려는 방식대로 외부 API를 호출한다. 통제된 환경에서 API를 제대로 이해하는지를 확인하는 것이다. 학습 테스트는 API를 사용하려는 목적에 초점을 맞춘다.

- **학습 테스트를 통해 외부 코드를 익히고 통합하는 어려운 일을 쉽게 할 수 있다**.

### moment.js 익히기 (학습 테스트 예시)

유용한 자바스크립트 라이브러리인 **moment.js**를 사용하려 한다고 가정해보자. 테스트 도구는 **Jest**를 사용했다.

1. 패키지를 내려 받아 소개 페이지를 연다.
   <br/>

2. 문서를 자세히 읽기 전에 첫 번째 테스트 케이스를 작성한다.

   ```tsx
   // moment.test.ts
   import moment from "moment";

   const getSpecificDate = (day: number) => {
     return moment().date(+day);
   };

   test("현재 날짜 기준으로 증가 및 감소한 날짜 출력하기", () => {
     expect(getSpecificDate(2)).toBe(30); // 28일 기준
   });
   ```

3. 테스트 케이스를 돌려본다.

- 테스트를 돌려보니 이번 달의 1일의 날짜 객체가 문자열로 출력된다.

4. 문서를 읽어보니 `format()`이라는 메서드가 있다. 그래서 이를 추가하고, 아까 결과가 문자열이었으니 `Number` 형식으로 변환한 후에 테스트 케이스를 다시 돌린다.

   ```tsx
   // moment.test.ts
   import moment from "moment";

   const getSpecificDate = (day: number) => {
     return Number(moment(day).format("DD"));
   };

   test("현재 날짜 기준으로 증가 및 감소한 날짜 출력하기", () => {
     expect(getSpecificDate(2)).toBe(30); // 28일 기준
   });
   ```

   날짜가 더해지지 않고, 1이 반환값이 되는 것을 확인했다.
   <br/>

5. 구글을 검색한 후 아래와 같이 시도한다.

   ```tsx
   // moment.test.ts
   import moment from "moment";

   const getSpecificDate = (day: number) => {
     const specificDay = Number(moment().add(day, "day").format("DD"));
     return specificDay;
   };

   test("현재 날짜 기준으로 증가 및 감소한 날짜 출력하기", () => {
     expect(getSpecificDate(2)).toBe(30); // 28일 기준
   });
   ```

6. 테스트 케이스를 짜는 과정에서 moment.js의 동작을 많이 이해했고, 이 지식을 바탕으로 단위 테스트 케이스 몇 개를 작성한다.

   ```tsx
   // moment.ts

   import moment, { Moment } from "moment";

   export const getDate = () => {
     const today = moment().date();
     return today;
   };

   export const getSpecificDate = (day: number) => {
     const specificDay = Number(moment().add(day, "day").format("DD"));
     return specificDay;
   };

   export const getSpecificFormDate = (format: string) => {
     const now = moment().format(format);
     return now;
   };

   export const getDateAfterSpecificTime = (
     time: number,
     unit: moment.DurationInputArg2
   ) => {
     const afterTime = moment().add(time, unit).format("YY-MM-DD");
     return afterTime;
   };

   export const getDateBeforeSpecificTime = (
     time: number,
     unit: moment.DurationInputArg2
   ) => {
     const beforeTime = moment().subtract(time, unit).format("YY-MM-DD");
     return beforeTime;
   };

   export const getDiffTimeFromNow = (date: string) => {
     return moment(date).fromNow();
   };

   export const getDiffTimeFromSpecificDay = (date: string) => {
     return moment().from(moment(date));
   };
   ```

   ```tsx
   // moment.test.ts

   import moment from "moment";
   import {
     getDate,
     getDateAfterSpecificTime,
     getDateBeforeSpecificTime,
     getDiffTimeFromNow,
     getDiffTimeFromSpecificDay,
     getSpecificDate,
     getSpecificFormDate,
   } from "./moment";

   test("오늘 날짜 구하기", () => {
     expect(getDate()).toBe(28);
   });

   test("현재 날짜 기준으로 증가 및 감소한 날짜 출력하기", () => {
     expect(getSpecificDate(2)).toBe(30);
   });

   test("특정 형식으로 날짜 구하기", () => {
     expect(getSpecificFormDate("YY-MM-DD")).toBe("22-04-28");
   });

   test("특정 기간 이후의 날짜 구하기", () => {
     expect(getDateAfterSpecificTime(1, "month")).toBe("22-05-28");
   });

   test("특정 기간 이전의 날짜 구하기", () => {
     expect(getDateBeforeSpecificTime(2, "year")).toBe("20-04-28");
   });

   test("현재 날짜 및 시간 기준으로 상대적인 시간 구하기", () => {
     expect(getDiffTimeFromNow("2021-07-08")).toBe("10 months ago");
   });

   test("지정 날짜 기준으로 상대적인 시간 구하기", () => {
     expect(getDiffTimeFromSpecificDay("2021-07-08")).toBe("in 10 months");
   });
   ```

7. 익힌 지식을 통해 날짜 및 시간을 반환하는 날짜 클래스로 캡슐화한다. 그러면 나머지 프로그램은 moment.js 경계 인터페이스를 몰라도 된다.

**[⬆ back to top](#목차)**
<br/>

## 학습 테스트는 공짜 이상이다.

학습 테스트는 드는 비용은 없지만, 필요한 지식만 확보할 수 있는 방법이다. 추가적으로 아래의 이점도 있다.

- 패키지가 예상대로 도는지 검증하고, 우리 코드와 **통합 이후에도 주기적으로 검증이 가**능하다.
  - 패키지의 새 버전이 나오면 학습 테스트만 돌려 차이가 있는지만 확인할 수 있다.
  - 호환되지 않을 경우 코드를 수정하든, 패키지를 수정하든 조치를 미리 취할 수 있다.
- 경계 테스트가 있다면 패키지의 **새 버전으로 이전하기 쉬워진다**.

**[⬆ back to top](#목차)**
<br/>

## 아직 존재하지 않는 코드를 사용하기

지금 알지 못하는 코드 영역을 개발할 때도 경계는 유용하게 쓰일 수 있다. 알지 못하는 영역에서 먼 부분부터 차근차근 작업해나가면 경계에 도달하게 되고, 그에 필요한 인터페이스를 알 수 있을 것이다.

- 필요한 인터페이스를 구현하면 인터페이스를 전적으로 통제할 수 있다.
- 코드 가독성이 높아지고, 코드 의도가 분명해진다.
- 테스트도 간편하게 진행할 수 있다.

**[⬆ back to top](#목차)**
<br/>

## 깨끗한 경계

**변경**이 이루어질 때 **경계를** 통해 **통제**하고 있었다면, 향후 변경으로 인해 발생하는 비용을 줄일 수 있다.

- 경계에 위치하는 코드는 깔끔히 분리한다.
- 기대치를 정의하는 테스트 케이스도 작성한다.

통제가 불가능한 외부 패키지에 의존하는 대신 통제가 가능한 우리 코드에 의존하는 편이 훨씬 낫다고 할 수 있다. 외부 패키지를 호출하는 코드를 가능한 줄여 경계를 관리하자.

- 위의 `Map`과 같이 새로운 클래스로 경계를 감싼다.
- [**ADAPTER 패턴**](https://www.zerocho.com/category/JavaScript/post/57babe9f5abe0c17006fe230)을 사용해 우리가 원하는 인터페이스를 패키지가 제공하는 인터페이스로 변환하자.

이 방법들을 통해 코드 **가독성**이 높아지며, **경계 인터페이스를 사용하는 일관성**도 높아지고, **외부 패키지가 변했을 때 변경할 코드도 줄어든다**.

#### ADAPTER 패턴

브래드는 친구들과 오늘 치킨을 먹기로 약속했었다. 그러나 몇몇 친구들이 피자를 먹기를 원한다. 투표를 통해 결정하고자한다. 그러나 브래드는 귀가 얇아, 많은 친구들이 원하는 음식에 투표하려고 한다.

치킨을 원하는 친구들이 많다면 치킨에 투표하고, 피자를 원하는 친구들이 많다면 피자에 투표하려고 한다. 이럴 때 적응자 패턴이 도움이 된다.

```tsx
const voteSystem = new VoteSystem(chickenAdapater);
const voteSystem = new VoteSystem(pizzaAdapter);
```

현재 시스템은 치킨에 투표하는 시스템이고, 신규 시스템은 피자에 투표하는 시스템이다. 뒤의 인자만 바꾸면 시스템을 전환해 투표할 수 있게 만들고 싶다면 위와 같이 나타낼 수 있다. 즉, chicken이냐 pizza냐에 따라 시스템을 교체할 수 있다.

이제 내부 코드를 구현해보자.

```tsx
interface Adapter {
  vote(): void;
}

class VoteSystem {
  adapter: Adapter;
  constructor(adapter: Adapter) {
    this.adapter = adapter;
  }

  vote(): void {
    this.adapter.vote();
  }
}

const chickenAdapater = {
  vote: () => {
    console.log("그래, 치킨 먹자!");
  },
};

const pizzaAdapter = {
  vote: () => {
    console.log("그래, 그래. 피자 먹자!");
  },
};
```

이제 친구들이 원하는 것에 따라 바꿔가며 투표해보자. 여기서 **새 시스템과 이전 시스템의 메서드는 같을 수록 좋다. 사용자가 시스템마다 다른 메서드를 외울 필요가 없기 때문**이다.

```tsx
voteSystem = new VoteSystem(chickenAdapter);
voteSystem.vote(); // "그래, 치킨 먹자!"
voteSystem = new VoteSystem(chickenAdapter);
voteSystem.vote(); // "그래, 그래. 피자 먹자!"
```

이제 피자 먹기에 투표하기로 결정하고 마지막 친구의 투표를 기다린다. 그러나 마지막 친구가 갈비를 먹자고 친구들을 다시 선동한다. 정말 빠르게 투표 시스템을 바꿨다.

```tsx
const galbiAdapter = {
  vote: () => {
    console.log("갈비.. 먹을까?");
  },
};

voteSystem = new VoteSystem(galbiAdapter);
voteSystem.vote(); // "갈비.. 먹을까?"
```

정리하면, **적응자 패턴은 두 가지 이상의 구조에서 유연하게 전환하고 싶을 때 유용**하다. 뒤의 인자만 바꾸면 구조가 교체되기 때문에 간단하다.

**[⬆ back to top](#목차)**
<br/>

## 팀 프로젝트에서 경계 다루기

### 1. MIN-MAX

**팀에서 min과 max에 대한 이름을 사용할 때, 개념 정의를 함께 하고, 컨벤션을 정해두어야 한다**. `min, max`는 각각 경우에 따라 `미만, 초과` 또는 `이하, 이상` 둘 중에 하나로 해석될 수 있는데, 소속된 팀에서 한 가지로 정하고 그 컨벤션을 따라야 한다.

### Bad:

```tsx
// min초과 max미만의 난수를 발생시키는 함수
const generateRandomNumber = (min, max) => {
  return Math.floor(Math.random() * (max - min + 1)) + min;
};

const MAX_NUMBER = 100;
const MIN_NUMBER = 10;
```

위 코드에서 문제점은 무엇일까? 값을 상수화하여 다른 이들이 코드를 볼때 쉽게 이해할 수 있도록 짜여있다. 그러나 `MAX_NUMBER`와 `MIN_NUMBER`가 뜻하는 것이 최댓값과 최솟값을 포함하는 건지, 아닌지 알기 어렵다.

### Good:

```tsx
// min초과 max미만의 난수를 발생시키는 함수
const generateRandomNumber = (min: number, max: number) => {
  return Math.floor(Math.random() * (max - min - 1) + 1) + min;
};

// 미만, 초과
const MAX_NUMBER_LIMIT = 100;
const MIN_NUMBER_LIMIT = 10;
```

다음과 같이 LIMIT라는 이름을 붙여, 상수가 그 값을 포함하지 않는 그 값의 미만, 초과를 지칭함을 알 수 있다.

```tsx
// min이상 max이하의 난수를 발생시키는 함수
const generateRandomNumber = (min: number, max: number) => {
  return Math.floor(Math.random() * (max - min + 1)) + min;
};

// 이상, 이하
const MAX_IN_NUMBER = 100;
const MIN_IN_NUMBER = 10;
```

혹은 IN을 붙여서 숫자를 포함한다는 것을 나타낼 수 있다.

### 2. BEGIN-END

**시작(begin)에서 경계는 포함되고, 끝(end)에서는 경계가 포함되지 않는 암묵적인 규칙**이 있다. 예를 들어, 에어비앤비 사이트에서도 체크인 날짜(begin)는 포함되고, 체크아웃 날짜(end)는 포함되지 않는다. 시중의 수많은 데이터 피커 라이브러리도 이와 같이 `begin과 end`를 구분한다.

```tsx
const reservationDate = (beginDate: string, endDate: string) => {
  // ... some code
};

reservationDate("2021-12-10", "2021-12-12");
```

### 3. FIRST-LAST

양 끝 모두 포함되는 경우 `first-last` 키워드를 사용하는 것이 좋다. (**~부터~까지**)

```tsx
const students = ["James", "Brad", "Kyrie", "Durant"];

const getStudents = (first: string, last: string) => {
  const firstIdx = students.indexOf(first);
  const lastIdx = students.indexOf(last);

  const newStudents = students.slice(firstIdx, lastIdx + 1);
  return newStudents; // [James, Brad, Kyrie];
};
```

### 4. PREFIX-SUFFIX

이름을 지을 때, `prefix`(접두사)와 `suffix`(접미사)를 염두하면 좋다. 코드를 짤 때, 아래와 같이 prefix와 suffix를 활용하여 규칙을 만들고, 공유하며 **일관된 네이밍**을 하는 것이 가독성과 유지보수에 좋다.

### 접두사(prefix)

```tsx
// 1. 자바스크립트의 getter와 setter
const obj = {
  name: "John",

  get propName() {
    return this.name;
  },

  set propName(value: string) {
    this.name = value;
  },
};

console.log(obj.propName); // John
obj.propName = "Brad";
console.log(obj.propName); // Brad

// 2. React의 Hook - useState, useReducer, useEffect 등
const [todo, setTodo] = useState([]);

// 3. ES2019 이후의 # 접두사를 추가해 private class 필드를 선언
class PrivateClass {
  #name;
  constructor() {
    this.#name = "Brad";
  }
}

const instance = new PrivateClass();
console.log(instance.#name); // Syntax error. #name' 속성은 프라이빗 식별자를 포함하기 때문에 'PrivateClass' 클래스 외부에서 액세스할 수 없습니다.
```

### 접미사

```tsx
// 1. suffix를 통해, 상태 표현
const CHECKOUT_REQUEST = 'CHECKOUT_REQUEST';
const CHECKOUT_SUCCESS = 'CHECKOUT_SUCCESS';
const CHECKOUT_FAILURE = 'CHECKOUT_FAILURE';

// 2. 클래스명 정의 시 suffix를 통해 depth 표현
<nav class="navbar">
	<ul class="navbar-list">
		<li class="navbar-list__item">
		<li class="navbar-list__item">
	</ul>
</nav>
```

### 5. 매개변수의 순서

**호출하는 함수의 네이밍과 인자의 순서 간의 연관성을 고려**한다.

1. 매개변수는 **2개**가 넘지 않도록 만든다.
2. 불규칙적인 매개변수가 들어온다면, `arguments`나 `rest parameter` 사용을 고려한다.
3. 매개변수를 `객체`에 담아서 넘긴다. 매개변수가 객체로 넘어가면 순서는 상관이 없게 된다.
4. 이미 만든 함수가 있고 이 함수를 고치기 어렵다면, `Wrapping하는 함수`를 하나 만든다.

```tsx
// 불규칙적인 매개변수라면 arguemnts나 rest parameter 사용
const myConcat = (...seperator: string[]) => {
  const args = Array.prototype.slice.call(seperator, 1);
  return args.join(...seperator);
};

const colors = myConcat(", ", "red", "orange", "blue");
console.log(colors); // [red, orange, blue]

// 매개변수를 객체에 담아서 넘기기
const user = {
  name: "Brad",
  age: 26,
  handsome: true,
};

const exampleObjArgs = (user: User) => {
  // some code...
};

// or

const exampleObjArgs = ({ name, age, handsome }: User) => {
  // some code...
};

// 기존 함수를 고치기 힘들때, Wrapping 하는 함수 만들기
const someFunc = (args1, args2, args3, args4) => {
  // ...
};

// 그 함수를 또 호출하는 형태의 새 함수를 만들어서 2개 이하의 매개변수만 받기
const getFunc = (args1, args2) => {
  someFunc(args1, undefined, undefined, args2);
};
```

**[⬆ back to top](#목차)**
