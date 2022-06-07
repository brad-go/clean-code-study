# 13장 동시성

## 목차

1. [자바스크립트의 싱글 스레드와 동시성](#자바스크립트의-싱글-스레드와-동시성)
2. [자바스크립트 엔진 구조](#자바스크립트-엔진-구조)
3. [동시성과 이벤트 루프](#동시성과-이벤트-루프)
4. [콜백보다 프로미스를 사용하라](#콜백보다-프로미스를-사용하라)
5. [async/await은 프로미스보다 깔끔하다](#asyncawait은-promise보다-깔끔하다)

## 자바스크립트의 싱글 스레드와 동시성

자바스크립트는 **싱글 스레드** 기반 언어이다. 이는 **하나의 호출 스택(call stack)을 가지고, 한 번에 한 가지의 일만 수행할 수 있다는 것을 의미**한다. 그러나 자바스크립트에서는 `setTimeout`, `이벤트 callback` 함수 등은 비동기적으로 수행된다. 어떻게 가능할까? 호출 스택이 하나인 자바스크립트에서 동시성이 지원 가능한 이유는 **런타임(브라우저, Node.js)** 때문이다.

- **스레드(thread)**: 어떠한 프로그램 내에서, 특히 프로세스 내에서 실행되는 흐름의 단위
- **프로세스(process)**: 컴퓨터에서 연속적으로 실행되고 있는 컴퓨터 프로그램.
- **동시성(concurrency)**: 여러 작업이 마치 동시에 일어나는 것처럼 보이는 것

[⬆ Back to top](#목차)
<br />

## 자바스크립트 엔진 구조

자바스크립트를 실행하기 위해서는 엔진이 필요한데, 이는 코드를 해석하고 실행하는 인터프리터이다. 엔지의 종류로는 대표적으로 `V8`엔진을 들 수 있다.

![https://velog.velcdn.com/images%2Fminn602%2Fpost%2F8a19fd94-465b-4438-93b0-f83ff8d1f401%2Fimage.png](https://velog.velcdn.com/images%2Fminn602%2Fpost%2F8a19fd94-465b-4438-93b0-f83ff8d1f401%2Fimage.png)

자바스크립트 엔진은 두 가지 구성요소로 구성된다. 하나는 **Memory Heap(메모리 힙)** 이고, 다른 하나는 **Call Stack(호출 스택)** 이다.

- **Memory Heap(메모리 힙)**: **동적으로 생성된 객체(인스턴스)가 할당되는 곳(메모리 할당이 일어나는 곳)**
- **Call Stack(호출 스택)**: 자바스크립트 엔진은 **코드 실행에 따라 순차적으로 수행해야할 함수(스택)가 쌓이는 곳**으로 현재 프로그램 상에서 어디를 실행하고 있는지 기록이 쌓인다.

[⬆ Back to top](#목차)
<br />

## 동시성과 이벤트 루프

자바스크립트의 엔진은 위와 같은 구조로 이루어져있고, 자바스크립트 자체에는 비동기 코드를 처리하기 위한 개념을 가지고 있지 않다. 즉, 싱글 스레드인 언어인 자바스크립트는 한 번에 한가지 일만 수행 가능하다. 이는 시간이 많이 소요되는 함수를 수행할 때에는 **blocking**(함수를 호출하면, 제어권을 함수에 넘기며 대기하는 방식) 상태로 다른 코드의 실행을 막게 되어 많은 문제를 발생시킨다는 말이다.

![https://velog.velcdn.com/images%2Fyounoah%2Fpost%2F51cd222a-da15-4e59-a913-07f1300bca33%2Fsynchronous.png](https://velog.velcdn.com/images%2Fyounoah%2Fpost%2F51cd222a-da15-4e59-a913-07f1300bca33%2Fsynchronous.png)

그런데 어떻게 비동기 코드를 처리할 수 있을까?

![이벤트 루프](https://miro.medium.com/max/1400/1*4lHHyfEhVB0LnQ3HlhSs8g.png)

비동기 요청은 자바스크립트 엔진이 아닌 자바스크립트 엔진을 구동하는 런타임 환경(브라우저나 Node.js)가 담당한다. 그리고 이 런타임은 위 그림과 같이 **`Web APIs`**, **`Callback Queue`**, **`Event Loop`** 를 지원한다.

- **Web APIs**: 런타임 환경이 지원하는 API들, `setTimeout`, `DOM`, `AJAX` 등
- **Event Loop(이벤트 루프)**: 호출 스택(call stack)과 콜백 큐(callbakc queue)를 감시하면서 **호출 스택이 비어있을 때 콜백 큐에 있던 작업을 호출 스택에 넣어 수행되도록 하는 역할**
- **Callback Queue(콜백 큐)**: 자바사크립트의 런타임 환경에서 처리해야할 작업들(**비동기로 처리해야 하는 작업들)을 임시 저장해두는 대기열의 역할** (Task Queue, Event Queue라고도 한다.)

### Example:

```tsx
function foo() {
  console.log("1");
}

function foo2() {
  console.log("2");
}

foo();
setTimout(() => {
  console.log("3");
}, 2000);
foo(2);

// 1 2 3
```

[⬆ Back to top](#목차)
<br />

## 콜백보다 프로미스를 사용하라

콜백은 깔끔하지 못하다. 또, 그들은 지나칠 정도의 중첩을 일으킬 수 있다. (콜백 지옥)

기존에 존재하는 콜백 스타일의 함수들을 프로미스 버전으로 변환해주는 몇 가지 유틸리티가 있다. (Node.js의 경우 [`util.promisify`](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_promisify_original)를 참조, 일반적으로는 [`pify`](https://www.npmjs.com/package/pify), [`es6-promisfiy`](https://www.npmjs.com/package/es6-promisify)를 참조)

- **사전 설정**
  ```tsx
  npm i -D @types/request @types/node;
  ```
  ```tsx
  // tsconfig.json
  "compilerOptions": {
  	"types": [ "node" ]
  }
  ```

### Bad:

```tsx
import { get } from "request";
import { writeFile } from "fs";

function downloadPage(
  url: string,
  saveTo: string,
  callback: (error: Error, content?: string) => void
) {
  get(url, (error, response) => {
    if (error) {
      callback(error);
    } else {
      writeFile(saveTo, response.body, (error) => {
        if (error) {
          callback(error);
        }
      });
    }
  });
}

downloadPage(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  "article.html",
  (error, content) => {
    if (error) {
      console.log(error);
    } else {
      console.log(content);
    }
  }
);
```

### Good:

```tsx
import get from "axios";
import { writeFile } from "fs";
import { promisify } from "util";

const write = promisify(writeFile);

const downloadPage = (url: string, saveTo: string): Promise<string | void> => {
  return get(url).then((response) => write(saveTo, response.data));
};

downloadPage(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  "article.html"
)
  .then((content) => console.log(content))
  .catch((error) => console.log(error));
```

프로미스들은 코드를 더 간결하게 만들어줄 몇가지 메서드를 지원한다.

| Pattern                | Description                                                                                                        |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------ |
| Promise.resolve(value) | 값을 이행된 프로미스로 변환해준다.                                                                                 |
| Promise.reject(error)  | 값을 거절된 프로미스로 변환해준다.                                                                                 |
| Promise.all(promises)  | 전달된 프로미스들의 이행된 값들을 배열로 이행된 새 프로미스를 반환하거나 거부된 첫번째 프로미스의 이유를 반환한다. |
| Promise.race(promises) | 전달된 프로미스들의 배열에서 처음으로 해결된 프로미스의 결과/오류로 이행/거부된 새 프로미스를 반환한다.            |

Promise.all은 병렬적으로 작업을 수행하기 특히 유용하다. Promise.race는 프로미스의 timeout같은 것을 쉽게 구현하게 해준다.

[⬆ Back to top](#목차)
<br />

## Async/Await은 Promise보다 깔끔하다

`aysnc/await` 구문은 연쇄적인 프로미스들보다 더 이해하기 쉽고, 더 깔끔하다. `async`를 상단에 선언해줌으로써 자바스크립트에게 `await` 키워드 뒤에오 오는 실행을 기다리게 할 수 있다. (프로미스를 사용할 경우)

### Bad:

```tsx
import get from "axios";
import { writeFile } from "fs";
import { promisify } from "util";

const write = promisify(writeFile);

const downloadPage = (url: string, saveTo: string): Promise<string | void> => {
  return get(url).then((response) => write(saveTo, response.data));
};

downloadPage(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  "article.html"
)
  .then((content) => console.log(content))
  .catch((error) => console.log(error));
```

### Good:

```tsx
import get from "axios";
import { writeFile } from "fs";
import { promisify } from "util";

const write = promisify(writeFile);

const downloadPage = async (url: string, saveTo: string): Promise<string> => {
  const response = await get(url);
  await write(saveTo, response.data);
  return response.data;
};

// 비동기 함수 안에서 정의되었다고 가정
try {
  const content = await downloadPage(
    "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
    "article.html"
  );
  console.log(content);
} catch (error) {
  console.error(error);
}
```

`async/await`을 사용하면 비동기 코드를 동기 코드처럼 쉽게 읽히게 해줌으로 코드 흐름을 이해하기 쉽고, 가독성이 좋아진다. 다만 에러 핸들링 기능이 없어서 `try-catch`문을 이용해야 하는데 너무 중첩되지 않게 잘 사용해야 한다.

[⬆ Back to top](#목차)
<br />
