# JavaScript 핸드북

## 호이스팅

클래스 기반 객체지향은 객체를 추상적이고 본질적인 클래스로 분류하여 속성과 연산을 기반으로 일반화합니다. 반면, 프로토타입 기반 객체지향에서는 어휘의 본질적인 측면은 없고 상황과 맥락에 따라 의미를 결정합니다. 유사성을 기반으로 가장 전형적인 원형 객체를 정의하고 객체들은 원형을 기반으로 차이점을 반영하여 확장됩니다.

프로토타입 기반 객체지향 언어인 JavaScript(이하 JS)는 실행 컨텍스트에 있는 모든 선언들을 참고하여 어휘의 의미를 정의합니다. 식별자는 선언 단계와 초기화 단계 그리고 할당 단계로 나누어져 있으며, 실행 컨텍스트를 생성할 때 `Lexical Environment`의 `Environment Record`에 변수를 수집하는 선언 단계가 코드 실행 전에 이루어지기 때문에 `Lexical Scope`의 최상단으로 끌어올려지는 것처럼 동작합니다.

`var`은 선언과 동시에 초기화되어 `undefined`가 할당되지만, `let`과 `const`는 그렇지 않습니다. 따라서 초기화 전에 변수를 사용하면 `ReferenceError`가 발생합니다.

`function` 키워드를 사용한 함수 선언문은 선언과 동시에 함수가 할당되므로 호이스팅됩니다. 하지만, 함수를 변수에 할당하는 함수 표현식은 변수 선언만 호이스팅되므로 함수가 호출되기 전에 변수에 할당되어 있어야 합니다.

`class` 키워드를 통한 클래스 선언은 호이스팅되지만, 초기화되지 않은 `TDZ` (Temporal Dead Zone) 상태를 유지합니다. 그러나 클래스 표현식은 함수 표현식과 유사하게 할당은 호이스팅되지 않습니다.

### 참고 문서

- [변수의 유효범위와 클로저](https://ko.javascript.info/closure)

## this

어휘의 의미는 어휘가 속한 문맥에 따라 달라집니다. 문맥에는 그 어휘를 사용하는 주체가 포함되어 있으며, 이 발화 주체는 this로 표현됩니다. 전역 영역에 있는 함수를 호출할 때는 일반적으로 글로벌 객체(브라우저에서는 window)를 가리키며(strict mode일 경우에는 undefined), 객체 안에 속한 메서드를 호출할 때는 해당 객체를 나타냅니다. 생성자가 호출될 경우에는 생성 결과로 응답되는 객체를 나타냅니다.

### 참고 문서

- [this - JavaScript | MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/this)

## 클로저

클로저는 주변 문맥과 그에 대한 참조를 가지고 있는 함수의 조합을 나타냅니다. 즉, 클로저는 내부 함수에서 외부 함수의 스코프에 대한 접근을 제공합니다. 

내부 함수의 실행 컨텍스트가 생성될 때, `Lexical Environment`의 `Outer Environment Reference`에는 내부 함수가 선언될 당시의 외부 함수의 `Lexical Environment`를 참조합니다. 내부 함수에선 `Outer Environment Reference`를 통해 상위 `Lexical Environment` 에 접근하여 외부 변수를 참조할 수 있습니다. 내부 함수에서 `Lexical Environment`를 참조하고 있어 외부 함수가 종료되어 콜 스택에서 실행 컨텍스트가 제거된 이후에도 `GC`에 의해 수집되지 않고 접근할 수 있습니다.

### 참고 문서

- [변수의 유효범위와 클로저](https://ko.javascript.info/closure)

## 실행 컨텍스트

실행 컨텍스트(Execution Context)는 코드를 실행하는 데 필요한 환경을 제공하고 실행 결과를 실제로 관리하는 객체입니다. 

식별자와 스코프는 실행 컨텍스트의 `Lexical Environment`에서 관리되며, 코드 실행 순서는 실행 컨텍스트 스택, 즉 콜 스택에서 관리됩니다. 실행 컨텍스트는 `Lexical Environment`, `Variable Environment` 등으로 구성되며, `Variable Environment`는 초기 상태를 기억하고 있고 `Lexical Environment`는 최신 상태를 저장하고 있습니다.

`Variable Environment`는 최초 실행 시에 설정되며 `Lexical Environment`가 그 값을 복사해서 관리합니다. ES2020까지는 `Lexical Environment`의 `Environment Record`는 현재 컨텍스트와 관련된 식별자와 식별자에 바인딩된 값을 관리하며, `Outer Environment Reference`에서 상위 컨텍스트에 대한 참조를 관리하지만 ES2021부터는 `Lexical Environment`의 타입 자체가 `Environment Record`가 되어 `Environment Record` 내에서 상위 컨텍스트에 대한 참조도 같이 관리하게 되었습니다.

### 참고 문서

- [[번역]Javascript의 실행 콘텍스트와 실행 스택 이해하기](https://kimlog.me/js/2020-01-30-execution-context/)
- [Explore the lexical Environment & Environment Record in Javascript 2021](https://dev.to/codescoop/what-has-changed-in-lexical-environment-as-per-ecmascript-2021-2bjb)

## 이벤트 루프

JS는 단일 스레드 기반의 언어이기 때문에 동시에 여러 작업을 처리하기 위해 비동기 처리가 필요합니다. 이를 위해 이벤트 루프가 런타임에서 비동기 작업들을 관리하고 실행합니다. 이벤트 큐는 JS 코드를 실행하는 JS 엔진에 직접적으로 속하지는 않지만 런타임 구성에 중요한 역할을 합니다.

이벤트 루프는 여러 단계를 반복하며 여러 큐의 콜 스택이 비어있을 때 실행할 작업을 콜 스택으로 이동시켜 실행합니다. `Node.js` 에서는 다양한 단계가 존재하며, 모든 단계에서 실행 가능한 높은 우선순위를 가진 `nextTickQueue`와 `microTackQueue`가 있습니다.

첫 번째 `Timer` 단계에는 `setTimeout`이나 `setInterval`과 같은 타이머 콜백을 처리합니다. 이러한 콜백은 시간순으로 정렬된 힙에 등록되게 됩니다. 이 단계에서는 힙에 있는 타이머가 실행될 시점이 되었는지 확인하고 큐에 넣어 처리합니다.

다음으로 `Pending I/O` 단계에서는 `pending_queue`에 들어있는 콜백들을 처리하게 됩니다. 이 큐에는 이전 루프에서 처리가 연기된 TCP 오류 처리와 같은 I/O 콜백들을 처리합니다.

그 외에도 `Idle`, `Prepare`은 내부적인 관리를 위해 사용되고, `Poll` 단계에서는 `watch_queue`에 있는 I/O 콜백을 처리하고 다른 단계에서 처리할 작업이 큐에 추가 될 때까지 일정 시간을 대기합니다.

그 이후의 `Check` 단계에서는 `setImmediate`로 등록된 콜백들을 처리하고, `Close` 단계에서는 `close` 이벤트에 등록된 콜백들을 처리합니다.

`nextTickQueue`는 `process.nextTick()`의 콜백을, `microTackQueue`는 `Promise`의 `Resolve`된 콜백을 가지고 있습니다. 이들은 매 단계에서 현재 실행 중인 작업이 끝난 직후에 빠르게 처리되어야 합니다. 이 두 큐는 다른 큐들과는 다르게 시스템 콜에 접근하지 않아 시스템 실행 한도에 영향을 받지 않고 큐가 완전히 비어질 때까지 콜백들을 실행합니다. `nextTickQueue`가 `microTackQueue`보다 더 높은 우선순위를 가지고 먼저 작업이 처리됩니다.

시스템 콜은 `epoll`과 같은 비동기 API를 활용하여 처리되지만 비동기를 지원하지 않는 연산을 처리하기 위한 스레드 풀이 존재합니다. 이 스레드 풀은 JS 코드를 실행하는 것이 아닌 일부 OS의 파일 읽기, DNS Lookup과 같은 동기 시스템 콜을 처리하기 위해 활용됩니다.

### 참고 문서

- [Node.js - The Node.js Event Loop](https://nodejs.org/en/learn/asynchronous-work/event-loop-timers-and-nexttick)
- [Design overview - libuv document](https://docs.libuv.org/en/v1.x/design.html)

## 비동기

비동기 처리를 위해 전통적으로 콜백이 사용되었지만, 콜백은 비선형적이고 비순차적으로 표현돼 직관적으로 이해하기 어려웠습니다. 게다가 제어하기 어려운 외부 의존성에 종종 처리가 위임되어 의도한 대로 동작하는지 확인하기 어려웠습니다.

그 결과, 비동기 작업을 추상화한 `Promise`가 등장하게 되었습니다. 이를 통해 여러 비동기 작업을 선형적으로 연결할 수 있게 되었고, 시간에 의존적인 상태를 내부로 숨겨 비동기 작업 자체는 동기적으로 관리할 수 있게 되었습니다.

대기 중인 `Promise`는 비동기 작업의 결과에 따라 이행(fulfilled)되거나 거부(rejected)됩니다. 그 후, 처리가 완료되면 연결된 다른 작업이 큐에 들어가 대기하게 됩니다.

`Promise`는 동시성을 용이하게 하기 위해 `all`, `any`와 같은 메서드를 추가하였지만, 기존과는 다른 제어 방식을 활용해야 했습니다. 이에 기존의 표준 동기 함수를 사용하는 것과 유사하게 작업을 제어하기 위해 `async`와 `await`이 등장하게 되었습니다.

`async` 함수에 포함되는 `await`식은 함수의 실행을 일시 중지하고 전달된 `Promise`의 해결을 기다린 후 함수를 재실행합니다. `async` 함수는 반환값이 `Promise`가 아니라면 자동으로 `Promise`로 감싸서 반환하게 됩니다.

`async`와 `await`를 지원하지 않는 런타임을 위해 `Babel`과 같은 트랜스컴파일러는 기존에 지원하던 코루틴인 `generator`를 활용하여 이 구문을 변환했습니다. 함수에서 `Promise`를 대기하는 경우, `yield`를 사용하여 함수를 일시 중단하고 `Promise`가 완료될 때까지 대기한 후 결과를 다시 전달하여 재개합니다.

### 참고 문서

- [Promise - JavaScript | MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [async function - JavaScript | MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/async_function)
- [비동기와 Callback, Promise, Async/Await | Marco](https://wonsss.github.io/javascript/async-callback-promise)
- [Async-await는 어떻게 구현하는가](https://medium.com/@la.place/async-await%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EA%B5%AC%ED%98%84%ED%95%98%EB%8A%94%EA%B0%80-fa08a3157647)

## 원시 값

원시 값은 객체가 아니며 직접적으로 메서드나 속성을 갖지 않는 값입니다. `string`, `number`, `bigint`, `boolean`, `undefined`, `symbol`, `null`이 원시 값에 해당하며, 이들은 불변하고 변형할 수 없습니다. 원시 값에는 메서드가 없지만 런타임이 값을 자동으로 래퍼 객체로 감싸서 마치 메서드가 있는 것처럼 동작합니다.

`null`을 제외한 모든 원시 값은 `typeof` 연산자로 타입을 확인할 수 있지만 `typeof null`은 "object"를 반환하므로 `=== null`을 사용하여 `null`을 확인해야 합니다.

원시 값과 객체를 비교하기 위해 `==`, `===` 그리고 `Object.is()`를 사용할 수 있습니다. `==` 연산자는 비교하기 전에 다양한 변환을 적용하지만, `===` 는 그렇지 않습니다. `Object.is`는 `===` 와 비슷하게 타입을 변환하지 않지만 `-0`과 `+0`을 다르게 처리하고, `NaN`은 서로 같게 처리합니다.

### 참고 문서

- [원시 값 - MDN](https://developer.mozilla.org/ko/docs/Glossary/Primitive)
- [JavaScript의 타입과 자료구조](https://developer.mozilla.org/ko/docs/Web/JavaScript/Data_structures)
- [Object.is() - JavaScript | MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/is)
