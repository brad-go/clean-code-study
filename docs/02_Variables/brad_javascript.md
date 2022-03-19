# 2장 의미있는 이름 (Variables)

생성일: 2022년 3월 16일 오후 9:06

소프트웨어의 이름은 어디나 쓰인다. 우리는 변수에도 이름을 붙이고, 함수에도 이름을 붙이고, 인수와 클래스와 패키지에도 이름을 붙인다. 이름은 어디에나 쓰이고 많이 사용된다. 그러므로 이름을 잘 지으면 여러모로 편하다. 이 장에서는 이름을 잘 짓는 간단한 규칙을 몇 가지 소개한다.

## 의도를 분명히 밝혀라

- **독자가 이름을 통해 코드의 의도를 알 수 있게 지어라.**
- 좋은 이름을 지으려면 시간이 걸리지만 좋은 이름으로 절약하는 시간이 훨씬 더 많다.
- 변수나 함수 그리고 클래스 이름은 다음과 같은 질문에 답할 수 있어야 한다. 따로 주석이 필요하다면 의도를 분명히 드러내지 못했다는 말이다.
  - 존재 이유는?
  - 수행 기능은?
  - 사용 방법은?
- 의도가 드러나는 이름을 사용하면 코드 이해와 변경이 쉬워진다.
- **코드 맥락**이 코드 자체에 **명시적으로 드러날 수 있도록 작성**한다.

### Bad:

```tsx
const yyyymmdstr: Date = moment().format("YYYY/MM/DD");
```

### Good:

```tsx
const currentDate: Date = moment().format("YYYY/MM/DD");
```

## 그릇된 정보를 피하라

- 프로그래머는 코드에 그릇된 단서를 남겨서는 안 된다. **그릇된 단서는 코드의 의미를 흐린다.**
- 널리 쓰이는 의미가 있는 단어를 다른 의미로 사용하지 않는다.
- 서로 흡사한 이름을 사용하지 않도록 주의한다.
- 유사한 개념은 유사한 표기법을 사용한다. 이것도 정보이며, **일관성이 떨어지는 표기법은 그릇된 정보**다.

### Bad:

```tsx
function getUserInfo(): User;
function getUserDetails(): User;
function getUserData(): User;
```

### Good:

```tsx
function getUser(): User;
```

## 의미 있게 구분하라

- 연속된 숫자를 덧붙이거나 불용어를 추가하는 방식은 적절하지 못하다.
- 이름이 달라야 한다면 의미도 달라져야 한다.
- 읽는 사람이 차이를 알도록 이름을 지어라.

### Bad:

```tsx
const copyChars = (a1: string[], a2: string[]): void => {
  for (let i = 0; i < a1.length; i++) {
    a2[i] = a1[i];
  }
};
```

### Good:

```tsx
const copyChars = (source: string[], destination: string[]): void => {
  for (let i = 0; i < source.length; i++) {
    destination[i] = source[i];
  }
};
```

## 발음하기 쉬운 이름을 사용하라

- 발음하기 어려운 이름은 토론하기도 어렵다.
- 발음하기 쉬운 이름을 통해 지적인 대화가 가능해진다.

### Bad:

```tsx
interface DtaRcrd102 {
  genymdhms: Date;
  modymdhms: Date;
  pszqint: number;
}
```

### Good:

```tsx
interface CustomerType {
  generationTimestamp: Date;
  modificationTimestamp: Date;
  recordId: number;
}
```

## 검색하기 쉬운 이름을 사용하라

- 문자 하나를 사용하는 이름과 상수는 텍스트 코드에서 쉽게 눈에 띄지 않는다.
- 이름 길이는 범위 크기에 비례해야 한다.
- 변수나 상수를 코드 여러 곳에서 사용한다면 검색하기 쉬운 이름이 바람직하다.
- 검색하기 쉬운 이름은 길어질지라도, 디버깅과 유지 보수에 용이하다.
- 우리는 코드를 작성하는 것보다 읽는 것에 시간을 더 많이 소비한다. 읽기 쉽고, 검색하기 쉽게 코드를 작성하는 것은 매우 중요하다.

### Bad:

```tsx
// What the heck is 86400000 for?
setTimeout(restart, 86400000);
```

### Good:

```tsx
// Declare the as capitalized named constants.
const MILLISECONDS_PER_DAY: number = 60 * 60 * 24 * 10000; // 86400000

setTimeout(restart, MILLISECONDS_PER_DAY);
```

## 인코딩을 피하라

- 문제 해결에 집중하는 개발자에게 인코딩은 불필요한 정신적 부담이다. 인코딩한 이름은 거의 발음하기 어려우며 오타가 생기기도 쉽다.
- 언어의 발전과 IDE, 컴파일러의 발전을 통해 더이상 [헝가리식 표기법](https://namu.wiki/w/%ED%97%9D%EA%B0%80%EB%A6%AC%EC%95%88%20%ED%91%9C%EA%B8%B0%EB%B2%95)이나 기타 인코딩 방식을 사용할 필요가 없어졌다.
- 멤버 변수에 m\_이라는 접두어를 붙일 필요도 없다. **클래스와 함수는 접두어가 필요없을 정도로 작아야 마땅하다.**

### Bad:

```tsx
class Part {
  m_dsc = null;
  constructor(m_dsc) {
    this.m_dsc = m_dsc;
  }
  setName(name: string): void {
    this.m_dsc = name;
  }
}
```

### Good:

```tsx
class Part {
  description: string;
  constructor(desription: string) {
    this.description = desription;
  }
  setDescription(description: string): void {
    this.description = description;
  }
}
```

## 자신의 기억력을 자랑하지 마라

- 독자가 코드를 읽으면서 변수 이름을 자신이 아는 이름으로 변환해야 한다면 그 변수 이름은 바람직하지 못하다.
- 암시된 것보다 명시적인 것이 낫다. **명료함**이 최고다.
- 자신의 용어로 이름을 짓지말고 남들이 이해하는 코드를 작성한다.

### Bad:

```tsx
const u = getUser();
const s = getSubscription();
const t = charge(u, s);
```

### Good:

```tsx
const user = getUser();
const subscription = getSubscription();
const transaction = charge(user, subscription);
```

## 클래스 이름

- 클래스 이름과 객체 이름은 명사나 명사구가 적합하다.
- 동사는 사용하지 않는다.

### Bad:

```tsx
const Data = {
  id: 1,
  name: "Brad",
};
```

### Good:

```tsx
const Customer = {
  id: 1,
  name: "Brad",
};
```

## 메서드 이름

- 메서드 이름은 동사나 동사구가 적합하다.
- 접근자, 변경자, 조건자는 앞에 get, set, is를 붙인다.
- 생성자를 중복정의(overload)할 때는 정적 팩토리 메서드를 사용한다.

### Bad:

```tsx
class Diary {
  name: string;
  date: Date;
  constructor(name: string, date: Date) {
    this.name = name;
    this.date = date;
  }
}

const diary = new Diary("Today's diary", new Date());
```

### Good:

```tsx
class Diary {
  name: string;
  date: Date;
  constructor(name: string, date: Date) {
    this.name = name;
    this.date = date;
  }
  static createDiary() {
    return new this("Today's diary", new Date());
  }
}

const diary = Diary.createDiary();
```

## 기발한 이름은 피하라

- 재미난 이름보다 명료한 이름을 선택하라
- 의도를 분명하고 솔직하게 표현하라

## 한 개념에 한 단어를 사용하라

- 추상적인 개념 하나에 단어 하나를 선택해 이를 고수한다.
  - 예를 들어 똑같은 메서드를 클래스마다 fetch, retrieve, get으로 제각각 부르면 혼란스럽다.
- 메서드 이름은 독자적이고 일관적이어야 한다.
  - 그래야 주석을 뒤져보지 않고도 프로그래머가 올바른 메서드를 선택한다.
- **일관성 있는 어휘**를 사용한다.

## 말장난을 하지마라

- 한 단어를 두 가지 목적으로 사용하지 마라.
  - 다른 개념에 같은 단어를 사용한다면 그것은 말장난에 불과하다.
- 맥락이 다른 것을 비슷하다는 이유로 같은 이름으로 짓지 않는다.
- 집중적인 탐구가 필요한 코드가 아니라 대충 훑어봐도 이해할 코드를 작성한다.

## 해법 영역에서 가져온 이름을 사용하라

- 코드를 읽을 사람도 프로그래머다. 전산 용어, 알고리즘 이름, 패턴 이름, 수학 용어 등을 사용해도 괜찮다.
- 모든 이름을 문제 영역에서 가져오는 방법은 현명하지 못하다.
- **기술 개념에는 기술 이름**이 가장 적합한 선택이다.

## 문제 영역에서 가져온 이름을 사용하라

- 적절한 프로그래밍 용어가 없다면 문제 영역에서 이름을 가져온다.
- 문제 영역 개념과 관련이 깊은 코드라면 문제 영역에서 이름을 가져와야 한다.

## 의미 있는 맥락을 추가하라

- 클래스, 함수, 이름 공간에 넣어 맥락을 부여한다.
- 모든 방법이 실패하면 마지막 수단으로 접두어를 붙인다.
- 맥락을 개선하면 함수를 쪼개기가 쉬워지므로 알고리즘도 좀 더 명확해진다.

### Bad:

```tsx
const printGuessStatistics = (candidate: string, count: number) => {
  let number: string;
  let verb: string;
  let pluralModifier: string;

  if (count === 0) {
    number = "no";
    verb = "are";
    pluralModifier = "s";
  } else if (count === 1) {
    number = "1";
    verb = "is";
    pluralModifier = "";
  } else {
    number = count.toString();
    verb = "are";
    pluralModifier = "s";
  }

  const guessMessage = `There ${verb} ${number} ${candidate} ${pluralModifier}`;
  console.log(guessMessage);
};
```

### Good:

```tsx
class GuessStatisticsMessage {
  number?: string;
  verb?: string;
  pluralModifier?: string;

  constructor(number?: string, verb?: string, pluralModifier?: string) {
    this.number = number;
    this.verb = verb;
    this.pluralModifier = pluralModifier;
  }

  make(candidate: string, count: number) {
    this.createPluralDependentMessageParts(count);
    return `There ${this.verb} ${this.number} ${candidate} ${this.pluralModifier}`;
  }

  createPluralDependentMessageParts(count: number) {
    if (count === 0) {
      this.thereAreNoLetters();
    } else if (count === 1) {
      this.thereIsOneLetter();
    } else {
      this.thereAreManyLetters(count);
    }
  }

  thereAreManyLetters(count: number) {
    this.number = count.toString();
    this.verb = "are";
    this.pluralModifier = "s";
  }

  thereIsOneLetter() {
    this.number = "1";
    this.verb = "is";
    this.pluralModifier = "";
  }

  thereAreNoLetters() {
    this.number = "no";
    this.verb = "are";
    this.pluralModifier = "s";
  }
}

const message = new GuessStatisticsMessage();
console.log(message.make("Brad", 4));
```

## 불필요한 맥락을 없애라

- 일반적으로 짧은 이름이 긴 이름보다 좋다. (의미가 분명한 경우에 한해)
- 클래스, 타입, 객체의 이름으로 알 수 있는 것이 있다면, 변수 이름으로 반복하지 않는다.

### Bad:

```tsx
type Car = {
  carMake: string;
  carModel: string;
  carColor: string;
};

function print(car: Car): void {
  console.log(`${car.carMake} ${car.carModel} (${car.carColor})`);
}
```

### Good:

```tsx
type Car = {
  make: string;
  model: string;
  color: string;
};

function print(car: Car): void {
  console.log(`${car.make} ${car.model} (${car.color})`);
}
```

## 단락 평가 또는 조건 대신 기본 인수를 사용하라

- 기본 인수는 종종 단락 평가보다 깔끔하다.
- 기본 인수를 사용할 경우, 함수에서 `undefined`에 한해 기본 인수가 사용된다는 것을 인지하자.
- 다른 거짓으로 분류되는 값들(`’’`, `“”`, `false`, `null`, `0`, `NaN`)은 기본 인수로 대체되지 않는다.

### Bad:

```tsx
function loadPages(count?: number) {
  const loadCount = count !== undefined ? count : 10;
  // ...
}
```

### Good:

```tsx
function loadPages(count: number = 10) {
  // ...
}
```

## Enum을 사용하여 의도를 문서화하라

- Enums(열거형)은 코드의 의도를 문서화하는데 도움이 된다.
- 예를 들어 정확한 값들보다는 다른 값들이 되어버릴 것 같은 우려가 있는 경우 사용할 수 있다.

### Bad:

```tsx
const GENRE = {
  ROMANTIC: "romantic",
  DRAMA: "drama",
  COMEDY: "comedy",
  DOCUMENTARY: "documentary",
};

projector.configureFilm(GENRE.COMEDY);

class Projector {
  // declaration of Projector
  configureFilm(genre) {
    switch (genre) {
      case GENRE.ROMANTIC:
      // some logic to be executed
    }
  }
}
```

### Good:

```tsx
enum GENRE {
  ROMANTIC,
  DRAMA,
  COMEDY,
  DOCUMENTARY,
}

projector.configureFilm(GENRE.COMEDY);

class Projector {
  // declaration of Projector
  configureFilm(genre) {
    switch (genre) {
      case GENRE.ROMANTIC:
      // some logic to be executed
    }
  }
}
```

## 퀴즈

### 1. 발음하기 쉬운 이름을 사용하라

이 코드를 고친다면?

```tsx
interface DtaRcrd102Type {
  genymdhms: Date;
  modymdhms: Date;
  pszqint: string;
}

const genymdhms = (): Date => {
  const date = new Date();
  return date;
};

const modymdhms = (): Date => {
  const date = new Date();
  return date;
};

const DtaRcrd102: DtaRcrd102Type = {
  genymdhms: genymdhms(),
  modymdhms: modymdhms(),
  pszqint: "102",
};

console.log(DtaRcrd102);
```

<details><summary><b>정답</b></summary><div markdown="1">

```tsx
interface CustomerType {
  generationTimestamp: Date;
  modificationTimestamp: Date;
  recordId: string;
}

const generateTimestamp = (): Date => {
  const date = new Date();
  return date;
};

const modifyTimestamp = (): Date => {
  const date = new Date();
  return date;
};

const Customer: CustomerType = {
  generationTimestamp: generateTimestamp(),
  modificationTimestamp: modifyTimestamp(),
  recordId: "102",
};

console.log(Customer);
```

</div></details>
    


### 2. 의도를 분명히 밝혀라

변수나 함수 그리고 클래스 이름이 주석없이 답할 수 있어야 하는 세가지 질문은?

<details><summary><b>정답</b></summary><div markdown="1">

- 존재 이유
- 수행 기능
- 사용 방법

</div></details>
