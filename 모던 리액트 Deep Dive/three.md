# 리액트 훅 깊게 살펴보기

### useState

#### useState와 클로저

`useState` 는 함수 컴포넌트 내부에서 상태 관리를 하기 위해 사용됩니다.
클러저 기능을 이용해 useState 에서는 setState 으로 상태 값을 기억하고, 변경할 수 있게 해줍니다.
이는 React 가 함수형 메커니즘으로 렌더링 떄마다 값을 유지할 수 있는 메커니즘입니다.

#### 게으른 초기화(Lazy Initialization)

`useState`에 함수를 전달하는 것을 게으른 초기화라고 합니다.
이 방법은 상태의 초기값 설정이 복잡한 계산을 필요로 할 떄 사용되며, 해당 초기화 함수는 컴포넌트가 마운트될 떄 마다 한 번만 호출이 됩니다.
이는 React 에서 불필요한 연산을 줄이고, 성능을 최적화하는 데 도움을 줍니다.

```javascript
export default function App() {
  const [state, setState] = useState(() => {
    console.log("복잡한 연산...");
    return 0;
  });

  function handleClick() {
    setState((prev) => prev + 1);
  }

  return (
    <div>
      <h1>{state}</h1>
      <button onClick={handleClick}>+</button>
    </div>
  );
}
```

위 예에서 볼 수 있듯이, `useState`로 전달된 함수`(() => { console.log("복잡한 연산..."); return 0; })`는 컴포넌트가 최초로 렌더링될 때만 실행됩니다. 이후의 렌더링에서는 이 함수를 무시하고, 이전 상태 값을 기반으로 작동합니다.

##### 게으른 초기화의 장점

- 성능 최적화 : 초기 상태가 복잡한 계산을 필요로 할 떄, 컴포넌트의 리렌더링 떄마다 해당 계산을 반복하지 않아도 됩니다. 이는 특히 초기값이 외부 데이터 소스에서 오는 경우 더욱 유용합니다.
- 리소스 절약 : 저장 공간(예: localStorage, sessionStorage) 접근이나 복잡한 데이터 처리와 같은 비용이 많이 드는 작업을 최소화합니다.

### useEffect

`useEffect`는 React에서 부수 효과(side effect)를 처리하기 위해 사용되는 훅 입니다.
이 훅은 두 개의 인수를 받습니다. 하나는 부수 효과를 실행하는 콜백 함수, 다른 하나는 의존성 배열 입니다.

```javascript
function Component() {
  useEffect(() => {}, [props, state]);
}
```

첫 번쨰 인수로는 실행할 부수 효과가 포함된 함수, 두 번쨰 인수로는 의존성 배열을 전달.
의존성 배열은 어느 정도 길이를 가진 배열, 아무런 값도 없는 빈 배열일 수도, 배열 자체를 넣지 않고 생략할 수 있습니다.

```javascript
function Component() {
  const counter = 1;

  useEffect(() => {
    console.log(counter);
  });

  return (
    <>
      <h1>{counter}</h1>
      <button onClick={handleClick}>+</button>
    </>
  );
}
```

useEffect 는 자바스크립트의 proxy 나 데이터 반딩인, 옵저버 같은 특별 기능을 통해 값의 변화를 관찰하는 것이 아니고, 렌더링할 떄마다 의존성이 있는 값을 보면서 이 의존성이 값이 이전과 다른 게 하나라도 있으면 부수 효과를 실행하는 평범한 함수라 볼 수 있습니다.

따라 useEffect 나 state 와 props 의 변화 속에서 일어나는 렌더링 과정에 실행되는 부수 효과 함수라고 볼 수 있습니다.

```javascript
useEffect(() => {
  function addMouseEvent() {
    console.log();
  }

  window.addEventListener("click", addMouseEvent);

  // 클린업 함수
  return () => {
    console.log("클린업 함수 실행!", 1);
    window.removeEventListener("click", addMouseEvent);
  };
}, [counter]);
```

useEffect에 이벤트를 추가했을 떄 클린업 함수에서 지워야 하는 지 알 수 있음.
함수 컴포넌트의 useEffect는 콜백이 실행될 떄 마다 이전의 클린업 함수가 존재한다면 그 클린업 함수를 실행한 뒤에 콜백을 실행합니다.
따라 이벤트를 추가하기 전 이전에 등록했던 이벤트 핸들러를 삭제하는 코드를 클린업 함수에 추가하는 것.
이렇게 함으로 특정 이벤트의 핸들러가 무한히 추가되는 것을 방지할 수 있습니다.

언마운트는 특정 컴포넌트가 DOM에서 사라진다는 것을 의미하는 클래스 컴포넌트 용어입니다.
클린업 함수는 언마운트라기 보다는 함수 컴포넌트가 리렌더링됐을 떄 의존성 변화가 있었을 당시 이전의 값을 기준으로 실행되는, 말 그대로 이전 상태를 청소해 주는 개념으로 보는 것이 옳습니다.

### 의존성 배열

의존성 배열은 보통 빈 배열을 두거나, 아예 아무런 값도 넘기지 않거나, 혹은 사용자가 직접 원하는 값을 넣어줄 수 있습니다.
만약 빈 배열을 둔다면 리액트 useEffect는 비교할 의존성이 없다고 판단해 최초 렌더링 직후에 실행된 다음부터는 더 이상 실행되지 않습니다.

```javascript
useEffect(() => {
  console.log("컴포넌트 렌더링");
});
```

```javascript
function Component() {
  console.log("렌더링됨");
}

function Component() {
  useEffect(() => {
    console.log("렌더링됨");
  });
}
```

1. useEffect 는 서버 사이드 렌더링 관점에서 클라이언트 사이드에서 실행되는 것을 보장해 줍니다.
2. useEffect 컴포넌트 렌더링 부수 효과, 즉 컴포넌트의 렌더링이 완료된 이후에 실행됩니다. 반면 직접 실행은 컴포넌트가 렌더링되는 도중이 실행됩니다. 1번과 달리 서버 사이드 렌더링의 경우 서버에서도 실행, 그리고 이 작업은 함수 컴포넌트의 반환을 지연시키는 행위, 즉 무거운 작업일 경우 렌더링을 방해하므로 성능에 악영향을 미칠 수 있습니다.

### useEffect 의 구현

```javascript
const MyReact = function () {
  const global = {};
  let index = 0;
};

function useEffect(callback, dependencies) {
  const hooks = global.hooks;

  // 이전 훅 정보가 있는지 확인한다.
  let previousDependencies = hooks[index];

  // 변경됐는지 확인
  // 이전 값이 있다면 이전 값을 얕은 비교로 비교해 변경이 일어났는지 확인한다.
  // 이전 값이 없다면 최초 실행이므로 변경이 일어난 것으로 간주해 실행을 유도한다.
  let isDependenciesChange = previousDependencies ? dependencies.some(
    (value, idx) => !Object.is(value, previousEnpendencies[idx]);
  ) : true

  if (isDependenciesChanges) {
    callback()
  }

  hooks[index] = dependencies

  index++;
}
return {useEffect}
})()
```

핵심은 의종성 배열이 이전 값과 현재 값의 얕은 비교입니다.
리액트의 값을 비교할 때 Object.is 를 기반으로 하는 얕은 비교를 수행합니다.

### useEffect를 사용할 떄 주의할 점

useEffect는 리액트 코드를 작성할 떄, 가장 많이 사용하는 훅이면서 가장 주의해야 할 훅이기도 합니다.
useEffect를 잘못 사용하면 예기치 못한 버그가 발생할 수 있으며, 심한 경우 무한 루프가 빠지기도 합니다.

#### eslint-disable-line react-hooks/exhaustive-deps 주석은 최대한 자제해라

리액트 코드를 보다 보면, eslint-disable-line, react-hooks/exhaustive-deps 주석을 사용해 ESLint 의 react-hooks/exhaustive-deps 룰에서 발생하는 경고를 무시하는 것을 볼 수 있습니다.
-> ESLint 룰은 useEffect 인수 내부에 사용하는 값 중 의존성 배열에 포함돼 있지 않은 값이 있을 떄 경고를 발생.

```javascript
useEffect(() => {
  console.log(props);
}, []); // eslint-disable-line react-hooks/exhaustive-deps
```

정말로 필요할 떄에는 사용할 수도 있지만 대부분의 경우 의도치 못한 버그를 만들 가능성이 큰 코드.
빈 배열 [] 의존성으로 할 떄, 즉 컴포넌트를 마운트하는 시점에만 무언가를 하고 싶다라는 의도로 작성하곤 합니다.
그러나 이 클래스 컴포넌트의 생명주기 메서드인 componentDidMount 에 기반한 접근법으로, 가급적으로 사용하면 안됩니다.

useEffect 는 반드시 의존성 배열로 전달한 값의 변경에 의해 실행해야 하는 훅입니다.
그러나 의존성 배열을 넘기지 않은 채 콜백 함수 내부에서 특정 값을 사용해야 한다는 것은, 이 부수 효과가 실제로 관찰해서 실행되야 하는 값과는 별개로 작동한다는 것을 의미합니다.

즉, 컴포넌트의 state, props 와 같은 어떤 값의 변경과 useEffect 의 부수 효과가 별개로 작동하게 된다는 것 입니다.
useEffect 에서 사용한 콜백 함수의 실행과 내부에서 사용한 값의 실제 변경 사이에 연결 고리가 끊어져 있는 것 입니다

```javascript
function Component({ log }: { log: string }) {
  useEffect(() => {
    logging(log);
  }, []); // eslint-disable-line react-hooks/exhaustive-deps
}
```

위 코드는 log가 최초로 props로 넘어와서 컴포넌트가 최초로 렌더링된 시점에만 실행이 됩니다.
코드를 작성한 의도는 아마 해당 컴포넌트가 최초로 렌더링됐을 떄만 logging을 실행하고 싶어서 일 것.

그러나 위 코드에서는 당장 문제는 없지만, 버그 위험성이 있습니다.
log 가 아무리 변하더라도 useEffect 의 부수 효과는 실행되지 않고, useEffect의 흐름과 컴포넌트의 props.log 의 흐름이 맞지 않게 됩니다.

logging 이라는 작업은 log 를 props로 전달하는 부모 컴포넌트에서 실행되는 것이 옳을지도 모릅니다.
-> 부모 컴포넌트에서 Component 렌더링되는 시점을 결정, 이에 맞게 log 값을 넘겨준다면 useEffect 해당 주석을 제거해도 위 예제 코드와 동일한 결과를 만들 수 있습니다. Component 부수 효과 흐름을 거스르지 않을 수 있습니다.

#### useEffect의 첫 번쨰 인수에 함수명을 부여하라

useEffect 의 코드가 복잡해지고, 많아질수록 무슨 일을 하는 useEffect 코드인지 파악하기 힘들어집니다.

```javascript
useEffect(
  function logActiveUser() {
    logging(user.id);
  },
  [user.id]
);
```

useEffect 의 인수를 익명 함수가 아닌 적절 이름을 사용한 기명 함수로 바꾸는 것이 좋음.

```javascript
useEffect(()
function logActiveUser() {
  logging(user.id)
}, [user.id])
```

변수에 적절 이름을 붙이는 이유는 해당 변수가 왜 만들어졌는지 파악하기 위해서 만들어짐. useEffect에서도 적절한 이름을 붙이면, useEffect 의 파악하기 쉬워집니다.

#### 거대한 useEffect 만들지 않기.

useEffect 의존성 배열을 바탕으로 렌더링 시 의존성이 변경될 떄 마다 부수 효과를 실행합니다.
이 부수 효과의 크기가 커질수록 애플리케이션 성능에 악영향을 미칩니다.
만약 부득이 하게 큰 useEffect 을 만들어야 한다면, 적은 의존성 배열을 사용하는 여러 개의 useEffect 으로 분리하는 것이 좋습니다.

만약 의존성 배열이 너무 거대해지고, 관리하기 어려운 수준일 떄 정확히 이 useEffect 가 언제 발생하는 지 알 수 없게 됩니다.
만약에 의존성 배열에 불가피하게 여러 변수가 들어가야 하는 상황이라면, 최대한 useCallback useMemo 등으로 사전에 정세한 내용들만 useEffect 에 담아두는 것이 좋습니다.

#### useMemo

useMemo는 비용이 더 큰 연산에 대한 결과를 저장하고, 이 저장된 값을 반환하는 훅 입니다.
흔히 리액트에서 최적화를 떠올릴 떄 가장 먼저 언급되는 훅이 바로 useMemo 입니다.

```javascript
const memoizedValue = useMemo(() => expensiveComputation(a, b), [a, b]);
```

첫 번쨰 인수로는 어떠한 값을 반환하는 생성 함수, 두 번쨰 인수를 해당 함수가 의존하는 값이 배열을 전달.
useMemo 렌더링 발생 시 의존성 배열의 값이 변경되지 않았으면 함수를 재실행하지 않고 이전에 기억해 둔 해당 값을 반환합니다.
의존성 배열이 값이 변경됐다면, 첫 번쨰 인수의 함수를 실행한 후에 그 값을 반환하고 그 값을 다시 기억해 둘 것 입니다.
메모이제이션은 컴포넌트를 감쌀 수 도 있습니다. React.memo 을 쓰는 것이 더 현명합니다.

### useCallback

useCallback 은 특정 함수를 새로 만들지 않고, 다시 재사용하는 것.
useCallback 을 추가하면, 해당 의존성이 변경됐을 떄만 함수가 재생성되는 것을 볼 수 있습니다.

이처럼 함수의 재생성을 막아 불필요한 리소스 또는 리렌더링을 방지하고 싶을 떄는 useCallback 을 사용할 수 있습니다.

```javascript
export function useCallback(callback, args) {
  currentHook = 8;
  return useMemo(() => callback, args);
}
```

useMemo와 useCallback 차이점은 메모이제이션을 하는 대상이 변수냐, 함수냐일 뿐.
자바스크립트에서 함수 또한 값으로 표현될 수 있으므로, 이러한 코드는 매우 자연스럽다고 볼 수 있습니다.

다만 useMemo 로 useCallback 을 구현하는 경우, 불필요하게 코드가 길어지고 혼동을 야기할 수 잇으므로, 리액트에서 별도로 제공하는 것으로 추측해볼 수 있습니다.

```javascript
export default function App() {
  const [counter, setCounter] = useState(0);

  const handleClick1 = useCallback(() => {
    setCounter((prev) => prev + 1);
  }, []);

  const handleClick2 = useCallback(() => {
    setCounter((prev) => prev + 1);
  });
}
```

useCallback, useMemo 모두 동일 기능을 합니다.
다만 useMemo 는 값 자체를 메모이제이션하는 용도이기 떄문에 반환문으로 함수 선언문을 반환해야 합니다.

코드를 작성하거나 리뷰하는 입장에서 혼란을 야기할 수 있으므로, 함수를 메모이제이션하는 용도라면 더 간단한 useCallback 을 사용하는 것이 좋습니다.

### useContext

Context 란? -> 애플리케이션은 기본적 부모 컴포넌트와 자식 컴포넌트로 이뤄진 트리 구조를 갖고 있으므로, 부모가 가지고 있는 데이터를 자식에서도 사용하고 싶다면 props 으로 데이터를 상호작용해야 합니다.
하지만 컴포넌트 자식 부모 사이가 멀어질수록 props 으로 넘겨주기 힘들 수 있습니다. 이떄 context 를 사용하게 됩니다.

### Context 를 함수 컴포넌트에서 사용할 수 있게 해주느 useContext Hook

콘텍스트와 해당 콘텍스트를 함수 컴포넌트에서 사용할 수 있게 해주는 useContext는 다음과 같이 작성 가능.

```javascript
const Context = (createContext < { hello: string }) | (undefined > undefined);

function ParentComponent() {
  return (
    <>
      <Context.Provider value={{ hello: "react" }}>
        <Context.Provider value={{ hello: "javascript" }}>
          <ChildrenComponent />
        </Context.Provider>
      </Context.Provider>
    </>
  );
}

function ChildComponent() {
  const value = useContext(Context);

  return <>{value ? value.hello : ""}</>;
}
```

useContext 는 상위 컴포넌트에서 만들어진 Context 를 함수 컴포넌트에서 사용할 수 있도록 만들어진 훅입니다.
useContext 를 사용하면 상위 컴포넌트 어딘가에서 선언된 <Context.Provider />에서 제공한 값을 사용할 수 있게 됩니다.

만약 여러 개의 Provider 가 있다면, 가장 가까운 Provider 값을 가져오게 됩니다.
예제에서는 가까운 콘텍스트인 값인 javascript 가 반환됩니다.

컴포넌트 트리가 복잡해질 수록 콘텍스트를 사용하는 것도 만만치 않음.
useContext 로 원하는 값을 얻을려 했지만, 정적 컴포넌트가 실행될 떄 이 콘텍스트가 존재하지 않아 에러가 발생할 수 있음.
에러를 방지하려면 useContext 내부에서 해당 콘텍스트가 존재하는 환경인지, 즉 콘텍스트가 한 번이라도 초기화되어 값을 내려주고 있는 지 확인해 보면 됩니다.

```javascript
const MyContext = (createContext < { hello: string }) | (undefined > undefined);

function ContextProvider({
  children,
  text,
}: PropsWithChildren<{ text: string }>) {
  return (
    <MyContext.Provider value={{ hello: text }}>{children}</MyContext.Provider>
  );
}

function useMyContext() {
  const context = useContext(MyContext);
  if (context === undefined) {
    throw new Error(
      "useMyContext 는 ContextProvider 내부에서만 사용할 수 있습니다."
    );
  }
  return context;
}

function ChildComponent() {
  // 타입이 명확히 설정돼 있어서 굳이 undefined 체크를 하지 않아도 됩니다.
  // 이 컴포넌트가 Provider 하위에 없다면 에러가 발생할 것이다
  const { hello } = useMyContext();
  return <>{hello}</>;
}

function ParentComponent() {
  return (
    <>
      <ContextProvider text="react">
        <ChildComponent />
      </ContextProvider>
    </>
  );
}
```

다수 Provider 와 useContext를 사용할 떄, 특히 타입스크립트를 사용하고 있다면 위와 같은 함수로 감싸주는 것이 좋습니다.
타입 추론도 유용, 상위 Provider 가 없는 경우에도 사전에 쉽게 에러를 찾을 수 있습니다.

#### useContext 를 사용할 떄 주의할 점

useContext 를 함수 컴포넌트 내부에서 사용하면, 항상 컴포넌트 재활용이 어려워 집니다.
useContext 가 있는 컴포넌트는 그 순간부터 눈으로는 직접 보이지도 않을 수 있는 Provider 의존성을 갖게 됨.

useContext 를 사용하는 경우, 컴포넌트를 최대한 작게 하거나 혹은 재사용되지 않을 만한 컴포넌트에서 사용해야 합니다.

1. 어떠한 상태를 기반으로 다른 상태를 만들어 낼 수 있어야 한다.
2. 필요에 따라 이러한 상태 변화를 최적화 할 수 있어야 한다.

```javascript
const MyContext = createContext<{ hello } | undefined>(undefined)

function ContextProvider({
  children,
  text
}: PropsWithChildren<{ text: string }>) {
  return (
    <MyContext.Providr value={{ hello: text }}>{children}</MyContext.Provider>
  )
}

function useMyContext() {
  const context = useContext(MyContext)
  if (context === undefined) {
    throw new Error('useMyContext는 ContextProvider 내부에서만 사용가능 합니다.');
  }
  return context
}

function GrandChildComponent() {
  const { hello } = useMyContext()

  useEffect(() => {
    console.log('렌더링 GrandChildComponent')
  })

  return <h3>{hello}</h3>
}

function ChildComponent() {
  useEffect(() => {
    console.log('렌더링 ChildComponent')
  })

  return <GrandChildComponent />
}

function ParentComponent() {
  const [text, setText] = useState('');

  function handleChange(e: ChangeEvent<HTMLInputElement>) {
    setText(e.target.value)
  }

  useEffect(() => {
    console.log('렌더링된 ParentComponent')
  })

  return (
    <>
      <ContextProvider text="react">
        <input type="text" onChange={handleChange} />
        <ChildComponent>
      </ContextProvider>
    </>
  )
}
```

코드를 살펴보면, ParentComponent 에서 Provider 의 값을 내려주고, 이를 GrandChildComponent 에서 사용 중.
언뜻 보기에는 text가 변경되는 ParentComponent 와 이를 사용하는 GrandChildComponent만 렌더링될 것 같지만 그렇지 않습니다.
사실은 컴포넌트 트리 전체가 리렌더링이 되고 있습니다.

```javascript
렌더링 GrandChildComponent
렌더링 ChildComponent
렌더링 ParentComponent
```

이 렌더링 되는 것을 막고 싶다면, memo으로 ChildComponent 을 감싸면 됩니다.

#### useImperativeHandle

이 훅은 잘 사용되지 않음. 그럼에도 일부 사용 사례에서는 유용하게 사용됩니다.
이 훅을 이해하기 위해서는 forwardRef 을 숙지해야 합니다.

#### forwardRef 살펴보기

ref 는 useRef 에서 반환한 객체, 리액트 컴포넌트의 props 인 ref 에 넣어, HTMLElement 에 접근하는 용도로 흔히 사용됩니다.
key 와 마찬가지 ref도 리액트에서 컴포넌트의 props 로 사용할 수 있는 예약어로서 별도로 선언돼 있지 않아도 사용할 수 있음을 useRef 에서 확인 했습니다.

만약 이러한 ref 를 상위 컴포넌트에서 하위 컴포넌트로 전달하고 싶다면 어떻게 해야 할까요?
즉, 상위 컴포넌트에서는 접근하고 싶은 ref가 있지만 이를 직접 props 로 넣어 사용할 수 없을 떄는 어떻게 해야 할까?

단순한 ref와 props 에 대한 상식으로 이 문제를 해결한다면, 다음과 코드와 같은 결과물이 나올 것 입니다.

```javascript
function ChildComponent({ ref }) {
  useEffect(() => {
    // undefined
    console.log(ref);
  }, [ref]);

  return <div>안녕!</div>;
}

function ParentComponent() {
  const inputRef = useRef();

  return (
    <>
      <input ref={inputRef} />
      <ChildComponent ref={inputRef} />
    </>
  );
}
```

리액트에서 ref 는 props 로 쓸 수 없다는 경고문과 함꼐 접근을 시도할 경우, undefined 를 반환한다고 돼 있음.
만약 예약어로 지정된 ref 대신 다른 props 로 받으면 어떨까?

```javascript
function ChildComponent({ parentRef }) {
  useEffect(() => {
    console.log(parentRef);
  }, [parentRef]);

  return <div>안녕!</div>;
}

function ParentComponent() {
  const inputRef = useRef();

  return (
    <>
      <input ref={inputRef} />
      <ChildComponent parentRef={inputRef} />
    </>
  );
}
```

이러한 방식은 앞선 예제와 다르게 잘 작동하는 것으로 보임.
forwardRef 방금 작성한 코드와 동일 작업을 하는 리액트 API입니다.

#### useLayoutEffect

useEffect 와 동일한 모습으로 작동합니다.
useLayoutEffect 는 `모든 DOM이 변경 후에 useLayoutEffect 의 Callback 함수 실행이 동기적으로 발생` 합니다.

여기서 말하는 DOM 변경이란 렌더링이지, 브라우저에 실제로 해당 변경 사항이 반영되는 시점을 의미하는 것은 아닙니다. 실행 순서는 다음과 같습니다.

1. 리액트 DOM 업데이트
2. useLayoutEffect 실행
3. 브라우저에 변경 사항을 반영
4. useEffect 을 실행

useLayoutEffect 는 브라우저에 변경 사항이 반영되기 전에 실행되고,
useEffect 는 브라우저에 변경 사항이 반영된 이후에 실행이 됩니다.

그리고 동기적으로 발생한다는 것은 `useLayoutEffect` 의 실행이 종료될 떄까지 기다린 다음 화면을 그린다는 것.

즉 그래서 useLayoutEffect 가 완료될 떄까지 기다리기 떄문에, 컴포넌트가 잠시 동안 일시 중지되는 것과 같은 일이 발생하게 됩니다.

### 훅의 규칙

리액트 공식 문서에서 혹을 사용할 떄 어떤식으로 사용해야 하는지 규칙이 정리되어 있습니다.

1. 최상위에서만 훅을 호출해야 합니다. 반복문이나, 조건문, 중첩된 함수에 훅을 선언 시킬 수 없음.
2. 리액트 함수 컴포넌트, 사용자 정의 훅에서만 훅을 호출시킬 수 있고, 일반 자바스크립트 함수에서는 훅을 사용할 수 없습니다.

### 사용자 정의 훅과 고차 컴포넌트 중 무엇을 써야 할까?

컴포넌트를 만들면서, 재사용 로직을 작성하는 방식 외에도 리액트에서는 재사용할 수 있는 로직을 관리할 수 있는 2가지 방법이 있습니다.
사용자 정의 훅(custom hook), 고차 컴포넌트(higher order component)가 있습니다.

#### 사용자 정의 훅

사용자 정의 훅은 반드시 use 로 시작하는 함수를 만들어야 합니다.
리액트 훅의 이름이 use로 시작한다는 규칙이 있으며, 사용자 정의 훅도 이러한 규칙을 준수함으로써 개발 시 해당 함수가 리액트 훅이라는 것을 바로 인식할 수 있다는 강점이 있습니다.

#### 고차 컴포넌트

HOC 패턴을 말함. 컴포너늩 자체의 로직을 재사용하기 위한 방법.
고차 함수의 일종으로 리액트에서 컴포넌트로 사용하지 않더라도, 자바스크립트에서 고차 함수를 사용할 수 있음.

리액트에서 가장 유명한 고차 함수가 react.memo 입니다.

고차 함수의 사전적 정의는 함수를 인수로 받거나 결과로 반환하는 함수라고 정의돼 있습니다.

##### 고차 함수를 활용해 리액트 고차 컴포넌트 만들어보기

사용자 인증 정보에 따라서, 인증된 사용자에게는 개인화된 컴포넌트를, 그렇지 않은 사용자에게는 별도로 정의된 공통 컴포넌트를 보여주는 시나리오를 떠올려보자.

고차 함수의 특징에 따라 개발자가 만든 또 다른 함수를 반환할 수 있다는 점에서 고차 컴포넌트를 사용하면 매우 유용합니다.

```javascript
interface LoginProps {
  loginRequired?: boolean;
}

function withLoginComponent<T>(Component: ComponentType<T>) {
  return function (props: T & LoginProps) {
    const { loginRequired, ...restProps } = props;

  if (loginRequired) {
    return <>로그인이 필요합니다</>
  }
  return <Component {...(restProps as T)} />
  };
}

const Component = withLoginComponent((props: { value: string }) => {
  return <h3>{props.value}</h3>
})
```

주의 할점은, 여러 개의 고차 컴포넌트를 감쌀 수 있어, 이렇게 감쌀 경우 복잡성이 커질 수 있습니다.

### 사용자 정의 훅과 고차 컴포넌트 중 무엇을 써야 할까?

사용자 정의 훅이 필요한 경우
-> useEffect, useState 단순 리액트에서 제공하는 훅으로만 공통 로직을 격리한다면, 사용자의 훅을 사용하는 것이 좋습니다.

고차 컴포넌트를 사용해야 하는 이유 -> 앞선 예제에서 애플리케이션 관점에서 컴포넌트를 감추고 로그인을 요구하는 공통 컴포넌트를 노출하는 것이 좋을 수 있습니다.
혹은 에러 바운더리와 비슷하게 어떠한 특정 에러가 발생했을 떄 현재 컴포넌트 대신 에러가 발생했음을 알릴 수 있는 컴포넌트를 노출하는 경우도 있습니다.

-> 이런 기능들은 사용자 정의 훅으로는 표현하기 어렵습니다.
함수 컴포넌트의 반환값, 즉 렌더링의 결과물에도 영향을 미치는 공통 로직이라면, 고차 컴포넌트를 사용합시다.
