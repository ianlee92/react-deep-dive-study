### 📅 2024년 3월 13일

# 📚 들어가며

### p.3, 11

- 당시 대부분의 프레임워크는 **양방향 바인딩** 구조를 채택해 모델과 뷰가 밀접한 관계를 맺고 서로가 서로를 변경할 수 있는 구조였다.
- 이러한 방식은 코드를 작성하는 것은 간단하지만 변경된 DOM을 추적하는 것이 어렵고, 왜 이렇게 변경됐는지 역시 추적하기 어려워 수많은 버그가 발생하던 지점이기도 했다.
- **리액트는 단방향 바인딩**만 지원한다. 말 그대로 데이터의 흐름이 한쪽으로만 간다는 것이다.
- 모델이 뷰를 변경하는 단방향 방식은 모델의 데이터가 변경되어 뷰가 변경되어야 하면 이전 DOM을 버리고 새롭게 렌더링하는 방식을 제안했다.

### p.16

- 전통적인 MVC 패턴을 유지해 개발하는 것에 어려움을 느꼈으며, 예측 가능한 데이터 흐름, 독립적인 컴포넌트 개발 등을 목표로 리액트와 Flux 패턴을 도입했다.

### p.18

- Angular나 Vue 같은 다른 라이브러리와는 다르게, 웹 개발을 위한 프레임워크를 지향하지 않으므로 리액트와 함께 사용할 수 있는 다양한 라이브러리가 있다.

# 📚 1장 리액트 개발을 위해 꼭 알아야 할 자바스크립트

### p.41

- 화살표 함수에서의 this는 우리가 원하는 대로 클래스의 인스턴스인 this를 가리키는 것을 볼 수 있다.
- 즉, 별도의 작업을 추가하지 않고 this에 접근할 수 있는 방법이 바로 화살표 함수인 것이다.

### p.39, 43, 44

- **즉시 실행 함수**(Immediately invoked Function Expression, 일반적으로 IIFE라고 부른다)는 말 그대로 함수를 정의하고 그 순간 즉시 실행되는 함수를 의미한다. 단 한 번만 호출되고, 다시금 호출할 수 없는 함수다. 그래서 일반적으로 즉시 실행 함수에 이름을 붙이지 않는다.

  ```tsx
  (function (a,b) {
    return a + b
  })(10, 24); // 34

  ((a, b) => {
    return a + b
  },
  )(10, 24) // 34
  ```

- **일급 객체**란 다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체를 의미한다. 자바스크립트에서 함수는 일급 객체다. 함수는 다른 함수의 매개변수가 될 수도 있고, 반환값이 될 수도 있으며, 할당도 가능하므로 일급 객체가 되기 위한 조건을 모두 갖추고 있다.

  ```tsx
  const sum = function (a, b) {
    return a + b;
  };

  sum(10, 24); // 34
  ```

- **고차 함수(Higher Order Function)**은 자바스크립트의 함수가 일급 객체라는 특징을 활용하면 함수를 인수로 받거나 결과로 새로운 함수를 반환시킬 수 있는데 이런 역할을 한다.

  ```tsx
  const doubeldArray = [1, 2, 3].map((item) => item * 2);

  doubledArray; // [2, 4, 6]
  ```

### p.44

- 함수의 부수 효과(side-effect)란 함수 내의 작동으로 인해 함수가 아닌 함수 외부에 영향을 끼치는 것을 의미한다. 이러한 부수 효과가 없는 함수를 **순수 함수**라 한다.

### p.45

- 부수 효과를 처리하는 훅인 useEffect의 작동을 최소화하는 것이 부수 효과를 최대한 억제할 수 있는 방향으로 함수를 설계하는 방법이다. useEffect의 사용은 피할 수 없지만 최소한으로 줄임으로써 함수의 역할을 좁히고, 버그를 줄이며 컴포넌트의 안정성을 높일 수 있다.

### p.59

- 함수 컴포넌트의 구조와 작동 방식, 훅의 원리, 의존성 배열 등 함수 컴포넌트의 대부분의 기술이 모두 클로저에 의존하고 있기 때문에 함수 컴포넌트 작성을 위해서는 클로저에 대해 이해하는 것이 필수다.

### p.61

- 자바스크립트는 기본적으로 함수 레벨 스코프를 따른다. 즉, {} 블록이 스코프를 범위를 결정하지 않는다. {} 내부에 선언되어 있는데도 {} 밖에서도 접근이 가능하다.

  ```tsx
  if (true) {
    var global = "global scope";
  }

  console.log(global); // 'global scope'
  ```

### p.65

- 클로저의 원리를 사용하고 있는 대표적인 것 중 하나가 바로 useState다.

  ```tsx
  function Component() {
    const [state, setState] = useState();

    function handleClick() {
      // useState 호출은 위에서 끝났지만,
      // setState는 계속 내부의 최신값(prev)을 알고 있다.
      // 이는 클로저를 활용하기 때문에 가능하다.
      setState((prev) => prev + 1);
    }
    // ...
  }
  ```

### p.67

- **클로저**를 사용할 때 한 가지 주의할 점은, 클로저를 사용하는 데는 비용이 든다는 것이다. 클로저는 생성될 때마다 그 선언적 환경을 기억해야 하므로 추가로 비용이 발생한다.

### p.70

- 자바스크립트는 **싱글 스레드 언어**이다. 자바스크립트의 모든 코드는 동기식으로 한 번에 하나씩 순차적으로 처리된다. 하나의 프로그램에는 하나의 프로세스만이 할당되므로 이러한 작업을 수행하기 어려웠는데, 그래서 탄생한 더 작은 실행 단위가 바로 스레드(thread)다.

### p.72

- **이벤트 루프**란 자바스크립트 런타임 외부에서 자바스크립트의 비동기 실행을 돕기 위해 만들어진 장치라 볼 수 있다.
- **호출 스택(call stack)**은 자바스크립트에서 수행해야 할 코드나 함수를 순차적으로 담아두는 스택이다.

### p.74

- 호출 스택이 비어 있는지 여부를 확인하는 것이 바로 이벤트 루프다. 이벤트 루프는 단순히 이벤트 루프만의 단일 스레드 내부에서 이 호출 스택 내부에 수행해야 할 작업이 있는지 확인하고, 수행해야 할 코드가 있다면 자바스크립트 엔진을 이용해 실행한다.
- 코드를 실행하는 것과 호출 스택이 비어있는지 확인하는 것 모두가 단일 스레드에서 일어난다는 점이다. 즉, 두 작업은 동시에 일어날 수 없으면 한 스레드에서 순차적으로 일어난다.

### p.75

- **태스트 큐**란 실행해야 할 태스크의 집합을 의미한다. 이벤트 루프느 이러한 태스크 큐를 한 개 이상 가지고 있다. 그리고 이름과는 다르게 태스크 큐는 자료 구조의 큐(queue)가 아니고 set 형태를 띠고 있다.
- 자료구조인 큐는 무조건 앞에 있는 것을 FIFO(First In First Out) 형식으로 꺼내와야 하지만 태스크 큐는 그렇지 않다.
- 태스크 큐에서 의미하는 실행해야 할 태스크라는 것은 비동기 함수의 콜백 함수나 이벤트 핸들러 등을 의미한다.
- 즉, 이벤트 루프의 역할은 호출 스택에 실행 중인 코드가 있는지, 그리고 태스크 큐에 대기 중인 함수가 있는지 반복해서 확인하는 역할을 한다.
- 호출 스택이 비었다면 태스크 큐에 대기 중인 작업이 있는지 확인하고, 이 작업을 실행 가능한 오래된 것부터 순차적으로 꺼내와서 실행하게 된다. 이 작업 또한 마찬가지로 태스크 큐가 빌 때까지 이루어진다.

### p.76

- 태스크 큐와 다르게, **마이크로 태스크 큐**라는 것도 있다.
- 이벤트 루프는 하나의 마이크로 태스크 큐를 갖고 있는데, 기존의 태스크 큐와는 다른 태스크를 처리한다.
- 여기에 들어가는 마이크로 태스크에는 대표적으로 Promise가 있다.
- 이 마이크로 태스크 큐는 기존 태스크 큐보다 우선권을 갖는다. 즉, setTimeout과 setInterval은 Promise보다 늦게 실행된다.

### p.79

- 브라우저에 렌더링하는 작업은 마이크로 태스크 큐와 태스크 큐 사이에서 일어난다는 것을 알 수 있다.
- 결론적으로 동기 코드는 물론이고 마이크로 태스크 또한 마찬가지로 렌더링에 영향을 미칠 수 있다.
- 따라서 만약 특정 렌더링이 자바스크립트 내 무거운 작업과 연관이 있다면 이를 어떤 식으로 분리해서 사용자에게 좋은 애플리케이션 경험을 제공해 줄지 고민해 보아야 한다.

### p.95

- **forEach**는 아무런 반환값이 없다. 단순히 콜백 함수를 실행할 뿐 맵과 같이 결과를 반환하는 작업은 수행하지 않는다.
- 즉, 콜백 함수 내부에서 아무리 반환해도 모두 의미 없는 값이 된다.
- forEach의 반환값은 undefined로 의미 없다는 것을 알아두어야 한다.

# 📚 2장 리액트 핵심 요소 깊게 살펴보기

### p.129

- **DOM**은 웹페이지에 대한 인터페이스로 브라우저가 웹페이지의 콘텐츠와 구조를 어떻게 보여줄지에 대한 정보를 담고 있다.

- **DOM과 브라우저 렌더링 과정**

  - 브라우저가 사용자가 요청한 주소를 방문해 HTML 파일을 다운로드한다.
  - 브라우저의 렌더링 엔진은 HTML을 파싱해 DOM 노드로 구성된 트리(DOM)을 만든다.
  - CSS 파일을 만나면 해당 CSS 파일도 다운로드한다.
  - 브라우저의 렌더링 엔진은 이 CSS도 파싱해 CSS 노드로 구성된 트리(CSSOM)을 만든다.
  - 브라우저는 DOM 노드를 순호하는데, 여기서 모든 노드를 방문하는 것이 아니고 사용자 눈에 보이는 노드만 방문한다. 즉 display: none과 같이 사용자 화면에 보이지 않는 요소는 방문해 작업하지 않는다. 이는 트리를 분석하는 과정을 조금이라도 빠르게 하기 위해서다.
  - 눈에 보이는 노드를 대상으로 해당 노드에 대한 CSSOM 정보를 찾고 여기서 발견한 CSS 스타일 정보를 이 노드에 적용한다. 이 DOM 노드에 CSS를 적용하는 과정은 크게 두 가지로 나눌 수 있다.
    - 레이아웃(layout, reflow): 각 노드가 브라우저 화면의 어느 좌표에 정확히 나타나야 하는지 계산하는 과정. 이 레이아웃 과정을 거치면 반드시 페인팅 과정도 거치게 된다.
    - 페인팅(painting): 레이아웃 단계를 거친 노드에 색과 같은 실제 유효한 모습을 그리는 과정.

### p.133

- 가상 DOM과 렌더링 과정 최적화를 가능하게 해주는 것이 바로 리액트 파이버(React Fiber)다.
- 리액트 파이버는 리액트에서 관리하는 평범한 자바스크립트 객체다.
- 파이버는 파이버 재조정자(fiber reconciler)가 관리하는데, 이는 앞서 이야기한 가상 DOM과 실제 DOM을 비교해 변경 서항을 수집하며, 둘 사이에 차이가 있으면 변경에 관련된 정보를 가지고 있는 파이버를 기준으로 화면에 렌더링을 요청하는 역할을 한다.
- 이러한 모든 과정이 비동기로 일어난다.

### p.134

- 렌더 단계에서 리액트는 사용자에게 노출되지 않는 모든 비동기 작업을 수행한다. 그리고 이 단계에서 앞서 언급한 파이버의 작업, 우선순위를 지정하거나 중지시키거나 버리는 등의 작업이 일어난다.
- 커밋 단계에서는 앞서 언급한 것처럼 DOM에 실제 변경 사항을 반영하기 위한 작업. commitWork()가 실행되는데, 이 과정은 앞서와 다르게 동기식으로 일어나고 중단될 수도 없다.

### p.139

- 생성된 파이버는 state가 변경되거나 생명주기 메서드가 실행되거나 DOM의 변경이 필요한 시점 등에 실행된다. 중요한 것은 리액트가 파이버를 처리할 때마다 이러한 작업을 직접 바로 처리하기도 하고 스케줄링하기도 한다는 것이다.
- 즉, 이러한 작업들은 작은 단위로 나눠서 처리할 수도, 애니메이션과 같이 우선순위가 높은 작업은 가능한 한 빠르게 처리하거나, 낮은 작업을 연기시키는 등 좀 더 유연하게 처리된다.

- 파이버 트리는 사실 리액트 내부에서 두 개가 존재한다. 하나는 현재 모습을 담은 파이버 트리이고, 다른 하나는 작업 중인 상태를 나타내는 workInProgress 트리다.
- 리액트 파이버의 작업이 끝나면 리액트는 단순히 포인터만 변경해 workInProgress 트리를 현재 트리로 바꿔버린다. 이러한 기술을 더블 버퍼링이라고 한다.

### p.142

- 리액트는 앞서 만든 current 트리가 존재하고, setState로 인한 업데이트 요청을 받아 workInProgress 트리를 다시 빌드하기 시작한다. 이 빌드 과정은 앞서 트리를 만드는 과정과 동일하다.
- 최초 렌더링 시에는 모든 파이버를 새롭게 만들어야 했지만 이제는 파이버가 이미 존재하므로 되도록 새로 생성하지 않고 기존 파이버에서 업데이트된 props를 받아 파이버 내부에서 처리한다.

### p.143

- 리액트 컴포넌트에 대한 정보를 1:1로 가지고 있는 것이 파이버이며, 이 파이버는 리액트 아키텍처 내부에서 비동기로 이뤄진다.
- 이러한 비동기 작업과 달리, 실제 브라우저 구조인 DOM에 반영하는 것은 동기적으로 일어나야 하고, 또 처리하는 작업이 많아 화면에 불완전하게 표시될 수 있는 가능성이 높으므로 이러한 작업을 가상에서, 즉 메모리상에서 먼저 수행해서 최종적인 결과물만 실제 브라우저 DOM에 적용하는 것이다.

### p.172

- 리액트의 렌더링은 브라우저가 렌더링에 필요한 DOM 트리를 만드는 과정을 의미한다.
- 리액트에서의 렌더링이란 리액트 애플리케이션 틀 안에 있는 모든 컴포넌트들이 현재 자신들이 가지고 있는 props와 state의 값을 기반으로 어떻게 UI를 구성하고 이를 바탕으로 어떤 DOM 결과를 브라우저에 제공할 것인지 계산하는 일련의 과정을 의미한다.

### p.173

- 사용자가 처음 애플리케이션에 진입하면 당연히 렌더링해야 할 결과물이 필요하다. 리액트는 브라우저에 이 정보를 제공하기 위해 최초 렌더링을 수행한다.
- 리렌더링은 처음 애플리케이션에 진입했을 때 최초 렌더링이 발생한 이후로 발생하는 모든 렌더링을 의미한다.

- 함수 컴포넌트의 useState()의 두 번째 배열 요소인 setter가 실행되는 경우: useState가 반환하는 배열의 두 번째 인수는 클래스 컴포넌트의 setState와 마찬가지로 state를 업데이트하는 함수다. 이 함수가 실행되면 렌더링이 일어난다.
- 함수 컴포넌트의 useReducer()의 두 번째 배열 요소인 dispatch가 실행되는 경우: useReducer도 useState와 마찬가지로 상태와 이 상태를 업데이트하는 함수를 배열로 제공한다. 이 두 번째 배열 요소를 실행하면 컴포넌트의 렌더링이 일어난다.
- 컴포넌트의 key props가 변경되는 경우: 리액트에서 key는 명시적으로 선언돼 있지 않더라도 모든 컴포넌트에서 사용할 수 있는 특수한 props다. 리액트에서 key는 리렌더링이 발생하는 동안 형제 요소들 사이에서 동일한 요소를 식별하는 값이다. 리렌더링이 발생하면 current 트리와 workInProgress 트리 사이에서 어떠한 컴포넌트가 변경이 있었는지 구별해야 하는데, 이 두 트리 사이에서 같은 컴포넌트인지를 구별하는 값이 바로 key다. key를 활용해 강제로 리렌더링을 일으키는 것이 가능하다. (key={Math.random()}와 같이 렌더링할 때마다 변하는 임의의 값을 key에 넣는 경우)
- props가 변경되는 경우: 부모로부터 전달받는 값인 props가 달라지면 이를 사용하는 자식 컴포넌트에서도 변경이 필요하므로 리렌더링이 일어난다.
- 부모 컴포넌트가 렌더링될 경우: 한 가지 주의할 점은, 부모 컴포넌트가 리렌더링된다면 자식 컴포넌트도 무조건 리렌더링이 일어난다는 것이다.

### p.177

- 렌더 단계(Render Phase)는 컴포넌트를 렌더링하고 변경 사항을 계산하는 모든 작업을 말한다.
- 즉, 렌더링 프로세스에서 컴포넌트를 실행해 (render() 또는 return) 이 결과와 이전 가상 DOM을 비교하는 과정을 거쳐 변경이 필요한 컴포넌트를 체크하는 단계다. 여기서 비교하는 것은 크게 세 가지로, type, props, key다. 세 가지 중 하나라도 변경된 것이 있으면 변경이 필요한 컴포넌트로 체크해 둔다.
- 커밋 단계(Commit Phase)는 렌더 단계의 변경 사항을 실제 DOM에 적용해 사용자에게 보여주는 과정을 말한다. 이 단계가 끝나야 비로소 브라우저의 렌더링이 발생한다.
- 리액트의 렌더링이 일어난다고 해서 무조건 DOM 업데이트가 일어나는 것은 아니다. 렌더링을 수행했으나 커밋 단계까지 갈 필요가 없다면, 즉 변경 사항을 계산했는데 아무런 변경 사항이 감지되지 않는다면 이 커밋 단계는 생략될 수 있다.
