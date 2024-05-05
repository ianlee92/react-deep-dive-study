### 📅 2024년 4월 24일

# 📚 10장 리액트 17과 18의 변경 사항 살펴보기

### 10.1.2 이벤트 위임 방식의 변경
- 리액트는 이벤트 핸들러를 해당 이벤트 핸들러를 추가한 각각의 DOM요소에 부착하는 것이 아니라, 이벤트 타입(click, change)당 하나의 핸들러를 루트에 부착한다.
- 이를 이벤트 위임이라 한다.

#### 이해가 안가서...

#### 일반적인 이벤트 처리방식
```
<div id="target"></div>
<script>
  const doSomething = () => {
    console.log("doSomething");
  };
  document.querySelector("#target").addEventListener("click", doSomething);
</script>
```
- 해당 dom 요소에 이벤트를 직접 부착함.

#### 리액트에서 이벤트 처리방식
```typescript
return (<button onClick={handleClick}/>
```


- React에서 등록한 이벤트는 해당 요소에 직접 등록되지 않고, 모두 document나 root 요소에 등록
- 따라서, 실제 우리가 요소에 onClick등의 프로퍼티에 명시를 하더라도 실제 등록되는것은 root 요소에 등록됨.
- 리액트 17부터 이러한 이벤트 위임이 모두 document가 아닌 리액트 컴포넌트의 최 상단 트리, 즉 루트요소로 변경됨

즉, 리엑트는 이벤트 핸들러를 각 요소가 아닌 root에 연결해 이벤트를 좀 더 효율적으로 관리함.
- 이렇게 이벤트를 상위에 붙이는 것을 이벤트 위임이라 함.

#### 이런 패턴을 사용하는 이유
 - 메모리 절약.
 - 핸들러를 개별로 부착하는것보다 상위 하나의 요소에만 부착하는것이 메모리를 절약할수 있기 때문.
 - 하지만, React에서는 이벤트를 이미 위임해서 처리하므로 개발자가 이벤트 위임패턴을 사용하지 않아도 됨.

아래 두 예시는 성능적으로 별 차이 없음.

```typescript
// selectedItems = new Set([1, 2, 3])
// ids = [0, 1, 2, ..., 2999]

{ids.map((id) => (
  <FancyButton
    key={id}
    id={id}
    label={id}
    isSelected={selectedItems.has(id)}
    onClick={() => handleClick(id)}
  />
))}
```

```typescript
const handleClick = (event) => {
  // Get id from button element
  const id = event.target.id;
  setSelectedItems((prevState) => new Set([...prevState, id]));
};


<div className="container" onClick={handleClick}>
  {ids.map((id) => (
    <FancyButton
      key={id}
      id={id}
      label={id}
      isSelected={selectedItems.has(id)}
    />
  ))}
</div>
```


### 10.1.3 import React from 'react'
- 더이상 선언해주지 않아도 됨.

### 10.1.4 그 밖의 주요 변경 사항
#### 이벤트 풀링 제거
- SynteticEvent
  - native event객체를 래핑한 객체
  - 이벤트가 발생할때마다 native 객체를 SyntheticEvent객체로 래핑
  -  너무 많은 객체가 필요하므로 성능상 문제 발생
  

  event pool 로 해결
  -  Synthetic Event Instance 들이 들어있는 공간
  -   이벤트가 발생하면 이를 사용한뒤 끝나면 바로 초기화 시키고 다시 풀로 돌려놓는 것
  -  하지만 event pool 은 핸들러 동작이 끝나면 곧바로 이벤트 객체를 초기화 하므로 비동기적인 동작은 실행되지 않음.
  -  e.persist()를 사용해 다른 메모리에 정보를 저장해 두어야 사용할수 있다고 함.


=> react 17버전에서는 제거됨



#### useEffect 클린업 함수의 비동기 실행
클린업 함수가 완전히 비동기적으로 실행
- 컴포넌트의 커밋단계가 완료될 때 까지 지연됨
- 화면이 업데이트가 완전히 끝난 이후에 실행되도록.


## 리액트 18버전 살펴보기

### 새로 추가된 hook
#### useId
- 컴포넌트 별로 유니크한 값을 생성

#### useTransition
- UI변경을 가로막지 않고 상태를 업데이트 할 수 있는 리액트 훅
- 마치 비동기처럼 동작
- 느린 렌더링 과정에서 로딩 화면을 보여주거나 지금 진행중인 렌더링을 버리고 새로운 상태값으로 다시 렌더링하는 등의 작업을 할 수 있게 됨.

#### useDeferredValue

- 리액트 컴포넌트 트리에서 리렌더링이 급하지 않은 부분을 지연할 수 있게 도와주는 훅


#### 테어링 현상
- 하나의 state값임에도 서로 다른 값을 기준으로 렌더링되는 현상
- 리액트18부터 동시성의 개념이 추가되면서 생긴 문제같음.
- 이 현상을 해결하기 위한 훅이 바로 `useSyncExternalStore`

#### useInsertionEffect
DOM이 실제로 변경되기 전 동기적으로 실행
- 스타일을 삽입하는 코드를 넣음으로써 브라우저가 레이아웃을 계산하기 전에 실행하게끔 더 자연스러운 스타일 삽입이 가능함.

- useLayoutEffect와 차이점은, useLayoutEffect은 모든 DOM의 변경작업이 다 끝난 이후에 실행됨.

### 자동배치
- 여러 상태 업데이트를 하나의 리렌더링으로 묶어서 성능을 향상시키는 방법
- 리액트18부터 루트 컴포넌트를 createRoot를 사용해서 만들면 모든 업데이트가 배치 작업으로 최적화할 수 있게 됨.

### 더욱 엄격해진 엄격모드
엄격모드
- 잠재적인 버그를 찾는데 도움이 되는 기능
- 개발자모드에서만 작동
- 개발자모드에서 렌더링이 두번실행되는 것 처럼 느낄때가 있는데
- 이는, 리액트의 모든 컴포넌트는 순수하다고 가정(함수처럼 동일한 매개변수를 넘기면 늘 같은 값을 리턴한다.) 하기 때문에, 이를 잘 지키고 있는지 항상 순수한 결과물을 내고 있는지 확인시켜주기위해 두번 실행하게 되는 것.


# 📚 11장 Next.js 13과 리액트 18
