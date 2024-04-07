# 리액트의 등장 배경

페이스북에서 무거운 사용자 인터페이스를 사용했는데, 그당시 기술 스택(바닐라 자바스크립트, 앵귤러, 제이쿼리 등) 효율적이지 않다고 생각함.
그래서 초반에 페이스북에서는 `BoltJS` 를 개발했고, 계속 업데이트를 해 나가다가 React 형태로 발전하게 됐습니다.
그리고 초기에 Class 컴포넌트 형식을 사용했지만, 추후에는 Function Component 를 사용하게 됐습니다.

### 단뱡항 데이터 바인딩

리액트는 `단방향 바인딩` 데이터 흐름이 한 방향으로만 이루어진다는 것.
컴포넌트의 `상태(state)`가 변경되면, 해당 변경 사항이 `뷰(view)`에 반영이 됩니다.
하지만 뷰에서는 직접적으로 상태를 변경할 수 없음, 상태를 변경하기 위해서는 상태 변경 함수를 사용해야 합니다.
-> `이러한 접근 방식은 데이터 흐름을 예측 가능하게 하고, 코드의 가독성을 향상시키고 버그 발생을 줄여줍니다.`

```javascript
import React, { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      // `setCount` 함수를 호출하여 `count` 상태를 업데이트합니다.
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

`useState(0)` 은 `count` 라는 상태를 0으로 초기화, 이 상태를 업데이트할 수 있는 `setCount` 를 제공합니다.

사용자가 버튼을 클릭할 떄 마다 `setCount` 함수가 호출되고, `count` 상태를 업데이트 되며, 리액트에 의해 화면에 반영이 됩니다. -> `데이터가 한 방향으로만 흐르는 단방향 데이터 바인딩 예시`

### 리액트 등장과 초기 회의적 의견

리액트는 초기에 JSX 구문을 도입 -> 자바스크립트 내에 HTML 직접적으로 작성할 수 있음.
이 접근법은 `MVC` 패턴과는 다르게, 많은 개발자에게 의아함을 줌.

하지만, 리액트 팀은 `웹 애플리케이션을 효율적으로 관리하기 위한 새로운 패러다임`을 제시하고자 했고, 이 시간에 지남에 따라, 점점 더 많은 개발자와 회사들이 인정받게 됨.

리액트 성장과 함꼐, 상태 관라 라이브러리 (예: Redux, Mobox), 라우팅 라이브러리 (예: React Router), 서버 사이드 렌더링 프레임워크 (예: Next.js) 등 리액트를 보완하는 다양한 라이브러리 프레임워크가 등장하며, 리액트 생태계는 급속도로 확장되었습니다.

#### 리액트의 얕은 비교

리액트에서 `얕은 비교`는 주로 props, state 변화를 감지할 떄 사용.
객체의 최상위 프로퍼티만을 비교 해, 객체 내부의 객체까지의 비교하진 않습니다.
이 방식은 성능상의 이유로 선택, 대부분 경우에는 충분한 정보를 제공.

예로, 컴포넌트의 props가 변경되었는지 확인하기 위해 얕은 비교를 사용 가능.

```javascript
function areEqual(prevProps, nextProps) {
  // 얕은 비교를 통해 props 변경 감지
  return prevProps.value === nextProps.value;
}
React.memo(MyComponent, areEqual);
```

#### 재귀적 비교의 문제점

객체 안에 중첩된 객체를 `완벽하게 비교하기 위해 재귀적 비교`를 수행할 경우, `성능 저하의 우려`가 있음.
객체의 깊이가 얼마나 될지 예측할 수 없고, 매 런데링마다 깊은 비교를 수행하는 것은 리소스를 많이 소모할 수 있습니다.
따라 리액트는 효율성과 실용성을 고려해 `얕은 비교`를 기본 방식으로 사용.

자바스크립트의 동등 비교 원리와 리액트에서의 적용 방식을 이해하는 것은 리액트 개발에 있어 중요한 요소, 이를 통해 효율적 컴포넌트 업데이트와 성능 최적화 전략을 구현 가능합니다.

## 함수

함수의 다양한 형태와 함수의 차이점이 무엇인지 알아보자.
Component 에서는 props을 단일 props 으로 전달 하거나, {...props} 형태로 모든 props 을 전개 연산자로 받는 다는 차이가 있습니다.

### 함수 표현식

일급 객체 : 프로그래밍 세계에서 일급 객체런 `다른 객체들에 일반적 적용 가능한 연산을 모두 지원`하는 객체를 의미합니다.

자바스크립트는 일급 객체
-> 함수는 다른 함수의 `매개변수`가 될 수 있고, `반환값`이 될 수 있고, 앞에서 본 것처럼 할당도 가능하므로 `일급 객체가 되기 위한 조건`을 모두 갖추고 있습니다.

함수는 일급 객체이므로, 함수를 변수에 할당하는 것도 가능합니다.

```javascript
const sum = function (a, b) {
  return a + b;
};
sum(10, 24); // 34
```

함수 표현식에서는 할당하려는 함수의 이름을 생략하는 것이 일반적.
-> 함수를 변수에 할당 할떄, function add(a,b) 이런식으로는 실제 프로덕션 코드에서는 절대로 사용해서는 안됩니다. `함수 표현식에서 함수에 이름을 주는 것은 함수 호출에 도움이 전혀 안 되는, 코드를 읽는 데 방해가 될 수 있습니다.`

### 함수 표현식과 선언 식의 차이

2가지 방식의 가장 큰 차이는 `호이스팅(hoisting)` 입니다.
함수 선언문이 마치 코드 맨 앞단에 작성된 것처럼, 작동하는 자바스크립트 특징을 의미.

```javascript
hello(); // hello

function hello() {
  console.log("hello");
}

hello(); // hello
```

맨 앞단에 있는 hello() 가 잘 호출되고 있음.
함수의 호이스팅은 함수에 대한 선언을 실행 전 미리 메모리에 등록하는 작업.
-> 이러한 함수의 호이스팅이라는 특징 덕분에 `함수 선언문이 미리 메모리에 등록`, `코드의 순서에 상관없이 정상적으로 함수를 호출` 할 수 있게 된 것.

반면 함수 표현식은 함수를 변수에 할당, 변수도 마찬가지 호이스팅 발생, 그러나 함수의 호이스팅과는 다르게, 호이스팅되는 시점에서 let 경우에는 undefined 초기화가 되는 차이가 있습니다.

```javascript
console.log(typeof hello === "undefined"); // true
hello();

let hello = function () {
  console.log("hello");
};
hello();
```

`var` 변수로 선언된 변수는 호이스팅되어, 해당 스코프의 최상단으로 끌어올려집니다.
하지만 선언만 호이스팅되고 초기화는 호이스팅되지 않습니다. 이로 이해 변수를 선언하기 전에 접근하면 `undefined` 가 반환됩니다.

`let`, `const` 선언된 변수들은 블록 스코프를 가지며, 호이스팅되긴 하지만,
TDZ 는 변수가 선언되었지만 아직 초기화되지 않아 접근할 수 없는 코드 영역.

앞선 함수 선언문과는 다르게, undefined 으로 되어 있음.
함수와 다르게 변수는, 런타임 이전에 undefined 로 초기화, 할당문이 실행되는 시점, 즉 `런타임 시점에 함수가 할당되어 작동`한다는 것을 알 수 있음.

```javascript
const add = (a, b) => {
  return a + b;
};
const add = (a, b) => a + b;
```

### 다양한 함수 살펴보기

리액트에서 자주 쓰이는 함수 보기 -> 즉시 실행 함수, 고차 함수등이 있는 데 생략
함수를 만들 떄는 최대한 부수 효과(함수 외부 영향 막기), 가능한 함수를 작게 만들자, 누구나 이해할 수 있는 이름으로 만들기.

## Class

클래스란? 특정 형태의 객체를 반복적으로 만들기 위해 사용되는 것이 Class 입니다.
Class를 사용하면, 객체를 만드는 데, 필요한 데이터나 이를 조작하는 코드를 추상화해 객체 생성을 더욱 편리하게 할 수 있습니다.

```javascript
class Car {
  constructor(name) {
    this.name = name;
  }

  hook() {
    console.log(`${this.name}이 경적이 울립니다.`);
  }

  // 정적 메서드
  static hello() {
    console.log("저는 자동차입니다.");
  }
  // setter
  set age(value) {
    this.carAge = value;
  }
  // getter
  get age() {
    return this.carAge;
  }

  // Car Class를 활용해 car 객체를 만듬.
  const myCar = new Car('자동차')

  // 메서드 호출
  myCar.hook()

  // 정적 메서드는 클래스에서 직접 호출한다.
  Car.hello();

  // 정적 메서드는 클래스로 만든 객체에서는 호출할 수 없다.
  // Uncaught TypeError: myCar.hello is not a function
  myCar.hello()

  // setter 를 맏늘면 값을 할당 가능.
  myCar.age = 32

  // getter 로 값을 가져올 수 있다.
  console.log(myCar.age, myCar.name);
}
```

### constructor

생성자로, 이름에서 알 수 있는 것처럼 생성하는데, 사용하는 특수한 메서드 입니다.
단 하나만 존재할 수 있고, 여러 개를 사용하면 에러가 발생합니다.

### 프로퍼티

클래스로 인스턴스를 생성할 떄, 내부에 정의할 수 있는 속성값을 의미.
빈 객체에 프로퍼티의 키와 값을 넣어 활용할 수 있게 해줍니다.

```javascript
constructor(name) {
    // 값을 받으면 내부 프로퍼티를 할당.
    this.name = name;
}
const myCar = new Car('자동차') // 프로퍼티 값을 넘겨주었다.
```

### getter 와 setter

getter 란 class 에서 무언가 값을 가져올 댸 사용, getter 를 사용하기 위해서, get 을 앞에 붙어야 하고, 뒤 이어서 getter 의 이름을 선언해야 합니다.

```javascript
class Car {
  constructor(name) {
    this.name = name;
  }

  get firstCharacter() {
    return this.name[0];
  }
}

const myCar = new Car("자동차");
myCar.firstCharacter; //
```

setter 란 Class 필드에 값을 할당할 떄 사용,
마찬가지 set 이라는 키워드를 먼저 선언, 그 뒤를 이어서 이름을 붙이면 됩니다.

### 인스턴스 메서드

클래스 내부에서 선언한 메서드를 인스턴스 메서드라고 합니다.
인스턴스 메서드는 실제로 자바 스크립트의 prototype 에 선언되므로, 프로토타입 메서드로 불리기도 합니다.

```javascript
class Car {
  constructor(name) {
    this.name = name;
  }

  // 인스턴스 메서드 정의
  hello() {
    console.log(`안녕하세요, ${this.name} 입니다.`);
  }
}
```

Car 라는 class 선언 후, 내부에 hello 라고 하는 인스턴스 메서드를 정의.
인스턴스 메서드는 다음과 같이 선언 가능.

```javascript
const myCar = new Car("자동차");
myCar.hello(); // 안녕하세요, 자동차입니다.
```

이렇게 hello 으로 접근할 수 있는 이유는, 프로토타입 메서드라고 불리는 이유, 즉 메서드가 prototype 에 선언됐기 떄문입니다.

```javascript
const myCar = new Car("자동차");
Object.getPrototypeOf(myCar); // { constructor: f, hello: f }
```

Object.getPrototypeOf 를 사용하면, 인수로 넘겨준 변수의 prototype 를 확인가능 합니다.

이에 대한 결과로 { constructor: f, hello: f } 반환 받아, Car 의 prototype 을 받는 것을 짐작 가능합니다.

```javascript
Object.getPrototype(myCar) === Car.prototype; // true
```

Object.getPrototypeOf(myCar)를 Car.prototype 과 비교한 결과 true 가 반환되는 것을 볼 수 있음.
Object.getPrototypeOf 외에도 해당 변수의 prototype 을 확인할 수 있는 방법은 몇 가지가 더 있습니다.

### 정적 메서드

클래스의 인스턴스가 아닌 이름으로 호출할 수 있는 메서드입니다.

```javascript
class Car {
  static hello() {
    console.log("안녕하세요!");
  }
}
const myCar = new Car();

myCar.hello(); // Uncaught TypeError: myCar.hello is not a function
Car.hello(); // 안녕하세요!
```

정적 메서드 내부의 this 는 클래스로 생성된 인스턴스가 아닌, 클래스 자신을 가리키기 떄문에 다른 메서드에서 일반적으로 사용하는 this 을 사용할 수 없다.

이런 이유로 리액트 Class Component 생명주기 메서드인 static getDerivedStateFromProps(props, state) 에서는 this.state 에 접근 불가능합니다.

정적 메서드는 비록 this 에 접근할 수 없지만, 인스턴스를 생성하지 않아도, 사용할 수 있다는 점.

그리고 생성하지 않아도, 접근할 수 있기 때문에, 객체를 생성하지 않더라도 여러 곳에서 재사용이 가능하다는 장점이 있습니다.

이 떄문에, 애플리케이션 전역에서 사용하는 유틸 함수를 정적 메서드로 많이 활용하는 편입니다.

### 상속

클래스 컴포넌트를 만들기 위해서 extends React.Component 또는 extends React.PureComponent -> extends 는 기존 Class를 상속받아서, 자식 Class 에서 이 상속받은 Class 를 기반으로 확장하는 개념이라 볼 수 있음.

```javascript
class Car {
  constructor(name) {
    this.name = name;
  }

  hook() {
    console.log(`${this.name} 경적이 울립니다`);
  }
}

class Truck extends Car {
  constructor(name) {
    // 부모 클래스와 constructor, 즉 Car 의 constructor 를 호출한다.
    super(name);
  }
  load() {
    console.log("짐을 싣습니다.");
  }
}

const myCar = new Car("자동차");
myCar.hook(); // 자동차 경적이 울립니다!

const truck = new Truck("트럭");
truck.hook(); // 트럭 경적이 울립니다!
truck.load(); // 짐을 싣습니다!
```

Car 를 extends 한 Truck 이 생성한 객체에서도, Truck 이 따로 정의되지 않는 hook 메서드를 사용할 수 있는 것을 볼 수 있습니다.

### 클로저

리액트 클래스 컴포넌트는 자바스크립트 Class, 프로토타입, this 에 담겨 있음.
함수 컴포넌트는 클로저에 담겨 있습니다.

함수 컴포넌트의 구조와 작동 방식, 훅의 원리, 의존성 배열 등 함수 컴포넌트 대부분 기술이 모두 클로저에 담겨 있습니다.

#### 클로저 정의

리액트 함수 컴포넌트와 훅이 등장, 클로저라는 개념이 리액트에서 적극적으로 사용되기 시작하면서, 클로저를 뺴놓고서는 리액트가 어떤 식으로 작동하는 지 어려워졌습니다.

```javascript
function add() {
  const a = 10;
  function innerAdd() {
    const b = 20;
    console.log(a + b);
  }
  innerAdd();
}
add();
```

innerAdd 함수는 add 내부에 선언돼 있어, a 를 선언할 수 있음.

#### 변수의 유효 범위, 스코프

클로저를 이해하기 위해서는 변수의 유효 범위에 따라 어휘적 환경이 결정된다고 볼 수 있음.
-> 변수의 유효 범위를 스코프라고 하는데, 자바스크립트에는 다양한 스코프가 있습니다.

#### 전역 스코프

전역 레벨을 선언하는 것을 전역 스코프라고 합니다.
전역(global) 이라는 이름에서 알 수 있듯이, 이 스코프에서 변수를 선언하면, 어디서든 호출 가능.

브라우저 환경에서 전역 객체를 window, Node 환경에서는 global 있습니다.
이 객체에 전역 레벨에서 선언한 스코프가 바인딩됩니다.

```javascript
var global = "global scope";

function hello() {
  console.log(global);
}

console.log(global); // global scope
hello();
console.log(global === window.global); // true
```

#### 함수 스코프

다른 언어와 달리 자바스크립트는 기본적 함수 레벨 스코프를 따릅니다.
즉, {} 블록이 스코프 범위를 결정하진 않습니다.

```javascript
if (true) {
  var global = "global scope";
}

console.log(global);
console.log(global === window.global);
```

```javascript
var x = 10;

function foo() {
  var x = 100;
  console.log(x); // 100

  function bar() {
    var x = 1000;
    console.log(x); // 1000
  }
  bar();
}
console.log(x); // 10
foo();
```

자바스크립트 스코프는, 가장 가까운 스코프에서 변수에 존재하는지 확인.

#### 클로저 활용

자바스크립트는 함수 레벨 스코프를 가지고 있으므로,
선언된 함수 레벨 스코프를 활용해 어떤 작업을 할 수 있다는 개념이 바로 클로저라는 것을 어림풋이 알게 됐습니다.

```javascript
function outerFunction() {
  var x = "hello";

  function innerFunction() {
    console.log(x);
  }

  return innerFunction;
}

const innerFunction = outerFunction();
innerFunction(); // 'hello';
```

전역 스코프는 어디든지 원하는 값을 꺼내올 수 있다는 장점이 있음.
반대로는 누구든 접근해서 수정 할 수 있다는 뜻이 됩니다.

```javascript
var counter = 0;

function handleClick() {
  counter++;
}
```

counter 라는 큰 문제가 있음.
-> 전역 레벨에서 선언돼 있어서, 누구나 수정이 가능.
window.counter 활용하면 쉽게 해당 변수에 접근이 가능합니다.

만약에 useState 가 변수 전역 레벨에 저장돼 있으면, 리액트 애플리케이션을 쉽게 망가뜨릴 수 있습니다.
따라, 리액트가 관리하는 내부 상태 값은 리액트가 별도의 관리하는 클로저 내부에서만 접근이 가능합니다.

### 리액트에서 클로저

리액트에서는 useState 훅에서 클로저의 원리를 사용하고 있습니다.

```javascript
function Component() {
  const [state, setState] = useState();

  function handleClick() {
    setState((prev) => prev + 1);
  }
}
```

### 주의할 점

```javascript
for (var i = 0; i < 5; i++) {
  setTimeout(function () {
    console.log();
  }, i * 1000);
}
```

var 는 전역 변수로 작동하므로, for 문의 존재와 상관없이 해당 스코프를 바라보고 있으므로,
함수 내부 실행이 아니라면, 전역 스코프에 var i에 등록돼 있을 것 입니다.

즉 var 가 아닌, let 을 사용하면, let 은 블록 레벨 스코프를 가지므로 let i 가 for문을 순회하면서 각각의 스코프를 얻습니다 .

### 정리

클로저는 즉 외부 함수를 기억하고 이를 내부 함수에서 가져다 쓰는 메커니즘은 성능에 영향을 미칩니다.
클로저에 꼭 필요한 작업만 남겨두지 않으면, 메모리를 불필요하게 잡아먹고, 마찬가지로 클러저 사용을 적절한 스코프로 가둬두지 않는다면 성능에 약영향을 미치게 됩니다.

### 이벤트 루프와 비동기 통신의 이해

자바스크립트는 싱글 스레드에서 동작.
자바스크립트는 한 번에 하나의 작업한 동기 작업으로 처리 가능. 즉 직렬 처리 작업 처리.

## 싱글 스레드 자바스크립트

과거에 프로그램을 실행하는 단위가 프로세스였음.
프로세스를 구동해 프로그램이 메모리상에서 실행되는 작업 단위를 의미.

소프트에서 점차 복잡해지면서, 하나의 프로그램에서 동시에 여러 개의 복잡한 작업을 수행할 필요성이 대두됐음.
하지만 하나의 프로그램에서 하나의 프로세스만 할당하므로, 이러한 작업을 수행하기 어려웠는데, 그래서 더 작은 실행 단위가 바로 스레드 입니다.

하나의 프로세스에 여러 개의 스레드를 만들 수 있음.

그러면 왜 자바스크립트는 싱글 스레드로 만들어졌을까? -> 멀티 스레드의 장점이 있지만, 내부적으로 처리가 복잡합니다.

스레드 하나는 프로세스에서 동시에 서로 같은 자원에 접근 가능, 동시에 여러 작업을 수행하다 보면, 같은 자원에 대해 여러 번 수정하는 등 동시성 문제가 발생할 수 있어, 이에 대한 처리가 필요합니다.
또한 각각 격리돼 있는 프로세스와는 다르게, 하나의 스레드가 문제가 생기면, 같은 자원을 공유하는 다른 스레드에서 동시에 문제가 일어날 수 있습니다.

자바스크립트는 초기 만들어졌을 떄, 단순 처리를 하기 위해서 싱글 스레드 설계로 설계 됐었는데,
지금은 자바스크립트로 다양한 작업을 처리하면서, 자바스크립트 설계 자체가 바뀔 때가 됐다고 이야기를 하는 사람이 많아졌습니다.

하나의 작업이 끝나기 전에, 다른 작업을 수행하지 못한다면, 웹 페이지가 멈춘 것 같은 느낌을 줄 수 있음 -> 비동기식이 생김.

### 이벤트 루프

이벤트 루프는 자바스크립트 런타임 외부에서, 자바스크립트의 비동기 실행을 돕기 위해 만들어진 장치.

### 호출 스택과 이벤트 루프

호출 스택은 자바스크립트에서 수행해야 할 코드나 함수를 순차적으로 담아두는 스팩.

```javascript
function bar() {
  console.log("bar");
}
function baz() {
  console.log("baz");
}
function foo() {
  console.log("foo");
  bar();
  baz();
}
foo();
```

foo 를 호출 후, 내부 bar, baz 순차적으로 호출 구조.
이 호출 스택이 비어 있는 지 여부를 확인하는 것이 이벤트 루프.

이벤트 루프는 단순히 이벤트 루프만 단일 스레드 내부에서 이 호출 스택 내부에 수행해야 할 작업이 있는지 확인하고, 수행해야 할 코드는 있다면 자바스크립트 엔진을 이용해 실행합니다.

-> 코드를 실행하는 것, 호출 스택이 비어 있는지 확인하는 것 모두 단일 스레드에서 일어납니다.

```javascript
function bar() {
  console.log("bar");
}

function baz() {
  console.log("baz");
}

function foo() {
  console.log("foo");
  setTimeout(bar(), 0);
  baz();
}
foo();
```

1. foo() 가 호출 스택에 먼저 들어간다.
2. foo() 내부에 console.log 존재하므로 호출 스택에 들어갑니다.
3. 2의 실행이 완료된 이후에 다음 코드로 넘어감. (아직 foo()는 존재)
4. setTimeout(bar(), 0) 호출 스택에 들어감.
5. 4번 대해 타이머 이벤트가 실행되어 태스크 큐로 들어가고, 그 대신 바로 스택에 제거된다.
6. baz() 가 호출 스택에 들어간다.
7. baz() 내부에 console.log 존재하므로, 호출 스택에 들어간다.
8. 7의 실행이 완료된 이후에 다음 코드로 넘어간다 (아직 foo, baz는 존재)
9. 더 이상 baz()에 남는 것이 없어 호출 스택애서 제거됨 (아직 foo()는 존재)
10. 더 이상 foo() 에 남는 것이 없으므로 호출 스택에서 제거된다.
11. 이제 호출 스택이 완전히 비워짐.
12. 이벤트 루프가 호출 스택이 비워져 있다는 것을 확인, 그리고 태스트 큐를 확인하니 4번에 들어갔던 내용이 있어 baz() 호출 스택에 들여보냅니다.
13. bar() 내부에 console.log 존재하므로 호출 스택에 들어감.
14. 13의 실행이 끝나고, 다음 코드로 넘어갑니다. (아직bar()존재)
15. 더 이상 bar() 에 남은 것이 없으므로 호출 스택에 제거됩니다.

태스트 큐 : 실행해야 할 태스크의 집합, 이벤트 루프는 이러한 태스트 큐를 한 개 이상 가지고 있습니다.
태스트 큐는 자료 구조의 큐가 아니고 set 형태를 지니고 있습니다. 왜냐 선택된 큐 중 실행 가능한 가장 오래된 태스크를 가져와야 하기 떄문입니다.

태스크 큐에서 의미하는 `실행해야 할 태스크`라는 것은 비동기 함수의 콜백 함수나 이벤트 핸들러 등을 의미.

즉 이벤트 루프의 역할은 호출 스택에 실행 중인 코드가 있는지, 그리고 태스크 큐에 대기 중 함수가 있는 지 반복해서 확인하는 역할을 합니다.
-> 호출 스택이 비었다면, 태스크 큐에 대기 중인 작업이 있는지 확인하고, 이 작업을 실행 가능한 오래된 것부터 순차적으로 꺼내와서 실행하게 됩니다.

### 태스크 큐와 마이크로 태스트 큐

이벤트 루프는 하나의 마이크로 태스크 큐를 갖고 있는데, 기존의 태스크 큐와는 다른 태스크를 처리합니다.
마이크로 태스크에는 대표적으로 Promise가 있습니다.
`마이크로 태스트 큐는 기존 태스트 큐보다 우선순위를 가지고 됩니다.`
즉, setTimeout, setInterval 은 Promise 보다 늦게 실행이 됩니다.

```javascript
// 태스크 큐
setTimeout, setInterval, setImmediate;
// 마이크로 태스트 큐
process.nextTick, Promises, queueMicroTask, MutationObserver;
```

### 정리

자바스크립트 코드를 실행하는 자체는 싱글 스레드에서 이루어져셔, 비동기를 처리하기 어렵지만.
자바스크립트 코드를 실행하는 것 외에, 태스트 큐, 이벤트 루프, 마이크로 태스트 큐, 브라우저/Node.js API 등이 적절한 생태계를 이루고 있기 떄문에, 싱글 스레드로는 불가능한 비동기 이벤트 처리가 가능해졌습니다.
