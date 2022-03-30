# 주석

주석을 사용한다는 것은 주석없이 코드를 표현하지 못한다는 표시이다. 코드가 유일한 진실이어야 한다.

> 나쁜 코드에 주석을 달지 말고, 코드를 다시 작성해라.
> —_Brian W. Kernighan and P. J. Plaugher_

## 목차

- [주석 대신에 코드로 의도를 표현해라](#주석-대신에-코드로-의도를-표현해라)
- [법적인 주석](#법적인-주석)
- [정보를 제공하는 주석](#정보를-제공하는-주석)
- [나중에 개선할 사항이 있다면 TODO 주석을 사용하라](#나중에-개선할-사항이-있다면-todo-주석을-사용하라)
- [같은 이야기를 중복하는 주석을 피하라](#같은-이야기를-중복하는-주석을-피하라)
- [이력을 기록하는 주석을 달지 마라](#이력을-기록하는-주석을-달지-마라)
- [위치를 표시하는 주석을 피하라](#위치를-표시하는-주석을-피하라)
- [닫는 괄호에 다는 주석을 피하라](#닫는-괄호에-다는-주석을-피하라)
- [공로를 돌리거나 저자를 표시하는 주석](#공로를-돌리거나-저자를-표시하는-주석)
- [주석처리한 코드를 남기지 마라](#주석처리한-코드를-남기지-마라)
- [전역 정보, 너무 많은 정보, 모호한 관계](#그-외)

## 주석 대신에 코드로 의도를 표현해라

주석을 단다는 것은 양해를 구해야 하는 것이지 요구 사항이 아니다. 좋은 코드는 대부분 그 자체로 문서가 된다. 주석으로 달려는 설명을 함수나 변수로 만들어 표현하자.

### Bad:

```tsx
// 구독이 활성화 되어있는지 확인한다.
if (subscription.endDate > Date.now()) {
}
```

### Good:

```tsx
const isSubscriptionActive = subscription.endDate > Date.now();
if (isSubscriptionActive) {
}
```

**[⬆ Back to top](#목차)**

## 법적인 주석

각 소스 파일 첫머리에 주석으로 들어가는 저작권 정보와 소유권 정보는 필요하고도 타당하다.

```tsx
// Copyright (C) 2003, 2004, 2005 by Object Mentor, Inc. All rights reserved.
// GNU General Public License 버전 2 이상을 따르는 조건으로 배포한다.
```

**[⬆ Back to top](#목차)**

## 정보를 제공하는 주석

기본적인 정보를 주석으로 제공하면 편리하다. 이해하기 힘든 정규식 같은 경우 반환값의 형식을 표현해주면 좋다.

```tsx
// kk:mm:ss EEE, MMM dd, yyyy 형식이다.
const timeMatcher: RegExp = new RegExp("\\d*:\\d*:\\d* \\w*");
```

**[⬆ Back to top](#목차)**

## 나중에 개선할 사항이 있다면 TODO 주석을 사용하라

때때로 나중에 개선할 사항이 있다면 `// TODO` 주석을 사용하여 코드에 메모를 남겨두면 편하다. 대부분의 IDE는 이러한 종류의 주석을 특별히 지원하므로 전체 작업 목록을 빠르게 검토할 수 있다.

하지만 TODO 주석은 악성 코드의 변명이 되지 않는다. 주기적으로 TODO 주석을 점검해 없애도 괜찮은 주석은 없애도록 하자.

### Bad:

```tsx
const getActiveSubscriptions = (): Promise<Subscription[]> => {
  // dueDate가 인덱스에 들어있어야 한다.
  return db.subscriptions.find({ dueDate: { $lte: new Date() } });
};
```

### Good:

```tsx
const getActiveSubscriptions = (): Promise<Subscription[]> => {
  // TODO: dueDate가 인덱스에 들어있어야 한다.
  return db.subscriptions.find({ dueDate: { $lte: new Date() } });
};
```

**[⬆ Back to top](#목차)**

## 같은 이야기를 중복하는 주석을 피하라

긴 함수나 클래스가 있다면, 주석을 통해 함수나 클래스를 설명하고 싶을 수도 있다. 하지만 이러한 주석은 독자가 함수나 클래스를 대충 이해하고 넘어가게 만든다. 또, 자칫하면 코드보다 주석을 읽는 시간이 더 오래 걸릴 수도 있다.

### Bad:

```tsx
// 서버에 문제가 없때 데이터를 받아서 json() 형식으로 반환한다.
// 받아온 데이터에 문제가 있다면 에러를 던진다.
const fetchAPI = async (url: string) => {
  try {
    const res = await fetch(url);
    if (!res.ok) throw new Error("데이터에 문제가 생겼습니다.");
    return await res.json();
  } catch (e) {
    const err = e as Error;
    throw new Error(`서버에 문제가 생겼습니다. ${err.message}`);
  }
};
```

### Good:

```tsx
const fetchAPI = async (url: string) => {
  try {
    const res = await fetch(url);
    if (!res.ok) throw new Error("데이터를 불러오지 못했습니다.");
    return await res.json();
  } catch (e) {
    const err = e as Error;
    throw new Error(`서버에 문제가 생겼습니다. ${err.message}`);
  }
};
```

**[⬆ Back to top](#목차)**

## 이력을 기록하는 주석을 달지 마라

버전 관리 시스템을 사용하는 것을 기억해라. 사용하지 않는 코드, 주석 처리한 코드, 특히 변경 이력을 기록한 주석은 필요없다. 기록을 보려면 `git log`를 사용하자.

### Bad:

```tsx
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Added type-checking (LI)
 * 2015-03-14: Implemented combine (JR)
 */
const combine = (a: number, b: number): number => {
  return a + b;
};
```

### Good

```tsx
const combine = (a: number, b: number): number => {
  return a + b;
};
```

**[⬆ Back to top](#목차)**

## 위치를 표시하는 주석을 피하라

일반적으로 이러한 주석은 가독성만 낮추게된다. 적절한 들여쓰기와 포맷을 지킨 함수와 변수 이름은 코드에 시각적인 구조를 부여한다. 대부분의 IDE는 코드 블록을 접었다 펼 수 있는 기능을 지원한다.

### Bad:

```tsx
////////////////////// Client class /////////////////////
class Client {
  id: number;
  name: string;
  address: Address;
  contact: Contact;

  ////////////////////// public methods /////////////////////
  public describe(): string {
    // ...
  }

  ////////////////////// private methods /////////////////////
  private describeAddress(): string {
    // ...
  }

  private describeContact(): string {
    // ...
  }
}
```

### Good:

```tsx
class Client {
  id: number;
  name: string;
  address: Address;
  contact: Contact;

  public describe(): string {
    // ...
  }

  private describeAddress(): string {
    // ...
  }

  private describeContact(): string {
    // ...
  }
}
```

**[⬆ Back to top](#목차)**

## 닫는 괄호에 다는 주석을 피하라

중첩이 심하고 장황한 함수라면 의미가 있을지 모르지만, 작고 캡슐화된 함수에는 잡음일 뿐이다. 닫는 괄호에 주석을 달아야 겠다는 생각이 든다면 함수를 리팩토링하자.

### Bad:

```tsx
const main = (args: string[]) => {
  const input = require("fs").readFileSync("input.txt").toString().trim();
  let line: string;
  let lineCount = 0;
  let charCount = 0;
  let wordCount = 0;
  try {
    for (let i = 0; i < input.length; i++) {
      line = input[i];
      lineCount++;
      charCount += line.length;
      const words: string[] = line.split("\\W");
      wordCount += words.length;
    } // for
    console.log(`wordCount = ${wordCount}`);
    console.log(`lineCount = ${lineCount}`);
    console.log(`charCount = ${charCount}`);
  } catch (e) {
    // try
    const err = e as Error;
    throw new Error(`Error: ${err.message}`);
  } // catch
};
```

**[⬆ Back to top](#목차)**

## 공로를 돌리거나 저자를 표시하는 주석

기본적으로 버전 관리 시스템을 이용하면 누가 언제 무엇을 추가했는지 기록된다. 저자 이름으로 코드를 오염시킬 필요가 없다. 정 달고 싶다면, package.json에서 author를 표시하자.

**[⬆ Back to top](#목차)**

## 주석처리한 코드를 남기지 마라

버전 관리 시스템(ex. git)이 우리를 대신해 이전 코드를 기억해준다. 주석으로 코드를 남겨둘 필요가 없다. 그냥 코드를 삭제해라!

### Bad:

```tsx
type User = {
  name: string;
  email: string;
  // age: number;
  // jobPosition: string;
};
```

### Good:

```tsx
type User = {
  name: string;
  email: string;
};
```

**[⬆ Back to top](#목차)**

## 그 외

### 전역 정보

주석을 달아야 한다면 근처에 있는 코드만 주석을 달아라. 바로 아래 코드가 아닌 코드 어딘가의 다른 곳을 찾아봐야 한다면 문제가 있는 것이다.

### 너무 많은 정보

주석에다 흥미로운 역사나 관련 없는 정보를 장황하게 늘어놓지 마라.

### 모호한 관계

주석과 주석이 설명하는 코드는 둘 사이의 관계가 명확해야 한다. 이왕 달거면 코드를 명확히 이해할 수 있도록 제대로된 정보를 제공한다.

**[⬆ Back to top](#목차)**
