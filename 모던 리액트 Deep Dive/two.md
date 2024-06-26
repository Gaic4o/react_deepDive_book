# 리액트 필수 개념

### JSX 란?

JSX 는 JSXElement, JSXAttributes, JSXChildren, JSXStrings 4가지 컴포넌트로 구성되어 있습니다.

리액트에서 JSX의 모든 구문을 다 활용할 필요가 없다고 판단해서, 문법에는 실제로 있지만, 실제 리액트에서 사용하지 않는 것들이 있습니다. -> 네임스페이스를 가진 태그, ref 속서을 이용한 문자열 참조, 조건부 연산자를 통한 논리적 조건 처리 등이 있음.

### 가상 DOM 과 리액트 파이버

#### 가상 DOM

가상 DOM : 가상 DOM은 실제 DOM의 복사본입니다. 실제 DOM을 직접 조작하는 것은 성능에 부담을 줄 수 잇으므로, 리액트에서는 변경사항을 먼저 가상 DOM 에 적용합니다.
가상 DOM의 장점 : 변경사항이 발생하면, 리액트에서는 가상 DOM에 있는 현재 상태와 변경 후 상태를 비교하여 실제로 업데이트가 필요한 부분만을 실제 DOM 에 반영합니다.

#### React Fiber

- React Fiber 의 도입 배경 : 리액트 개발팀의 애플리케이션의 반응성을 높이고, 애니메이션, 레이아웃, 제스처와 같은 인터랙티브한 작업의 처리 성능을 개선하기 위해 React Fiber를 도입합니다.

- Fiber의 핵심 개념 : React Fiber 는 리액트의 새로운 재조정 엔진. 기존의 동기적인 재조정 과정을 비동기적으로 만들어 작업을 중단하고, 필요에 따라 다시 시작하거나 우선순위를 변경할 수 있게 합니다.

- 작업의 우선순위 부여 : Fiber 는 애플리케이션에서 발생하는 업데이트를 여러 작은 단위로 나누어 처리. 이러한 작은 단위의 작업들은 필요에 따라 우선순위가 부여되며, 긴급도에 따라 작업의 순위를 조정 가능.

#### 파이버 트리

파이버 트리의 구조 : 리액트는 현재 화면에 보이는 컴포넌트의 상태를 관리하기 위해 두 개의 파이버 트리(현재 트리와 작업 중인(work-in-progress 트리) 를 유지합니다. 작업이 완료되면, 두 트리의 역할이 교체됩니다.

효율적인 업데이트 : 이 구조를 통해 리액트의 애플리케이션의 상태 변경을 효율적으로 가능, 변경사항이 발생하면, 리액트는 새로운 상태에 기반한 새로운 파이버 트리를 구축 해, 변경이 필요한 부분만을 실제 DOM 에 반영합니다.

#### 정리

가상 DOM은 변경사항을 효율적으로 처리하기 위한 리액트의 전략이며, React Fiber는 이러한 전략을 한 단계 더 발전시킨 재조정 엔진입니다. Fiber는 애플리케이션의 성능을 개선하고, 개발자가 인터랙티브하고 반응성 높은 사용자 인터페이스를 구현할 수 있도록 돕습니다. 따라서, Fiber는 단순히 성능 최적화를 넘어서 리액트 애플리케이션의 기능성과 사용자 경험을 향상시키는 중요한 역할을 합니다.

## 클래스 컴포넌트의 생명주기 메소드

- 마운트(mount) : 컴포넌트가 DOM에 처음으로 삽입되는 과정(생명주기가 시작되는 순간)
- 업데이트(update) : 이미 생성된 컴포넌트의 내용이 변경(업데이트)되는 시점
- 언마운트(unmount) : 컴포넌트가 더 이상 존재하지 않는 시점

render() : Class 컴포넌트에서 필수값, 컴포넌트가 UI를 렌더링하기 위해서 쓰임.
componentDidMount() : 컴포넌트가 마운트되고 즉시, 호출되는 것이 DidMount().
componentDidUpdate() : 컴포넌트 업데이트가 일어난 이후 바로 실행.
componentWillUnmount() : 컴포넌트가 언마운트되거나, 더 이상 사용하지 않기 직전에 호출.
shouldComponentUpdate() : state나 props의 변경으로 리액트 컴포넌트가 다시 리렌더링되는 것을 막고 싶다면, 생명주기 메서드를 사용하면 됩니다.
componentDidCatch() : 자식 컴포넌트에서 에러가 발생했을 떄 실행됩니다.

#### 클래스 컴포넌트의 한계

- 데이터 추적 힘듬 : 생명주기 메서드를 적용하면, state 흐름을 추적하기 힘듬.
- 애플리케이션 내부 로직이 재사용이 어렵다.
- 기능이 많이질수록 컴포넌트 크기가 커진다.

### 함수 컴포넌트

React 16.8 버전에서 소개된 Hooks를 사용하여 상태 관리와 생명주기 기능을 구현할 수 있게 되면서, 클래스 컴포넌트를 대체할 수 있는 간결하고 효율적인 방법으로 자리잡았습니다. 함수 컴포넌트는 보통 더 짧은 코드로 작성되며, this 바인딩이 필요 없고, 코드의 재사용 및 테스트가 용이하다는 장점이 있습니다.

### 렌더링

리액트의 렌더링 프로세스는 크게 렌더 단계와 커밋 단계로 나눌 수 있습니다. 렌더 단계에서는 컴포넌트의 변화를 감지하고 가상 DOM에 반영합니다. 이 단계에서는 실제 DOM에 아직 반영되지 않으므로, 사용자는 변화를 볼 수 없습니다. 커밋 단계에서는 렌더 단계에서 계산된 변화를 실제 DOM에 적용하여 사용자에게 보여줍니다.

가상 DOM은 실제 DOM의 빠른 추상화를 가능하게 합니다. 컴포넌트의 상태가 변경될 때마다 전체 UI를 가상 DOM에 재렌더링하고, 이전 가상 DOM과 비교하여 실제 DOM에 필요한 최소한의 변경만을 적용함으로써 성능을 최적화합니다.

React Fiber는 리액트의 렌더링 엔진의 재구현으로, 애플리케이션의 반응성을 향상시킵니다. 작업을 더 작은 단위로 나누어 처리하고, 우선순위를 부여하여 필요한 작업만을 선별적으로 수행함으로써, 사용자 인터랙션과 애니메이션의 성능을 개선합니다.

### 무조건 메모이제이션을 적용 x, 필요한 곳에만 적용하자

메모이제이션은 리액트에서 성능 최적화를 위해 사용되는 중요한 기술 중 하나입니다. 그러나 모든 컴포넌트에 무분별하게 적용하는 것이 항상 좋은 것은 아닙니다. 이유는 메모이제이션을 사용하면 계산된 결과를 메모리에 저장하기 때문에, 메모리 사용량이 증가할 수 있기 때문입니다. 또한, 메모이제이션으로 인한 성능 이점이 실제 렌더링 비용을 상쇄하지 못하는 경우도 있습니다.

### 렌더링 과정은 비용이 비싸다, 모조리 메모리제이션해 버리자

필요한 곳에만 적용: 렌더링 비용이 높거나 자주 업데이트되는 컴포넌트에 대해서만 메모이제이션을 고려해야 합니다. 불필요하게 메모이제이션을 적용하면 오히려 메모리 사용량이 증가하여 성능에 부정적인 영향을 줄 수 있습니다.

useMemo와 useCallback의 신중한 사용: useMemo와 useCallback 훅은 계산된 값을 메모리에 저장하거나 함수를 재사용하기 위해 사용됩니다. 이들은 주로 다른 컴포넌트로 props를 통해 전달되는 값이나 함수에 적용될 때 유용합니다. 그러나 모든 상황에서 사용할 필요는 없으며, 실제로 성능 이점을 제공하는 경우에만 사용해야 합니다.
