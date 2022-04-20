# 7장 오류 처리

발생된 에러는 좋은 것이다. 런타임에서 프로그램에서 문제가 발생한 경우를 성공적으로 확인했으며, 현재 스택에서 함수 실행을 중지하고, (노드에서) 프로세스를 중지하고, 콘솔을 사용한 스택 추적을 통해 사용자에게 알려줄 수 있다는 것이다.

이렇게 에러 처리는 프로그램에 반드시 필요한 요소이며 중요하다. 프로그램에서 뭔가가 잘못될 가능성은 늘 존재하기 때문이다. 하지만 오류 처리가 코드가 여기저기 흩어져 있어 프로그램 논리를 이해하기 어려워진다면 클린 코드라 부를 수 없다.

## 목차

1. [자바스크립트의 예외처리](#자바스크립트의-예외처리)
   1-1. [기본 에러 처리](#기본-에러-처리)
   1-2. [임의로 에러 발생시키기](#임의로-에러-발생시키기)
   1-3. [에러가 발생되도 항상 실행되는 코드 짜기](#finally로-에러가-발생되도-실행되는-코드-만들기)
   1-4. [에러 객체](#error-객체)
   1-5. [에러 타입](#error-서브-타입)
2. [항상 Error를 사용해라](#항상-error를-사용해라-throw-또는-reject-할-때에)
3. [발견된 오류를 무시하지마라](#발견된-오류를-무시하지마라)
   3-1. [winston 라이브러리 사용법](#L258)
4. [요청이 거분된 프로미스들을 무시하지 마라](#요청이-거부된-프로미스들을-무시하지-마라)
5. [커스텀 에러 클래스와 확장](#커스텀-에러-클래스와-에러-확장)
6. [에러 유형 판별하기](#에러-유형-확인-시에-instanceof를-사용하라)
7. [예외감싸기](#호출자를-고려해-예외-클래스를-정의하라-예외-감싸기)
8. [요약](#요약)

## 자바스크립트의 예외처리

자바스크립트에도 다른 언어들에 준하는 런타임 예외처리 구문이 있다. 짧은 코드를 작성할 때는 이런 런타임 에러들이 큰 문제가 안되기도 하고, 쉽게 해결할 수 있다.

하지만 자바스크립트 코드가 길어지고 로직이 복잡해지면서 파일이나 네트워크 연결을 제어하는 수준이 되면 무수히 많은 에러와 예외 상황이 개발자를 괴롭히게 된다.

자바스크립트의 에러 핸들링(오류 처리)은 이런 예외 또는 에러 상황을 매끄럽고 부드럽게 처리할 수 있게 해준다. **의미를 알기 힘든 알 수 없는 기술 용어로 범벅된 에러 메시지보다 코드를 작성한 개발자가 이해하고 구분할 수 있는 메시지가 훨신 도움이 될 수 있다.**

### 기본 에러 처리

```tsx
try {
  // 예외를 발생시킬 수 있는 코드
} catch (error) {
  // 인자로 에러(Error) 객체가 넘어옴
  // 에러가 발생했을 때 처리하는 코드
}
```

위는 기본적인 예외처리를 하는 구문이다. 그러나 `try{}` 구문만으로는 예외가 발생했을 때 할 수 있는 처리는 없다. `try{}` **구문은 예외가 발생하는지만을 체크한다**. 에러가 발생했을 때 처리하는 코드는 `catch(error){}` 구문으로 작성한다. 이는 단독으로 사용할 수 없고 반드시 `try{}` 구문과 함께 사용해야한다. (`if ~ else` 구문과 비슷하다고 볼 수 있다)

**에러가 발생하면 에러 메시지를 표시하고 코드 실행이 종료되지 않고, 코드 실행이 `catch(){}` 구문으로 넘어오고 그 안의 코드가 실행된다.**

```tsx
readFile();
console.log("end!");
```

위와 같이 정의되지 않은 함수 `readFile()`을 실행시키면 “readFile is not defined”라는 참조 에러가 발생한다. **코드는 에러가 발생한 위치에서 더 이상 실행되지 않고 종료된다!**

하지만 `try/catch` 구문을 사용해서 에러 처리를 한다면 코드가 실행 완료되게 할 수 있다.

```tsx
try {
  readFile();
} catch (e) {
  // 오류 발생 시에 정상 흐름을 제공할 수 있다.
  console.error(e);
}
console.log("end!");
```

이렇게 작성하고 실행해보면 에러에 대해 알려주고 “end!” 까지 출력되는 것을 볼 수 있을 것이다. 정의되지 않은 `readFile()` 함수를 호출하면서 에러가 발생하고, 에러를 캐치한 `try` 구문은 실행 제어를 catch 구문으로 넘긴다. 콘솔에 에러가 출력되고 `catch` 구문에서 **정상 처리해서 종료했기 때문에 다음 코드는 정상 실행**된다!

### 임의로 에러 발생시키기

에러가 발생하지 않아도 임의로 에러를 발생시킬 수 있다. 예를 들어 조건에 맞지 않는 값이 들어왔을 경우 에러를 발생시키고 에러 처리를 하도록 해서 이후에 발생할 에러를 미리 막을 수 있다.

적절하게 에러를 발생시키고 에러 처리를 하는 것은 간결하고 유지보수가 쉬운, 그리고 코드가 더 안정적으로 실행되도록 하는 권장되는 코딩 방법이다.

자바스크립트에는 예외 처리용으로 사용할 수 있는 `Error` 클래스가 있다. 오류를 던질 때는 `throw` 키워드를 사용한다. 그 오류는 `try/catch` 블럭으로 잡을 수 있다.

```tsx
try {
  throw new Error("에러 발견!");
} catch (e) {
  console.log(e);
}
```

위와 같이 작성하면 에러가 발생한다. 물론 **에러 핸들링을 하지 않으면 에러를 발생시킨 지점에서 코드 실행이 멈추게 된다.**

### finally로 에러가 발생되도 실행되는 코드 만들기

그렇다면 의문을 가질 수 있다. 예외처리를 해서 에러가 발생해도 에러가 어디서 난지 알려주고 다음 코드가 실행되기 원할 수 있다. 그럴 때 `finally` 구문을 사용할 수 있다. 이 또한 `catch`와 같이 단독으로 사용할 수 없으며, `try` 구문의 실행 코드 영역과 `catch` 구문의 실행 코드 영역 어느 쪽이 실행돼도, 마지막에 무조건 실행되어야 하는 코드를 `finally` 구문에 추가할 수 있다.

```tsx
try {
  // 실행 코드
} finally {
  // 항상 실행
}

// catch 구문은 없을 수 있다.

try {
  // 실행 코드
} catch (error) {
  // 에러 처리
} finally {
  // 항상 실행
}
```

### Error 객체

```tsx
class Error {
  constructor(message) {
    this.message = message;
    this.name = 'Error';
    this.stack = <call stack>;
  }
}
```

에러 객체는 다음과 같은 프로퍼티를 가진다.

- `message`: 사람이 읽을 수 있는 형태의 에러 메시지
- `name`: 에러 이름을 담은 문자열(에러 생성자 이름)
- `stack`: 표준이 아니지만 대부분의 런타임이 지원하는 프로퍼티로 에러가 발생한 순간의 스택을 나타낸다.

### Error 서브 타입

내장된 `Error` 클래스에 더하여 `Error`를 상속하는 내장 오류 클래스가 몇개 더 있고, 자바스크립트 런타임이 이러한 오류를 던질 수 있다.

**RangeError**

숫자 변수 또는 파라미터가 유효한 범위를 벗어난 경우를 나타내는 인스턴스를 생성

```tsx
// 너무 많은 수의 인자로 콘솔 호출
console.log.apply(console, new Array(1000000000)); // RangeError: Invalid array length;
```

**ReferenceError**

유효하지 않은 참조 값을 참조(de-referencing)하여 오류가 발생했음을 나타내는 인스턴스를 생성

```tsx
"use strict";
console.log(notValidVar); // ReferenceError: notValidVar is not defined
```

**SyntaxError**

유효하지 않은 자바스크립트 코드를 파싱하여 구문 오류가 발생했음을 나타내는 인스턴스 생성

```tsx
1***3; // SyntaxError: Unexpected token '*'
```

**TypeError**

변수 또는 파라미터가 유효하지 않은 타입인 경우일 때 발생하는 오류를 나타내는 인스턴스 생성

```tsx
"1.2".toPrecision(1); // TypeError: "1.2".toPrecision is not a function
```

**URIError**

`encodeURI()` 또는 `decodeURI()`에 유효하지 않은 파라미터가 전달되었을 때 발생하는 오류를 나타내는 인스턴스 생성

```tsx
decodeURI("%"); // URIError: URI malformed
```

**[⬆ back to top](#목차)**

## 항상 Error를 사용해라 (throw 또는 reject 할 때에)

자바스크립트와 타입스크립트에서는 어떤 객체든 `throw` 할 수 있다. 또한 `Promise`는 어떠한 이유를 나타내는 객체와 함께 `rejected`(거부)될 수 있다.

오류 유형(에러 타입)과 함께 `throw` 문법을 사용하는 것이 좋다. 이는 오류가 `catch` 구문이 있는 상위 레벨 코드에서 발견될 수 있기 때문이다. 여기서 문자열 메시지를 찾는 것은 매우 혼란스럽고 [**디버깅을 더 힘들게 한다.**](https://basarat.gitbook.io/typescript/type-system/exceptions#always-use-error) 같은 이유로 `Promise`도 오류 유형(에러 타입)과 함께 `reject`(거부)해야 한다.

`Error` **객체의 근본적인 편의성은 자신이 어디서 만들어졌고, 발생했는지에 대해 `stack` 프로퍼티로 자동으로 기록을 유지해준다는 점**이다.

### Bad:

```tsx
const calculateTotal = (itmes: Item[]): number => {
  throw "Not implemented.";
};

const get = (): Promise<Item[]> => {
  return Promise.reject("Not implemented.");
};
```

이와 같이 생 문자열을 던지는 것은 정말 고통스러운 디버깅을 경험하게 할 수 있고, 로그를 보고 오류를 분석하는 일을 힘들게 만든다.

### Good:

```tsx
const calculateTotal = (items: Item[]): number => {
  throw new Error("Not implemented.");
};

const get = (): Promise<Item[]> => {
  return Promise.reject(new Error("Not implemented."));
};
```

**[⬆ back to top](#목차)**

## 발견된 오류를 무시하지마라

오류가 발견되어도 아무 조치도 취하지 않으면 해당 오류를 수정하거나 대처할 수 없게 된다. **콘솔에 에러를 기록하는 것은 종종 콘솔에 출력된 수많은 것들 사이에서 에러를 찾을 없을 수 있으므로 별로 나은 방법이 아니다.**

`try/catch`문을 이용해 일정 코드를 감싸는 것은 에러가 그곳에서 발생할 것이고, 에러가 일어났을 때 그에 대한 계획이나 코드 경로를 가지고 있다는 의미이므로 `**try/catch`문으로 에러가 발생할 곳을 감싸주자!\*\*

### Bad:

```tsx
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}

// 더 나쁠 수도 있는 코드

try {
  functionThatMightThrow();
} catch (error) {
  // 에러 무시
}
```

### Good:

```tsx
try {
  functionThatMightThrow();
} catch (error) {
  console.error(error);
  // 다른 방식:
  notifyUserOfError(error);
  // 다른 방식:
  reportErrorToService(error);
  // 세개 모두 다 사용하기!
}
```

### Good:

```tsx
import { logger } from './logger;

try {
  functionThatMightThrow();
} catch (error) {
  logger.error(error);
}
```

`winston` 라이브러리를 사용한다면 위와 같이 작성할 수 있다. 서버의 동작을 파악하거나 오류를 찾아야 할 때 로그파일을 확인하는 용도로 아주 유용하다!

<details><summary><b>winston 사용 방법 알아보기</b></summary><div markdown="1">
<br />

우선 몇가지 라이브러리를 설치해야 한다.

```tsx
npm i winston winstonDaily @types/winston
```

자바스크립트로 한다면 `@types/winston`은 설치하지 않아도 된다. 그리고 다음과 같이 winston을 설정할 파일을 만들어준다.

```tsx
// logger.ts

import winston from "winston";
import winstonDaily from "winston-daily-rotate-file";

const logDir = "logs";
const { printf, timestamp, combine } = winston.format;

// 출력할 로그 포맷 정의하기
const logFormat = printf(
  (info) => `${info.timestamp} - ${info.level}: ${info.message}`
);

// 로그 레벨
// error: 0, warn: 1, info: 2, http: 3, verbose: 4, debug: 5, silly: 6
// 숫자가 낮을 수록 priority가 높다.
// 레벨을 설정하면 해당 레벨 이상의 priority를 가지는 (즉, 숫자가 같거나 낮은) 로그를 출력하게 된다.

// logger 생성
const logger = winston.createLogger({
  format: combine(timestamp({ format: "YYYY-MM-DD HH:mm:ss" }), logFormat),
  transports: [
    // info 레벨 로그를 저장할 파일 설정
    new winstonDaily({
      // level: "info",
      datePattern: "YYYY-MM-DD",
      dirname: logDir,
      filename: `%DATE%.log`,
      maxFiles: 30,
      zippedArchive: true,
    }),
    // error 레벨 로그를 저장할 파일 설정
    new winstonDaily({
      // level: "error",
      datePattern: "YYYY-MM-DD",
      dirname: logDir + "/error", // error.log 파일은 /logs/error 하위에 저장
      filename: `%DATE%.error.log`,
      maxFiles: 30,
      zippedArchive: true,
    }),
  ],
});

// Productoin 환경이 아닌 경우 (dev 등)
// if (process.env.NODE_ENV !== 'production') {}

logger.add(
  new winston.transports.Console({
    format: winston.format.combine(
      winston.format.colorize(), // 색깔 넣어서 출력
      winston.format.simple() // `${info.timestamp} - ${info.level}: ${info.message}`
    ),
  })
);

// 잘 출력되나 확인해보기
// logger.info("Hello world!");

export { logger };
```

자바스크립트라면 `import` 대신 `require`를 사용하고 `export.module`을 사용해서 코드를 작성할 수 있다.

```tsx
new winstonDaily({
  ...
  dirname: logDir,
  filename: `%DATE%.log,
  ...
```

이 부분이 로그를 기록할 폴더와 파일명을 지정하는 것이다. 폴더와 파일이 존재하지 않는다면, 새로 생성된다.

이제 다 작성했다면 위의 코드를 간단히 node 명령어를 통해 실행해보자. 그러면 `logs`폴더가 생기고 그 안에 해쉬값의 이름을 가진 json파일과 지정한 파일명과 같이 날짜를 가진 .log 파일이 생성되있을 것이고 다음과 같이 기록되어 있을 것이다.

```tsx
2022-04-19 16:32:45 - info: functionThatMigthThrow is not defined
```

이렇게 `winston`을 사용해서 콘솔에 알아보기 쉽게 출력하거나 파일에 기록해 에러 로그를 파악하기 쉽게 만들 수 있다.

</div></details>
<br />

**[⬆ back to top](#목차)**

## 요청이 거부된 프로미스들을 무시하지 마라

프로미스의 거부도 아무 조치를 취하지 않으면 해당 상태를 수정하거나 대처할 수 없게 된다. 그러므로 `try/catch`를 통해 발견된 오류를 무시하면 안된다.

### Bad:

```tsx
getUser()
  .then((user: User) => {
    return sendEmail(user.email, "Welcome!");
  })
  .catch((error) => {
    console.log(error);
  });
```

### Good:

```tsx
import { logger } from "./logger";

getUser()
  .then((user: User) => {
    return sendEmail(user.email, "Welcome!");
  })
  .catch((error) => {
    logger.log(error);
  });

// async/await 구문을 사용하능 방법

try {
  const user = await getUser();
  await sendEmail(user.email, "Welcome!");
} catch (error: any) {
  logger.log(error);
}
```

**[⬆ back to top](#목차)**

## 커스텀 에러 클래스와 에러 확장

개발을 하다 보면 자체 에러 클래스가 필요한 경우가 종종 생긴다. 네트워크 관련 작업 중 에러가 발생했다면 `HttpError`, 데이터베이스 관련 작업 중 에러가 발생했다면 `DbError`, 검색 관련 작업 중 에러가 발생했다면 `NotFoundError`를 사용하는 것이 **직관적**이기 때문이다.

커스텀 에러 클래스를 만든 경우, 이 에러들은 에러 객체와 같이 `message`, `name` 가능하다면 `stack` 프로퍼티를 지원해야 한다. 이 프로퍼티들 외에 다른 프로퍼티를 지원할 수도 있다. 예를 들어 `HttpError` 클래스의 객체에 `statusCode` 프로퍼티를 만들고 `404`, `403`, `500` 같은 숫자를 값으로 지정할 수 있을 것이다.

`throw`의 인수엔 아무런 제약이 없기 때문에 커스텀 에러 클래스는 반드시 `Error`를 상속할 필요는 없다. 그러나 `Error`를 상속받아 커스텀 에러 클래스를 만들게 되면 `obj instanceof Error`를 사용해서 **에러 객체를 식별할 수 있다**! 이런 장점이 있으므로 `Error`를 상속받아 에러 객체를 만드는 것이 낫다.

```tsx
const json: string = `{ "name": "John", "age": 30 }`;
```

위와 같은 형태의 JSON 데이터를 받는 `readUser(json)` 함수가 있다고 가정해보자. 내부에선 `JSON.parse`를 이용해 객체로 변환하는데, 잘못된 형식의 `json`이 들어오면 `SyntaxError`가 발생할 것이다.

### Bad:

```tsx
const readUser = (json: string) => {
  let user = JSON.parse(json);

  return user;
};
```

필수 프로퍼티가 없거나 위 형식에 맞지 않으면 에러를 발생시킬 수 있어야 한다. 하지만 이때 발생하는 에러는 `SyntaxError`가 아니다. JSON 형식은 맞지만, 자체 기준에 맞지 않기 때문에 발생한 에러이므로 전혀 다른 종류의 에러다. 따라서 위 함수는 JSON 형식의 데이터를 읽을 수 있는 뿐만 아니라, 데이터를 **검증**할 수 있어야 한다.

### Good:

이 에러를 `ValidationError`라고 부르고, 이 에러를 위한 클래스를 만들어보자.

```tsx
class ValidationError extends Error {
  constructor(message: string) {
    super(message); // super로 부모 생성자 호출. 자바스크립트는 자식 생성자에서 반드시 super 호출 필요. message 프로퍼티는 부모 생성자에서 설정
    this.name = "ValidationError"; // 원하는 이름으로 재설정, this.constructor.name을 사용해도 같은 결과
  }
}
```

새로운 커스텀 에러 클래스를 만들었다면, 위 `readUser(json)` 함수와 함께 사용하는 코드를 보자.

```tsx
const readUser = (json: string) => {
  let user = JSON.parse(json);

  if (!user.age) {
    throw new ValidationError("No field: age");
  }
  if (!user.name) {
    throw new ValidationError("No field: name");
  }

  return user;
};

try {
  const user = readUser('{ "age": 25 }'); // 잘못된 형식의 json 데이터 전달
} catch (e) {
  // catch 블록에선 유효성 검사와 문법 오류만 처리하고, 다른 종류의 에러는 밖으로 던진다.
  if (e instanceof ValidationError) {
    // instanceof를 통해 에러 유형을 확인
    console.log("Invalid data: " + e.message);
  } else if (e instanceof SyntaxError) {
    console.log("JSON Syntax Error: " + e.message);
  } else {
    throw e; // 알려지지 않은 에러가 있다면 에러를 다시 던진다.
  }
}
```

위 `try/catch` 블록을 통해 커스텀 에러 `ValidationError`와 `JSON.parse`에서 발생하는 `SyntaxError` 둘 다를 처리할 수 있게 된다.

**[⬆ back to top](#목차)**

## 에러 유형 확인 시에 instanceof를 사용하라

에러 유형 확인은 `instanceof` 말고 다음과 같이 `e.name`을 사용해도 가능하다. 하지만 `instanceof`를 사용하는게 훨씬 좋다. 애플리케이션 크기가 커지게 되면 에러 클래스들도 자연스레 계층 구조를 형성하게 될 것이고 \***\*`instanceof`**는 새로운 상속 클래스에서도 동작하기 때문\*\*이다. `HttpTimeoutError`는 `HttpError`를 상속받는 식으로 말이다.

```tsx
class ValidationError extends Error {
  constructor(message: string) {
    super(message); // super로 부모 생성자 호출. 자바스크립트는 자식 생성자에서 반드시 super 호출 필요. message 프로퍼티는 부모 생성자에서 설정
    this.name = this.constructor.name; // 원하는 이름으로 재설정
  }
}

// 누락된 프로퍼티에 대한 추가 정보가 담긴 새로운 에러 클래스
class PropertyRequiredError extends ValidationError {
  property: string;
  constructor(property: string) {
    super("No property: " + property);
    this.name = this.constructor.name;
    this.property = property;
  }
}

const readUser = (json: string) => {
  let user = JSON.parse(json);

  if (!user.age) {
    throw new PropertyRequiredError("No field: age");
  }
  if (!user.name) {
    throw new PropertyRequiredError("No field: name");
  }

  return user;
};
```

`VallidationError` 클래스는 너무 포괄적이어서 뭔가 잘못될 확률이 있다. 꼭 필요한 프로퍼티가 누락되거나 age에 문자열 값이 들어가는 것처럼 형식이 잘못된 경우를 처리할 수 없다. 필수 프로퍼티가 없는 경우에 대응할 수 있도록 좀 더 구체적인 클래스를 만들어보자.

이 경우 에러의 이름을 문자열과 비교하거나 `instanceof`를 통해 에러를 확인할 수 있다. 둘의 동작을 확인해보자.

### Bad:

```tsx
try {
  const user = readUser('{ "age": 25 }');
} catch (e) {
  if (e.name === "ValidationError") {
    console.log("Invalid data: " + e.message);
    console.log(e.name);
    console.log(e.property);
  } else if (e.name === "SyntaxError") {
    console.log("JSON Syntax Error: " + e.message);
  } else {
    throw e; // 여기 코드가 실행된다.
  }
}
```

### Good:

```tsx
try {
  const user = readUser('{ "age": 25 }');
} catch (e) {
  if (e instanceof ValidationError) {
    console.log("Invalid data: " + e.message); // Invalid data: No property: name
    console.log(e.name); // PropertyRequiredError
    console.log(e.property); // name
  } else if (e instanceof SyntaxError) {
    console.log("JSON Syntax Error: " + e.message);
  } else {
    throw e;
  }
}
```

위와 같이 `instanceof`를 새로 만든 상속 클래스 `PropertyRequiredError`로 변경해주지 않아도 자연스레 새로운 상속 클래스를 사용할 수 있게 된다.

**[⬆ back to top](#목차)**

## 호출자를 고려해 예외 클래스를 정의하라 (예외 감싸기)

오류를 분류하는 방법은 수없이 많다. 오류가 발생한 위치로 분류가 가능하다. 오류가 발새한 컴포넌트, 유형, 디바이스 실패, 네트워크 실패, 프로그래밍 오류 등으로 분류할 수 있다. 하지만 **어플리케이션에서 오류를 정의할 때 프로그래머에게 가장 중요한 관심사는 “오류를 잡아내는 방법”이 되어야 한다**.

다음과 같은 에러 처리 스키마가 있다고 가정해보자.

```tsx
try {
  ...
  readUser()  // 잠재적 에러 발생처
  ...
} catch (err) {
  if (err instanceof ValidationError) {
    // validation 에러 처리
  } else if (err instanceof SyntaxError) {
    // 문법 에러 처리
  } else {
    throw err; // 알 수 없는 에러는 다시 던지기 함
  }
}
```

위 스키마에는 두 종류의 에러만 나와 있다. 그런데 `readUser` 함수의 기능이 커지면서 에러 종류가 많아질 것이므로 종류가 더 추가될 수 있다. 그러면 ‘에러 종류에 따라 매번 에러 처리 분기문을 추가해야 하나?’ 라는 의문이 들 수 있다.

보통은 그렇지 않다. 실제 **우리가 필요로 하는 정보는 “데이터를 읽을 때” 에러가 발생했는지에 대한 여부다.** 왜 에러가 발생했는지와 자세한 설명은 대부분의 경우 필요하지 않다.

이러한 상황에 필요할 때 에러 세부사항에 대한 정보를 얻기 위한 에러 처리 기술을 “**예외 감싸기(wrapping exception)**”라고 부른다. 예외감싸기는 다음과 같은 순서로 진행된다.

1. ‘데이터 읽기'와 같은 포괄적인 에러를 대변하는 새로운 클래스 `ReadError`를 생성한다.
2. **함수** `readUser`에서 발생한 `ValidationError`, `SyntaxError` 등의 **에러는** `readUser` **내부에서 잡고** 이때 `ReadError`를 생성한다.
3. `ReadError` 객체의 `cause` 프로퍼티엔 실제 에러에 대한 참조가 저장된다.

이렇게 예외 감싸기 기술을 써서 스키마를 변경하면 `readUser`를 호출하는 코드에선 `ReadError`만 확인하면 된다. 데이터를 읽을 때 발생하는 에러 종류 전체를 확인하지 않아도 된다. 추가 정보가 필요하다면 `cause` 프로퍼티를 확인하면 된다.

### Bad:

```tsx
class ValidationError extends Error {
  [x: string]: any;
  constructor(message: string) {
    super(message); // super로 부모 생성자 호출. 자바스크립트는 자식 생성자에서 반드시 super 호출 필요. message 프로퍼티는 부모 생성자에서 설정
    this.name = this.constructor.name; // 원하는 이름으로 재설정
  }
}

class PropertyRequiredError extends ValidationError {
  property: string;
  constructor(property: string) {
    super("No property: " + property);
    this.name = this.constructor.name;
    this.property = property;
  }
}

const readUser = (json: string) => {
  let user = JSON.parse(json);

  if (!user.age) {
    throw new PropertyRequiredError("No field: age");
  }
  if (!user.name) {
    throw new PropertyRequiredError("No field: name");
  }

  return user;
};

try {
  const user = readUser("{잘못된 형식의 json}");
} catch (e) {
  if (e instanceof ValidationError) {
    console.log("Invalid data: " + e.message); // Invalid data: No property: name
    console.log(e.name); // PropertyRequiredError
    console.log(e.property); // name
  } else if (e instanceof SyntaxError) {
    console.log("JSON Syntax Error: " + e.message);
  } else {
    throw e;
  }
}
```

### Good:

```tsx
class ReadError extends Error {
  cause: Error;
  constructor(message: string, cause: Error) {
    super(message);
    this.cause = cause;
    this.name = this.constructor.name;
  }
}

class ValidationError extends Error {
  [x: string]: any;
  constructor(message: string) {
    super(message); // super로 부모 생성자 호출. 자바스크립트는 자식 생성자에서 반드시 super 호출 필요. message 프로퍼티는 부모 생성자에서 설정
    this.name = this.constructor.name; // 원하는 이름으로 재설정
  }
}

class PropertyRequiredError extends ValidationError {
  property: string;
  constructor(property: string) {
    super("No property: " + property);
    this.name = this.constructor.name;
    this.property = property;
  }
}

interface User {
  name: string;
  age: number;
}

const validateUser = (user: User) => {
  if (!user.age) {
    throw new PropertyRequiredError("No field: age");
  }
  if (!user.name) {
    throw new PropertyRequiredError("No field: name");
  }
};

const readUser = (json: string) => {
  let user;

  try {
    user = JSON.parse(json);
  } catch (err) {
    if (err instanceof SyntaxError) {
      throw new ReadError("Syntax Error", err);
    } else {
      throw err;
    }
  }

  try {
    validateUser(user);
  } catch (err) {
    if (err instanceof ValidationError) {
      throw new ReadError("Validation Error", err);
    } else {
      throw err;
    }
  }
};

try {
  readUser("{잘못된 형식의 json}");
} catch (e) {
  if (e instanceof ReadError) {
    console.log(e);
    console.log("Original error: " + e.cause); // Original error: SyntaxError: Unexpected token 잘 in JSON at position 1
  } else {
    throw e;
  }
}
```

이제 `readUser`는 `Syntax` 에러나 `Validation` 에러가 발생한 경우 해당 에러 자체를 다시 던지기 하지 않고 `ReadError`를 던지게 된다. 이렇게 `readUser`를 호출하는 바깥 코드에선 `instanceof ReadError` 딱 하나만 확인하면 된다. 에러 처리 분기문을 여러 개 만들 필요가 없어진다.

‘**예외 감싸기**'란 이름은 종류별 에러를 좀 더 추상적인 에러, `ReadError`에 하나로 모아(wrap) 처리하기 때문에 붙여졌다. 이러한 기법은 객체 지향 프로그래밍에서 널리쓰이는 패턴이다.

### 예외 감싸기의 이점

- 외부 API를 감싸면 외부 라이브러리와 프로그램 사이에서 의존성이 크게 줄어든다.
- 다른 라이브러리로의 변경 시에도 비용이 적다.
- 테스트하기 쉬워진다.
- 특정 업체가 API를 설계한 방식에 구애받지 않는다.

**[⬆ back to top](#목차)**

## 요약

깨끗한 코드는 읽기도 좋아야 하지만 안정성도 높아야 한다. 오류 처리를 프로그램 논리와 분리해 독자적인 사안으로 고려하면 튼튼하고 깨끗한 코드를 작성할 수 있다. **오류 처리를 프로그램 논리와 분리해 독립적인 추론이 가능하게 하고 코드 유지보수성을 높이자**.

- **예외처리**를 통해 의미를 알기 힘든 알 수 없는 기술 용어로 범벅된 에러 메시지보다 코드를 작성한 **개발자가 이해하고 구분할 수 있는 메시지로 예외에 의미를 제공**해라.
- 에러 객체는 `message`, `name`, `stack` 프로퍼티를 가진다.
- 에러 객체의 서브 타입으로는 `Range`, `Reference`, `Syntax`, `Type`, `URI`가 있다.
- **항상 에러 객체를 사용**해서 에러가 어디서 발생했는지 알리고 스택에 기록한다.
- 에러가 포괄적이라면 커스텀 에러를 사용해서 직관적으로 알린다.
- **커스텀 에러**는 `Error` 객체를 상속받아서 만든다. 이때 `super`를 호출해야 하며 `name` 프로퍼티를 통해 에러의 이름을 정한다.
- **에러 판별**은 `instanceof`를 사용한다. 이는 상속된 클래스에서도 동작한다. 하지만 서드파티 라이브러리에서 온 에러 객체는 클래스를 알아내기 쉽지 않으므로, `name` 프로퍼티를 사용해 오류 종류를 확인한다.
- **예외 감싸기**는 객체 지향 프로그래밍에서 널리 알려진 예외 처리 기술로 이를 적용한 함수에선 모든 에러를 종류별로 처리하지 않는다. 대신 모든 에러를 포함할 수 있는 추상 에러를 만들고, 에러가 발생하면 이 추상 에러를 던지도록 한다. 추상 에러를 던질 때 실제 발생한 에러의 프로퍼티(`error.cause`)로 넘기면 구체적인 에러 정보를 함께 넘겨줄 수 있는데, 반드시 필요한 것은 아니다.

**[⬆ back to top](#목차)**
