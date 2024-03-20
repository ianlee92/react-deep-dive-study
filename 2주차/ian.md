### 📅 2024년 3월 20일

# 📚 3장 리액트 훅 깊게 살펴보기

### p.189

- useState는 함수 컴포넌트 내부에서 상태를 정의하고, 이 상태를 관리할 수 있게 해주는 훅이다.

### p.194

- useState는 자바스크립트의 특징 중 하나인 클로저에 의존해 구현돼 있을 것이라는 사실을 짐작해 볼 수 있다.
- 클로저를 사용함으로써 외부에 해당 값을 노출시키지 않고 오직 리액트에서만 쓸 수 있었고, 함수 컴포넌트가 매번 실행되더라도 useState에서 이전의 값을 정확하게 쓸 수 있게 됐다.
- useState 인수로 특정한 값을 넘기는 함수를 인수로 넣어줄 수도 있다.
- useState에 변수 대신 함수를 넘기는 것을 게으른 초기화(lazy initialization)라고 한다.

### p.196

- localStorage나 sessionStorage에 대한 접근, map, filter, find 같은 배열에 대한 접근, 혹은 초깃값 계산을 위해 함수 호출이 필요할 때와 같이 무거운 연산을 포함해 실행 비용이 많이 드는 경우에 게으른 초기화를 사용하는 것이 좋다.

### p.197

- useEffect는 애플리케이션 내 컴포넌트의 여러 값들을 활용해 동기적으로 부수 효과를 만드는 메커니즘이다.
- 이 부수 효과가 '언제' 일어나는지보다 어떤 상태값과 함께 실행되는지 살펴보는 것이 중요하다.

### p.198

- useEffect는 state와 props의 변화 속에서 일어나는 렌더링 과정에서 실행되는 부수 효과 함수라고 볼 수 있다.

### p.201

- 함수 컴포넌트의 useEffect는 그 콜백이 실행될 때마다 이전의 클린업 함수가 존재한다면 그 클린업 함수를 실행한 뒤에 콜백을 실행한다.
- 따라서 이벤트를 추가하기 전에 이전에 등록했던 이벤트 핸들러를 삭제하는 코드를 클린업 함수에 추가하는 것이다.
- 이렇게 함으로써 특정 이벤트의 핸들러가 무한히 추가되는 것을 방지할 수 있다.
- 언마운트는 특정 컴포넌트가 DOM에서 사라진다는 것을 의미하는 클래스 컴포넌트의 용어다.
- 클린업 함수는 언마운트라기보다는 함수 컴포넌트가 리렌더링됐을 때 의존성 변화가 있었을 당시 이전의 값을 기준으로 실행되는, 말 그대로 이전 상태를 청소해 주는 개념으로 보는 것이 옳다.

### p.202

- useEffect의 effect는 컴포넌트의 사이드 이펙트, 즉 부수 효과를 의미한다는 것을 명심하자.
- useEffect는 컴포넌트가 렌더링된 후에 어떠한 부수 효과를 일으키고 싶을 때 사용하는 훅이다.

### useEffect를 사용할 때 주의할 점 (p.203 ~ 208)

- eslint-disable-line reacat-hooks/exhaustive-deps 주석은 최대한 자제하라
  - 컴포넌트의 state, props와 같은 어떤 값의 변경과 useEffect의 부수 효과가 별개로 작동하게 된다는 것이다.
  - useEffect에 빈 배열을 넘기기 전에는 정말로 useEffect의 부수 효과가 컴포넌트의 상태와 별개로 작동해야만 하는지, 혹은 여기서 호출하는 게 최선인지 한 번 더 검토해 봐야 한다.
  - 만약 특정 값을 사용하지만 해당 값의 변경 시점을 피할 목적이라면 메모이제이션을 적절히 활용해 해당 값의 변화를 막거나 적당한 실행 위치를 다시 한번 고민해 보는 것이 좋다.
- useEffect의 첫 번째 인수에 함수명을 부여하라
  - useEffect의 수가 적거나 복잡성이 낮다면 익명 함수를 사용해도 큰 문제는 없지만 코드가 복잡하고 많아질수록 무슨 일을 하는 useEffect 코드인지 파악하기 어려워진다.
  - 이때 useEffect의 인수를 익명 함수가 아닌 적절한 이름을 사용한 기명 함수로 바꾸는 것이 좋다. (해당 변수가 왜 만들어졌는지 파악, useEffect 목적 파악)
    ```tsx
    useEffect(
      function logActiveUser() {
        logging(user.id);
      },
      [user.id]
    );
    ```
- 거대한 useEffect를 만들지 마라
  - 만약 부득이하게 큰 useEffect를 만들어야 한다면 적은 의존성 배열을 사용하는 여러 개의 useEffect로 분리하는 것이 좋다.
  - 의존성 배열에 불가피하게 여러 변수가 들ㅇ러가야 하는 상황이라면 최대한 useCallback과 useMemo 등으로 사전에 정제한 내용들만 useEffect에 담아두는 것이 좋다.
- 불필요한 외부 함수를 만들지 마라
  - useEffect 외부에 있던 관련 함수를 내부로 가져오면 훨씬 간결하고 불필요한 의존성 배열도 줄일 수 있다.
  - useEffect의 인수로 비동기 함수를 지정할 수 없으나 내부에서 비동기 함수를 선언해 실행하거나 즉시 실행 비동기 함수를 만들어서 사용하는 것은 가능하다.
  - 하지만 비동기 useEffect는 state의 경쟁 상태를 야기할 수 있고 cleanup 함수의 실행 순서도 보장할 수 없기 때문에 개발자의 편의를 위해 useEffect에서 비동기 함수를 인수로 받지 않는다고 볼 수 있다.

### p.209

- useMemo는 렌더링 발생 시 의존성 배열의 값이 변경되지 않았으면 함수를 재실행하지 않고 이전에 기억해 둔 해당 값을 반환하고, 의존성 배열의 값이 변경됐다면 첫 번째 인수의 함수를 실행한 후에 그 값을 반환하고 그 값을 다시 기억해 둘 것이다.
- 메모이제이션은 단순히 값뿐만 아니라 컴포넌트도 가능하지만 useMemo보다 React.memo를 쓰는 것이 더 현명하다.

### p.210

- useMemo가 값을 기억했다면, useCallback은 인수로 넘겨받은 콜백 자체를 기억한다. 쉽게 말해 useCallback은 특정 함수를 새로 만들지 않고 다시 재사용한다는 의미다.

### p.215

- useCallback이나 useMemo는 모두 동일한 역할을 한다. 다만 useMemo는 값 자체를 메모이제이션하는 용도이기 때문에 반환문으로 함수 선언문을 반환해야 한다.
- 이는 코드를 작성하거나 리뷰하는 입장에서 혼란을 불러올 수 있으므로 함수를 메모이제이션하는 용도라면 좀 더 간단한 useCallback을 사용하자.
  ```tsx
  /**
   * 아래 두 함수의 작동은 동일하다.
   */
  const handleClick1 = useCallback(() => {
    setCounter((prev) => prev + 1);
  }, []);
  const handleClick2 = useMemo(() => {
    return () => setCounter((prev) => prev + 1);
  }, []);
  ```

### p.216

- useRef는 useState와 동일하게 컴포넌트 내부에서 렌더링이 일어나도 변경 가능한 상태값을 저장한다는 공통점이 있다.
- useRef와 useState의 차이점
  - useRef는 반환값인 객체 내부에 있는 current로 값에 접근 또는 변경할 수 있다.
  - useRef는 그 값이 변하더라도 렌더링을 발생시키지 않는다.

### p.217

- useRef의 가장 일반적인 사용 예는 바로 DOM에 접근하고 싶을 때일 것이다.
- useRef의 최초 기본값은 return 문에 정의해 둔 DOM이 아니고 useRef()로 넘겨받은 인수라는 것이다. useRef가 선언된 당시에는 아직 컴포넌트가 렌더링되기 전이라 return으로 컴포넌트의 DOM이 반환되기 전이므로 undefined다.

### p.220

- props 내려주기(props drilling)을 극복하기 위해 등장한 개념이 바로 콘텍스트(Context)다. 콘텍스트를 사용하면, 이러한 명시적인 props 전달 없이도 선언한 하위 컴포넌트 모두에서 자유롭게 원하는 값을 사용할 수 있다.
- useContext는 상위 컴포넌트에서 만들어진 Context를 함수 컴포넌트에서 사용할 수 있도록 만들어진 훅이다.

### p.222

- 콘텍스트는 상태를 주입해 주는 API다. 상태 관리 라이브러리가 되기 위해서는 어떠한 상태를 기반으로 다른 상태를 만들어 낼 수 있어야 하고, 필요에 따라 이러한 상태 변화를 최적화할 수 있어야 한다. 그러나 콘텍스트는 둘 중 어느 것도 하지 못한다.
- 단순히 props 값을 하위로 전달해 줄 뿐, useContext를 사용ㅇ한다고 해서 렌더링이 최적화되지는 않는다.

### p. 225

```tsx
const [state, dispatcher] = useReducer(reducer, initialState, init);
```

- useReducer는 useState의 심화 버전으로 볼 수 있다.
- 반환값은 useState와 동일하게 길이가 2인 배열이다.
  - state: 현재 useReducer가 가지고 있는 값.
  - dispatcher: state를 업데이트하는 함수. setState는 단순히 값을 넘겨주지만 여기서는 action을 넘겨준다는 점이 다르다.
- 인수는 useState와 달리 2개에서 3개의 인수를 필요로 한다.
  - reducer: useReducer의 기본 action을 정의하는 함수다.
  - initialState: useReducer의 초깃값을 의미한다.
  - init: useState의 인수로 함수로 넘겨줄 때처럼 초깃값을 지연해서 생성시키고 싶을 때 사용하는 함수다.(게으르 초기화) 이 함수가 없다면 두 번째 인수로 넘겨받은 기본값을 사용하게 될 것이다.

### p.229

- useReducer나 useState 둘 다 세부 작동과 쓰임에만 차이가 있을 뿐, 결국 클로저를 활용해 값을 가둬서 state를 관리한다는 사실에는 변함이 없다.
- 따라서 개발자는 필요에 맞게 취사선택해 사용하면 될 것이다.

### p.230

- ref를 상위 컴포넌트에서 하위 컴포넌트로 전달하고 싶으면 forwardRef를 사용한다. 어떤 props명으로 전달할지 모르고, 이에 대한 완전한 네이밍의 자유가 주어진 props보다는 forwardRef를 사용하면 좀 더 확실하게 ref를 전달할 것임을 예측할 수 있고 안정적으로 받아서 사용할 수 있다.

### p.231, 232

- useImperativeHandle은 부모에게서 넘겨받은 ref를 원하는 대로 수정할 수 있는 훅이다.
- 부모는 단순히 HTMLElement뿐만 아니라 자식 컴포넌트에서 새롭게 설정한 객체의 키와 값에 대해서도 접근할 수 있게 됐다.
- useImperativeHandle을 사용하면 이 ref의 값에 원하는 값이나 액션을 정의할 수 있다.

### p.234

- useLayoutEffect는 useEffect와 동일하나 모든 DOM의 변경 후에 동기적으로 발생한다. 항상 useLayoutEffect가 useEffect보다 먼저 실행된다.
- useLayoutEffect의 특징상 DOM은 계산됐지만 이것이 화면에 반영되기 전에 하고 싶은 작업이 있을 때와 같이 반드시 필요한 때만 사용하는 것이 좋다. (동기적으로 발생하므로 웹 애플리케이션 성능에 문제가 발생할 수 있기 때문.)
- DOM 요소를 기반으로 한 애니메이션, 스크롤 위치를 제어하는 등 화면에 반영되기 전에 하고 싶은 작업에 useLayoutEffect를 사용하면 useEffect를 사용했을 때보다 훨씬 더 자연스러운 사용자 경험을 제공할 수 있다.

### p.238

- 훅은 절대 조건문, 반복문 등에 의해 리액트에서 예측 불가능한 순서로 실행되게 해서는 안 된다.
- 항상 훅은 실행 순서를 보장받을 수 있는 컴포넌트 최상단에 선언돼 있어야 한다.
- 조건문이 필요하다면 반드시 훅 내부에서 수행해야 한다.

### p.242~245

- 고차 컴포넌트(HOC, Higher Order Component)는 컴포넌트 자체의 로직을 재사용하기 위한 방법이다.
- 고차 컴포넌트 기법으로 다양한 최적화나 중복 로직 관리를 할 수 있다. 리액트에서 가장 유명한 고차 컴포넌트는 React.memo다.
- props가 변경되지 않았고 변경되지 않았다는 것을 memo가 확인하고 이전에 기억한 컴포넌트를 그대로 반환하여 불필요한 렌더링 작업을 생략할 수 있게 됐다.
- useMemo를 사용할 경우 값을 반환받기 때문에 JSX 함수 방식이 아닌 {}을 사용한 할당식을 사용한다는 차이점이 있다. 혼선을 빚을 수 있으므로 목적과 용도가 뚜렷한 memo를 사용하는 편이 좋다.

### p.248

- 사용자 정의 훅이 use로 시작하는 이름을 사용했다면 리액트의 고차 컴포넌트도 마찬가지로 with로 시작되는 이름을 사용해야 한다.
- 고차 컴포넌트는 반드시 컴포넌트를 인수로 받게 되는데, 반드시 컴포넌트의 props를 임의로 수정, 추가, 삭제하는 일은 없어야 한다.
- 여러 고차 컴포넌트로 컴포넌트를 감쌀 경우 복잡성이 커지므로 최소한으로 사용하는 것이 좋다.

### 사용자 정의 훅 vs 고차 컴포넌트 (p.250~251)

- 단순히 컴포넌트 전반에 걸쳐 동일한 로직으로 값을 제공하거나 특정한 훅의 작동을 취하게 하고 싶다면 사용자 정의 훅을 사용하는 것이 좋다.

  ```tsx
  function HookComponent() {
    const { loggedIn } = useLogin();

    useEffect(() => {
      if (!loggedIn) {
        // do something..
      }
    }, [loggedIn]);
  }
  ```

- 렌더링의 결과물에도 영향을 미치는 공통 로직이라면 고차 컴포넌트를 사용하자.
- 고차 컴포넌트는 이러처럼 공통화된 렌더링 로직을 처리하기에 매우 훌륭한 방법이다.

  ```tsx
  cosnt HOCComponent = withLoginComponent(() => {
    // loggedIn state의 값을 신경 쓰지 않고 그냥 컴포넌트에 필요한 로직만 추가해서 간단해졌다.
    // loggedIn state에 따른 제어는 고차 컴포넌트에서 해줄 것이다.
    return <>안녕하세요.</>
  })
  ```

# 📚 5장 리액트와 상태 관리 라이브러리

### p.338, 339

- 페이스북 팀은 양방향이 아닌 단방향으로 데이터 흐름을 변경하는 것을 제안하는데 이것이 바로 Flux 패턴의 시작이다.
  - 액션(action): 어떠한 작업을 처리할 액션과 그 액션 발생 시 함께 포함시킬 데이터를 의미한다. 액션 타입과 데이터를 각각 정의해 이를 디스패처로 보낸다.
  - 디스패처(dispatcher): 액션을 스토어에 보내는 역할을 한다. 콜백 함수 형태로 앞서 액션이 정의한 타입과 데이터를 모두 스토어에 보낸다.
  - 스토어(store): 실제 상태에 따른 값과 상태를 변경할 수 있는 메서드를 가지고 있다. 액션의 타입에 따라 어떻게 이를 변경할지가 정의돼 있다.
  - 뷰(view): 리액트의 컴포넌트에 해당하는 부분, 스토어에 만들어진 데이터를 가져와 화면을 렌더링하는 역할을 한다.

### p.347

- useSWR의 첫 번째 인수로 조회할 API 주소를, 두 번째 인수로 조회에 사용되는 fetch를 넘겨준다.
- 첫 번째 인수인 API 주소는 키로도 사용되며, 이후에 다른 곳에서 동일한 키로 호출하면 재조회하는 것이 아니라 useSWR이 관리하고 있는 캐시의 값을 활용한다.

### p.348

```tsx
// Recoil
const counter = atom({ key: "count", default: 0 });
const todoList = useRecoilValue(counter);

// Jotai
const constAtom = atom(0);
const [count, setCount] = useAtom(countAtom);

// Zustand
const useCounterStore = create((set) => ({
  counter: 0,
  increase: () => set((state) => ({ count: state.count + 1 })),
}));
const count = useCounterStore((state) => state.count);

// Valtio
const state = proxy({ count: 0 });
const snap = useSnapshot(state);
state.count++;
```

### p.351

- useState를 useReducer로 구현하는 예제
- useReducer의 첫 번째 인수로는 reducer, 즉 state와 action을 어떻게 정의할지를 넘겨줘야 하는데 useState와 동일한 작동, 즉 T를 받거나 (prev: T) => T를 받아 새로운 값을 설정할 수 있게끔 코드를 작성했다.

  ```tsx
  type Initializer<T> = T extends any ? T | ((prev: T) => T) : never;

  function useStateWithUseReducer<T>(initialState: T) {
    const [state, dispatch] = useReducer(
      (prev: T, action: Initializer<T>) =>
        typeof action === "function" ? action(prev) : action,
      initialState
    );
    return [state, dispatch];
  }
  ```

- useReducer를 useState로 작성할 수 있다.

  ```tsx
  function useReducerWithUseState(reducer, initialState, initializer) {
    const [state, setState] = useState(
      initializer ? () => intializer(initialState) : initialState
    );

    const dispatch = useCallback(
      (action) => setState((prev) => reducer(prev, action)),
      [reducer]
    );

    return [state, dispatch];
  }
  ```

### p.355

- 업데이트되는 값을 가져오려면 상태를 업데이트하는 것뿐만 아니라 상태가 업데이트됐을 때 이를 컴포넌트에 반영시키기 위한 리렌더링이 필요하며 함수 컴포넌트에서 리렌더링을 하려면 다음과 같은 작업 중 하나가 일어나야 한다.
- useState, useReducer의 반환값 중 두 번째 인수가 어떻게든 호출된다. 두 번째 인수가 호출되면 리액트는 다시 컴포넌트를 렌더링한다.
- 부모 함수(부모 컴포넌트)가 리렌더링되거나 해당 함수(함수 컴포넌트)가 다시 실행돼야 한다.

### p.372

- Recoil과 Jotai는 Context와 Provider, 그리고 훅을 기반으로 가능한 작은 상태를 효율적으로 관리하는 데 초점을 맞추고 있다.
- Zustand는 리덕스와 비슷하게 하나의 큰 스토어를 기반으로 상태를 관리하는 라이브러리다. Recoil, Jotai와는 다르게 이 하나의 큰 스토어는 Context가 아니라 스토어가 가지는 클로저를 기반으로 생성되며, 이 스토어의 상태가 변경되면 이 상태를 구독하고 있는 컴포넌트에 전파해 리렌더링을 알리는 방식이다.

### 페이스북이 만든 상태 관리 라이브러리 Recoil (p.373~384)

- Recoil은 리액트를 만든 페이스북에서 만든 리액트를 위한 상태 관리 라이브러리다.
- Recoil을 사용하기 위해서는 RecoilRoot를 애플리케이션의 최상단에 선언해 둬야 한다.
- RecoilRoot에서 Recoil에서 생성되는 상태값을 저장하기 위한 스토어를 생성하는 것을 확인할 수 있다.
- useStoreRef로 ancestorStoreRef의 존재를 확인하는데, 이는 Recoil에서 생성되는 atom과 같은 상태값을 저장하는 스토어를 의미한다.
- useStoreRef가 가리키는 것은 다름 아닌 AppContext가 가지고 있는 스토어다.
  ```tsx
  const AppContext = React.createContext<StoreRef>({ current: defaultStore });
  const useStoreRef = (): StoreRef => useContext(AppContext);
  ```
- Recoil의 상태값은 RecoilRoot로 생성된 Context의 스토어에 저장된다.
- 스토어의 상태값에 접근할 수 있는 함수들이 있으며, 이 함수를 활용해 상태값에 접근하거나 상태값을 변경할 수 있다.
- 값의 변경이 발생하면 이를 참조하고 있는 하위 컴포넌트에 모두 알린다.
- atom은 상태를 나타내는 Recoil의 최소 상태 단위다.

  ```tsx
  // Atom 선언
  const statementsAtom = atom<Array<Statement>>({
    key: "statements",
    default: InitialStatements,
  });
  ```

- atom은 key 값을 필수로 가지며, 이 키는 다른 atom과 구별하는 식별자가 되는 필수 값이다.
- defulat는 atom의 초깃값을 의미한다. 이 값의 변화에 따라 컴포넌트를 리렌더링하기 위해 useRecoilValue와 useRecoilState를 사용한다.
- 컴포넌트는 Recoil에서 제공하는 훅을 통해 atom의 상태 변화를 구독(subscribe)하고, 값이 변경되면 forceUpdate 같은 기법을 통해 리렌더링을 실행해 최신 atom 값을 가져오게 된다.

### Recoil에서 영감을 받은, 그러나 조금 더 유연한 Jotai (p.384~391)

- Recoil의 atom 모델에 영감을 받아 만들어진 상태 관리 라이브러리다.
- Jotai는 상향식(bottom-up) 접근법을 취하고 있다. 이는 리덕스와 같이 하나의 큰 상태를 애플리케이션에 내려주는 방식이 아니라, 작은 단위의 상태를 위로 전파할 수 있는 구조를 취하고 있음을 의미한다.
- 리액트 Context의 문제점인 불필요한 리렌더링이 일어나는 문제를 해결하고자 설계돼 있으며 메모이제이션이나 최적화를 거치지 않아도 리렌더링이 발생되지 않도록 설계돼 있다.
- atom을 생성할 때마다 고유한 key를 필요로 했던 Recoil과는 다르게, Jotai는 atom을 생성할 때 별도의 key를 넘겨주지 않아도 된다.
- config이라는 객체를 반환하는데, 초깃값을 의미하는 init, 값을 가져오는 read, 값을 설정하는 write만 존재한다.
- Recoil과는 다르게 컴포넌트 루트 레벨에서 Context가 존재하지 않아도 되는데, Context가 없다면 Provider가 없는 형태로 기본 스토어를 루트에 생성하고 이를 활용해 값을 저장하기 때문이다.
- atom의 값은 store에 존재한다. store에 atom 객체 그 자체를 키로 활용해 값을 저장한다. 이러한 방식을 위해 WeakMap이라고 하는 자바스크립트에서 객체만을 키로 가질 수 있는 독특한 방식의 Map을 활용해 recoil과는 다르게 별도의 key를 받지 않아도 스토어에 값을 저장할 수 있다.
- rerenderIfChanged가 일어나는 경우는 넘겨받은 atom이 Reducer를 통해 스토어에 있는 atom과 달라지는 경우, subscribe를 수행하고 있다가 어디선가 이 값이 달라지는 경우다.
- 기본적인 API 외에도 localStorage와 연동해 영구적으로 데이터를 저장하거나, Next.js, 리액트 네이티브와 연동하는 등 상태와 관련된 다양한 작업을 Jotai에서 지원한다.
- Jotai는 사용자가 별도의 키를 관리할 필요가 없다. 각 값들을 관리할 수 있는 것은 객체의 참조를 통해 값을 관리하기 때문이다. 객체의 참조를 WeakMap에 보관해 해당 객체 자체가 변경되지 않는 한 별도의 키가 없어도 객체의 참조를 통해 값을 관리할 수 있다.
- Recoil에서는 atom에서 파생된 값을 만들기 위해 selector가 필요했지만, Jotai에서는 selector가 없이도 atom만으로 atom 값에서 또 다른 파생된 상태를 만들 수 있다.

### 작고 빠르며 확장에도 유연한 Zustand (p.391~399)

- Jotai가 Recoil의 영감을 받아 만들어졌다면, Zustand는 리덕스에 영감을 받아 만들어졌다.
- 즉, atom이라는 최소 단위의 상태를 관리하는 것이 아니라 Zustand에서는 하나의 스토어를 중앙 집중형으로 활용해 이 스토어 내부에서 상태를 관리하고 있다.
- Zustand 자체 라이브러리 크기는 79.1kB인 Recoil, 13.1kB인 Jotai와 다르게 고작 2.9kB밖에 되지 않는다.
- Zustand는 리덕스와 마찬가지로 미들웨어를 지원한다. create의 두 번째 인수로 원하는 미들웨어를 추가하면 되는데, 스토어 데이터를 영구히 보존할 수 있는 persist, 복잡한 객체를 관리하기 쉽게 도와주는 immer, 리덕스와 함께 사용할 수 있는 리덕스 미들웨어 등 여러 가지 미들웨어를 제공해 필요한 미들웨어를 사용할 수 있게 도와준다.
- 이 미들웨어를 사용하면 상태를 sessionStorage에 추가로 저장하는 등의 기본적인 상태 관리 작동 외에 추가적인 작업을 정의할 수도 있다.
