### 📅 2024년 3월 13일

# 1. 리액트 개발을 위해 꼭 알아야 할 자바스크립트

## 1.1 자바스크립트의 동등비교

eslint-react-config 에 선언돼 있는 react-hooks/exhaustive-deps의 도움을 받아 배열을 채움

- 컴포넌트의 렌더링 : props의 동등비교로 일어남, 이는 객체의 얕은 비교를 기반으로 이뤄짐
- 얕은비교에 대한 이해가 없다면 렌더링 최적화에 어려움을 겪을 수 있음

### 1.1.1 자바스크립트의 데이터 타입

- **원시타입** : boolean, null, undefined, number, string, symbol, bigint

  - 객체가 아닌 타입으로 메서드를 갖지않음
  - undefined : 선언 후 값을 할당하지 않은 변수 또는 값이 주어지지 않은 인수에 자동 할당되는 값
  - null : 명시적으로 비어있음을 나타내는 값
  - boolean : 0, -0 , NaN, null, undefined, ‘’ , “” 등 falsy한 값 존재, 이외는 모두 true로 취급
  - bigint : number가 다룰 수 있는 숫자 크기의 제한 극복 위해 등장
  - string : template literal - 백틱을 이용하여 표현한 문자열로 줄바꿈이 가능하고, 내부에 표현식을 쓸수 있다.

    ```jsx
    // 한번 문자열이 생성되면 그 문자열을 변경할 수 없다.
    const foo = "bar";

    console.log(foo[0]); // 'b'

    // 앞 글자를 다른 글자로 변경해 보았다.
    foo[0] = "a";

    // 이는 반영되지 않음
    console.log(foo); // bar
    ```

  - symbol : 중복되지 않는 어떠한 고유한 값을 나타내기 위해 만들어짐

    ```jsx
    // symbol 함수에 같은 인수를 넘겨주더라도 이는 동일한 값으로 인정되지 않음
    // 성별 함수 내부에 넘겨주는 값은 symbol 생성에 영향을 미치지 않음 (symbol.for 제외)
    const key = Symbol('key')
    const key2 = Symbol('key')

    key === key2 // false

    // 동일한 값을 사용하기 위해서는 Symbol.for 활용
    Symbol.for{('hello') === Symbol.for('hello') // true
    ```

- **객체타입** : object (배열, 함수, 정규식, 클래스)

  - 참조를 전달해 참조타입으로도 불림

    ```jsx
    typeof [] === "object"; // true
    typeof {} === "object"; // true

    function hello() {}
    typeof hello === "function"; // true

    const hello1 = function () {};

    const hello2 = function () {};

    // 객체인 함수의 내용이 육안으로는 같아 보여도 참조가 다르기 때문에 false 반환
    hello1 === hello2; // false
    ```

### 1.1.2 값을 저장하는 방식의 차이

- 원시타입은 불변형태의 값으로 저장 - 변수 할당 시점에 메모리 영역을 차지하고 저장

  ```jsx
  // 값을 비교하므로 선언 전달 모두 같은 결과
  let hello = "hello world";
  let hi = hello;
  let hola = "hello world";

  console.log(hello === hi); // true
  console.log(hello === hola); // true
  ```

- 객체는 프로퍼티 업데이트 가능 - 복사할때도 값이 아닌 참조를 전달

  ```jsx
  var hello = {
    greet: "hello world",
  };

  var hi = {
    greet: "hello world",
  };

  var hola = hello; // 변수명, 변수명의 주소가 다르지만 value가 가르키는 주소 동일

  // 둘은 완벽히 같은 내용을 지니지만 동등비교는 false 반환
  console.log(hello === hi); // false

  // 원시값인 내부 속성값을 비교하면 동일
  console.log(hello.greet === hi.greet); // true

  console.log(hello === hola); // true
  ```

### 1.1.3 자바스크립트의 또 다른 비교 공식, Object.is

- == vs Object.is : 양쪽이 같은 타입이 아니라면 강제 형변환(type casting)후 변경. 하지만 Object.is는 형변환 하지않음 타입이 다르면 false 반환
- === vs Object.is : Object.is가 더 개발자가 기대하는 방식으로 비교

  ```jsx
  -0 === +0; // true
  Object.is(-0, +0); // false

  Number.NaN = NaN; // false
  Object.is(Number.NaN, NaN); // true

  NaN === 0 / 0; // false
  Object.is(NaN, 0 / 0); // true
  ```

- 객체 비교에서는 큰차이가 없음 === 와 같은 동작, 위와 같은 동등비교의 한계를 극복하기 위해 만들어짐

### 1.1.4 리액트에서의 동등 비교

- 리액트에서는 이 Object.is를 이용해 구현한 폴리필을 이용해 사용 (아래 코드는 리액트 발췌)

  ```jsx
  // any 어떤값도 받을수 있는 타입
  function is(x:any, y:any) {
    return (
      (x === y && (x !== 0 || 1 / x === 1 / y)) || (x !== x && y !== y)
    )
  }

  // 런타임에 Object.is가 있다면 사용 아니라면 위함수 사용
  // 인터넷 익스플로러 등에 존재하지 않기 때문에 폴리필 넣어준 것으로 보임
  const objectIs: (x:any, y:any) => boolean =
  	typeOf Object.is === 'function' ? Object.is : is

  export default objectIs
  ```

- Object.is 기반으로 동등비교를 하는 shallowEqual 함수 사용

  ```jsx
  import is from '.objectIs'
  // 객체의 특정 프로퍼티가 있는지 확인하는 메서드
  import hasOwnProperty from './hasOwnProperty'

  /**
   * 주어진 객체의 키를 순회하면서 두 값이 엄격한 동등성을 가지는지 확인하고,
   * 다른값은 false 반환, 같은값은 true 반환
   */

  function shallowEqual(objA: mixed, objB: mixed):boolean {
  	if (is(objA, objB)) {
  	  return true
    }

  	if (typeOf objA !== 'object' ||
  			objA === null ||
  			typeOf objB !== 'object' ||
  			objB === null
  	) {
  	  return false
    }

    // 각 키 배열을 꺼냄
    const keysA = Object.Keys(objA)
    const keysB = Object.Keys(objB)

    // 배열의 길이가 다르면 false
    if (keysA.length !== keysB.length) {
  	  return false
  	}

  	// A의 키를 기준으로, B에 같은 키가 있는지, 그리고 그 값이 같은지 확인
  	for (let i = 0; i < keysA.length; i++) {
  		const currentKey = keysA[i]
  		if (
  			!hasOwnProperty.call(objB, currentKey) ||
  			!is(objA[currentKey], objB[currentKey])
  		) {
  			return false
  		}
  	}
    return true
  }

  export default shallowEqual
  ```

- Object.is가 수행하지 못하는 비교 즉 얕은 비교를 한번 더 수행, 1depth 존재하는 값만 비교 (2depth 불가)
- props가 깊어지는 경우 React.memo는 컴포넌트에 실제로 변경된 값이 있음에도 불구하고 메모이제이션된 컴포넌트를 반환하지 못한다. 이를 비교하려고 한다면 성능에 악영향을 줬을것이다. (p.33)

### 1.1.5 정리

- 자바스크립트 객체비교의 불완전성은 다른언어에서 볼수없는 특징이므로 반드시 기억하는것이 중요
- 향후 훅의 의존성 배열의 비교, 렌더링 방지를 넘어선 useMemo와 useCallback의 필요성, 렌더링 최적화를 위해서 꼭 필요한 React.memo를 올바르게 작동시키기 위해 고려해야 할 것들을 쉽게 이해할 수 있을 것

---

## 1.2 함수

함수의 다양한 형태와 차이점을 알아봄

### 1.2.1 함수란 무엇인가?

- 리액트에서는 컴포넌트라는 함수를 선언하고 매개변수로는 props라고 부르는 단일 객체를 받으며 JSX를 반환

### 1.2.2 함수를 정의하는 4가지 방법

- **함수선언문** : 선언문, 표현식 모두 사용할수 있음
- **함수표현식** : ‘일급 객체’는 다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체를 의미
  - 함수표현식과 선언식의 차이 : 호이스팅의 여부 (함수 선언문이 마치 코드 맨 앞단에 작성된 것처럼 작동하는 자바스크립트의 원리, 함수 실행전 미리 메모리에 등록) - p.38
  - 변수는 호이스팅때 undefined 로 값을 초기화함
  - 관리해야 하는 스코프가 길어질 경우 복잡해짐
- **Function 생성자** : 모두 문자열로 작성하므로 많아질수록 혼란
- **화살표 함수** : constructor 사용 불가, arguments 존재 X
  - this의 할당방식에서 클래스형 함수와의 차이 존재 - p.42

### 1.2.3 다양한 함수 살펴보기

- **즉시 실행 함수** : 재호출 불가
- **고차함수** : 일급객체라는 특징을 활용해 함수를 인수로 받는것이 가능

### 1.2.4 함수를 만들 때 주의해야 할 사항

- **부수효과 최대한 억제** : 함수내의 동작으로 인해 함수외부에 영향 (순수함수 X) - p.44
- **가능한 한 함수를 작게 생성** : 코드의 길이가 길어질수록 추적이 어려워짐
- **누구나 이해할 수 있는 이름 작성** : 네이밍의 중요성

### 1.2.5 정리

- 함수의 기초를 제대로 알고 함수가 함수답게 잘 설계되어 있는지 확인할 필요가 있다.

---

## 1.3 클래스

클래스와 프로토타입에 대한 이해가 필요

### 1.3.1 클래스란 무엇인가?

- 특정 형태의 객체를 반복적으로 만들기 위해 사용되는 것
- **constructor** : 객체를 생성하는 데 사용하는 특수한 메서드. 즉 생성자
- **property** : 클래스로 인스턴스를 생성할 때 내부에 정의할 수 있는 속성값
- **getter, setter** : 값을 가져오고 할당할때 사용
- **인스턴스 메서드** : 클래스 내부에서 선언한 메서드. 프로토타입에 선언되므로 프로토타입 메서드로 불린다.
- **정적 메서드** : 클래스의 인스턴스가 아닌 이름으로 호출할 수 있는 메서드
- **상속** : 기존 클래스를 상속받아서 자식 클래스에서 이를 상속받은 클래스를 기반으로 확장하는 개념

### 1.3.2 클래스와 함수의 관계

- 이전에는 프로토타입을 활용해 클래스의 작동 방식 동일하게 구현 (ES6미만 _createClass_

### 1.3.3 정리

- 클래스형 컴포넌트에 생명주기를 구현하는 방법 및 클래스형 컴포넌트 생성을 위해 React.Component, React.PureComponent를 상속하는지 등을 이해가능

---

## 1.4 클로저

클래스형 컴포넌트의 이해가 클래스, this, 프로토타입에 관련있다면 함수형 컴포넌트의 이해는 클로저에 있다. 함수형 컴포넌트의 구조 작동방식, 훅의 원리, 의존성 배열 등 대부분 클로저에 의존

### 1.4.1 클로저의 정의

- 함수와 함수가 선언된 어휘적 환경(어디서 선언되었는지)의 조합 - p.60

### 1.4.2 변수의 유효 범위, 스코프

- 클로저의 유효한 범위를 스코프라 칭함
- **전역스코프** : 이 스코프에서 변수를 선언하면 어디서든 호출 가능
- **함수스코프** : { }블록이 스코프 범위를 결정

### 1.4.3 클로저의 활용

- **클로저의 활용** : 전역 스코프의 무분별한 사용 서비스 망가짐 - p.63
- **리액트에서의 클로저** : useState 내부에서 클로저가 활용되어 외부함수가 반환한 내부함수는 외부 함수가 호출이 끝났음에도 외부함수가 선언된 환경을 기억하기 때문에 state 값을 계속 사용 할 수 있는 것

### 1.4.4 주의할 점

- 클로저를 사용하는 데 선언된 환경을 기억해야되므로 비용이 발생함
- 외부함수를 기억하고 이를 내부 함수에서 가져다 쓰면 성능에 영향
- 클로저에 필요한 작업만 남겨두지 않는다면 메모리를 불필요하게 잡아먹음

### 1.4.5 정리

함수형 프로그래밍의 부수효과가 없고 순수해야 한다는 목적을 위해 사용, 공짜로 쓸 수 없기에 주의가 필요

---

## 1.5 이벤트 루프와 비동기 통신의 이해

비동기 작업이 어떻게 처리되는지 이해하고 이를 도와주는 이벤트 루프를 비롯한 다양한 개념을 알아보자

### 1.5.1 싱글 스레드 자바스크립트

- 동기식으로 작동하는 자바스크립트 코드

### 1.5.2 이벤트 루프란?

- 런타임 외부에서 비동기 실행을 돕기 위해 만들어진 장치
- **호출스택과 이벤트 루프 -** p.76

### 1.5.3 테스크 큐와 마이크로 테스크 큐

### 1.5.4 정리

- 비동기 처리가 이루어지는 방법들을 자세히 살펴보는 것이 중요

---

## 1.6 리액트에서 자주 사용하는 자바스크립트 문법

### 1.6.1 구조 분해 할당

- 객체의 값을 분해해 개별변수에 할당
- **배열 구조 분해 할당 -** p.81
- **객체 구조 분해 할당** - p.83

### 1.6.2 전개 구문

- **배열의 전개 구문** : push, concat, splice
- 객체의 전개 구문 - p.88

### 1.6.3 객체 초기자

### 1.6.4 Array 프로토타입의 메서드 : map, filter, reduce, forEach

- map
- filter
- reduce
- forEach

### 1.6.5 삼항 조건 연산자

- JSX 조건부 렌더링

### 1.6.6 정리

- ES5이하의 문법으로 어떻게 구현될 수 있었는지 알아보는것도 재미를 느낄 수 있음

---

## 1.7 선택이 아닌 필수, 타입스크립트

### 1.7.1 타입스크립트란?

- 기존 자바스크립트 문법에 타입을 가미

### 1.7.2 리액트 코드를 효과적으로 작성하기 위한 타입스크립트 활용법

- any 대신 unknown 사용
- 타입가드를 적극 활용
- 제네릭
- 인덱스 시그니처

### 1.7.3 타입스크립트 전환 가이드

- tsconfig.json 먼저 작성하기
- JSDoc과 @ts-check를 활용해 점진적 전환
- 타입 기반 라이브러리 사용을 위한 @types 모듈 설치
- 파일 단위로 조금씩 전환

### 1.7.4 정리

자바스크립트를 제대로 이해하고 타입스크립트를 사용하자

---

# 📚 2장 리액트 핵심 요소 깊게 살펴보기

## 2.1 JSX란?

### 2.1.1 JSX의 정의

- **JSXElement** : 가장 기본요소이며 HTML요소와 비슷한 역할 - p.118
- **JSXAttribute** : 엘리먼트에 부여할 수 있는 속성
- **JSXChildren** : 엘리먼트에 자식 값
- **JSXString** : 큰따옴표/작은따옴표로 구성된문자열
