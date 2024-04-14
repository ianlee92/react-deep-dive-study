### 📅 2024년 4월 3일

# 📚 4장 서버 사이드 렌더링

### 싱글 페이지 어플리케이션

- 렌더링과 라우팅에 필요한 대부분의 기능을 브라우저의 자바스크립트에 의존.
- 첫 페이지에서 모든 데이터를 불러오고 이후에 페이지 전환을 위한 모든 작업이 자바스크립트와 브라우저에서의 작업으로 이루어진다.
- 페이지를 불러온 이후에는 서버에서 html을 내려받지 않고 하나의 페이지에서 모든 작업을 처리한다. => "싱글 페이지" 어플리케이션
- html내부코드는 비어있다. 빈껍데기 상태의 html파일에서 자바스크립트코드를 실행하는 과정에서 페이지가 렌더링된다.
- 단점: 최초에 로딩속도가 느리다.
  - 사용자가 해당 페이지에 진입하고, 자바스크립트 리소스를 다운로드하고, http 요청을 수행한 이후에 이 결과를 가지고 화면을 렌더링해야한다.
- 장점: 최초 로딩 이후에는 서버를 거쳐 리소스를 받아올 일이 적어지므로 UX/UI에 이점.

### JAM 스택

- CSR이 널리 퍼지게 된 것은 단순히 사용자에게 더 좋은 UX를 제공할 수 있어서만은 아니다.
  - 이전에는 웹 어플리케이션을 만들기 위해 자바스크립트 이외에도 신경쓸 것이 많았다.
  - SSR방식에서는 서버에서 데이터가 채워진 상태의 html을 넘겨주어야했구나! 서버 의존성을 해결해주었다
- Javascript, API, Markup

### 서버사이드 렌더링

- 최초에 사용자에게 보여줄 페이지를 서버에서 렌더링해 빠르게 사용자에게 화면을 제공하는 방식.
- 웹페이지를 렌더링하는 작업에 대한 책임이 서버에 있음
- 장점
  - 최초 페이지 진입이 (비교적) 빠르다.
  - 검색엔진 최적화, sns 공유 등 메타데이터 제공이 쉽다.
  - 누적 레이아웃 이동이 적다. 페이지의 모든 정보가 채워진 상태에서 사용자에게 서빙되므로
  - 사용자 디바이스 성능에 비교적 자유롭다.
  - 보안에 좀 더 안전하다.
- 단점
  - 소스코드를 작성할 때 항상 서버를 고려해야 한다.
  - 서버가 구축되어있어야 한다.
  - 서비스 지연에 따른 문제

> SSR도 모든 문제의 해결책이 될 수는 없다. 웹사이트의 설계와 목적, 우선순위에 따라 적절한 방법을 선택해야한다.

### 서버사이드 렌더링을 위한 리액트 API

- renderToString
  - 리액트 컴포넌트를 렌더링해 html문자열로 반환. 클라이언트에서 실행되는 자바스크립트 코드는 포함되어있지 않다. 브라우저가 빠르게 렌더링할 수 있는 html을 제공할뿐.
  - data-reactroot: 리액트 컴포넌트의 루트 엘리먼트가 무엇인지 식별하는 역핧
- renderToStaticMarkup
  - renderToString과 유사하나 리액트에서만 사용하는 추가적인 DOM 속성을 만들지 않는다 -> 크기가 아주 약간 줄어든다.
  - 리액트에서 제공하는 useEffect와 같은 API를 실행할 수 없다. 리액트 기능이 필요없는 완전 순수한 html을 만들때만 사용한다. ex) 블로그 글, 상품의 약관 정보 등
- renderToNodeStream
  - Node환경에 의존한다. 브라우저에서 사용하는 것이 완전히 불가능하다.
  - 결과물의 타입이 ReadableStream
  - \*스트림: 큰 데이터를 다룰 때 데이터를 청크로 분할해 조금씩 가져오는 방식
  - 브라우저에 제공해야하는 큰 HTML을 작은 단위로 쪼개 연속적으로 작성함으로써 리액트 애플리케이션을 렌더링하는 Nodejs 서버의 부담을 덜 수 있다.
- renderToStaticNodeStream
  - renderToNodeSteam과 결과물은 동일하나, 리액트 관련 메소드는 사용할 수 없다.
- hydrate
  - renderToString, renderToNodeStream으로 생성된 html 콘텐츠에 자바스크립트 핸들러나 이벤트를 붙이는 역할을 한다.
  - 브라우저에서의 render와 비슷하다.
    - render: html과 컴포넌트를 인수로 받음 / html에 컴포넌트를 렌더링 / 이벤트 핸들러를 붙이는 작업
    - hydrate: 렌더링된 html과 컴포넌트를 인수로 받음 / 이벤트 핸들러를 붙이는 작업

### 서버사이드 렌더링 예제 프로젝트

- 페이지가 온전히 서버에서 만들어졌는지 확인하려면 소스보기를 통해 확인하면 된다.
- renderNodeStream과 renderToString의 차이는 어디까지나 서버에서만 존재한다.
- webpack.config.js: entry를 선언해 시작점을 선언하고, 필요한 파일과 그에 맞는 loader를 제공하고, 마지막으로 번들링에서 제외할 내용을 선언한 뒤 output으로 내보낸다.
- 설정파일을 추출하는 명령어 - **eject**
- API 요청이 서버와 클라이언트 두 군데에서 일어난다면, API 결과에 따라 결과물이 달라질 수 있지 않을까?

## Nextjs 톺아보기

### 파일 구조

- package.json
- next.config.js
  - `/** @type {import('next').NextConfig} */`: 타입 도움을 받기위한 코드
  - reactStrictMode: 리액트 애플리케이션 내부에서 잠재적인 문제를 개발자에게 알리기 위한 도구
  - swcMinify: SWC를 기반으로 최소화 작업을 할 것인가?
    - SWC: 번들링과 컴파일을 빠르게 수행하기위한 도구. 바벨의 대안.
    - 러스트라는 언어로 만들어졌다.
    - 14에서부터 default = true
- \_app.tsx: 전체 페이지의 시작점. Nextjs를 초기화하는 파일.
  - 에러바운더리
  - reset.css와 같은 전역 css 선언
  - 최초에는 서버사이드렌더링. 이후에는 클라이언트에서 \_app.tsx의 렌더링이 실행된다.
- \_document.tsx: html설정과 관련된 코드를 추가하는 곳
  - 무조건 서버에서 실행된다.
  - css in js의 스타일을 서버에서 모아 html로 제공하는 작업
- \_error.tsx: 클라이언트 또는 서버에서 발생하는 500에러를 처리하는 페이지
  - 개발모드에서는 이 페이지로 이동하지 않고 개발자 에러 팝업이 뜬다.
- 404.tsx
- 500.tsx: 서버에서 발생하는 에러 핸들링
- index.tsx: 파일명들이 라우팅 구조로 구성된다.

- a태그 대신 Link를 사용하자.
- window.location.push 대신 router.push를 사용하자.
- ⬆️ 단, getServerSideProps가 존재하는 파일일 경우만 유효한 효과를 보인다.

- api/hello.ts: 서버의 API를 정의하는 폴더
  - 서버에서 내려주는 데이터를 조합해 BFF 형태로 활용
  - CORS를 우회하는 용도로 사용될 수 있다.

### DataFetching

- 서버사이드 렌더링을 지원하기 위한 데이터 불러오기 방식

  - getStaticPaths / getStaticProps
    - 정적인 페이지를 보여주는 데 사용.
    - getStaticPaths: 접근 가능한 주소를 지정. 이 함수에서 정의하지 않은 url로 접근할 시 404를 반환한다.
      - fallback: 미리 빌드해야 한 페이지가 너무 많은 경우.
    - 사용자가 접근할 수 있는 페이지를 미리 빌드해둠으로써 사용자는 페이지가 렌더링되는 것을 기다릴 필요가 없어진다.
  - getServerSideProps

    - 페이지 진입 전 실행되는 함수.
    - json으로 직렬화할 수 없는 값은 props로 내려줄 수 없다.
    - redirect를 사용할 수 있다.
    - 서버사이드 렌더링 작동 순서

      1. 서버에서 fetch 등으로 필요한 데이터를 가져온다.
      2. html을 완성한다.
      3. 완성된 html을 브라우저에 전달한다.
      4. hydrate 작업을 진행한다.
      5. hydrate의 결과물과 서버에서 만든 html이 다르면 에러를 뱉는다.
      6. 위 작업에서도 fetch를 이용해 데이터를 가져와야 한다.

      위에서 fetch 시점의 차이로 인한 결과물의 불일치가 발생할 수 있으므로 1번 fetch에서 가져온 정보를 html결과물 내에 script형태로 내려준다.6번에서는 재요청을 보내는 대신 script를 읽는다.

  - getInitialProps

### 스타일 적용하기

- 전역 스타일
- 컴포넌트 레벨 css - [name].module.css
- scss, sass
- css in js

# 📚 8장 좋은 리액트 코드 작성을 위한 환경 구축하기

## ESlint

- espree라는 파서를 이용해 코드를 구조화
- eslint-plugin: import와 같이 특정 프레임워크나 도메인 관련 규칙을 묶어서 제공하는 패키지
- eslint-config: 위와 같은 eslint-plugin을 한데 묶어서 완벽하게 한 세트로 제공하는 패키지
  - 직접 만들 수 있으나 매우 번거로움. eslint-config뒤에 접두사를 붙이는 형태로 네이밍해야하며, 반드시 한 단어로 구성
  - eslint-config-airbnb
  - @titicaca/triple-config-kit
  - eslint-config-next: 단순히 자바스크립트 코드를 정적으로 분석할 뿐 아니라 페이지나 컴포넌트에서 반환하는 jsx구문 및 _app, _document 파일 또한 정적 분석 대상으로 분류해 제공,
  - 핵심 웹 지표(core web vitals)를 분석해 제공

### 이미 있는 규칙을 커스터마이징해서 적용하기

- import React 제거하기
- lodash

```jsx
module.exports = {
  rules: {
    'no-restricted-imports': {
      'error',
      {
        paths: [
          {
            name: 'react',
            importNames: ['default'],
            message: "import React from 'react'는 react 17부터 더 이상 필요하지 않습니다. 필요한 것만 react import해서 사용해주세요"
          }
        ]
      }
    }
  }
}
```

### 새로운 규칙 만들기

### 주의할 점

- Prettier와의 충돌
  - 서로 규칙이 충돌되지 않게 규칙 설정하기
    - Prettier에서 제공하는 규칙을 ESLint에서 끈다.
  - 자바스크립트나 타입스크립트는 ESLint에, 그 외의 파일은 Prettier에 맡긴다.
    - eslint-plugin-prettier: Prettier에서 제공하는 모든 규칙을 ESLint에서 사용할 수 있는 규칙으로 만든 플러그인
  - ESLint 버전 충돌
    - 설치하고자하는 eslint-config, eslint-plugin이 지원하는 ESLint버전을 확인하고 설치하고자하는 프로젝트에서 지원하는 ESLint 버전을 확인하고 두 버전 모두를 충족시킬 수 있는 환경을 만들어두어야한다.
    - ESLint의 의존성은 peerDependencies로 명시하는 것을 권장한다.

- 특정 규칙에 대한 예외처리하기
  - `eslint-disable-*`주석을 사용한다.

### eslint-disable-line no-exhaustive-deps 주석을 사용하지 말하야하는 경우

- 괜찮다고 임의로 판단한 경우: 해당 변수는 컴포넌트의 상태와 별개로 동작한다는 것을 의미한다.
- 의존성배열이 너무 긴 경우: useEffect를 쪼개야한다는 것을 의미한다.
- 마운트 시점에 한 번만 실행하고 싶은 경우: 
  - []배열이 있다는 것은 컴포넌트의 상태값과 별개의 부수효과가 되어 컴포넌트의 상태와 불일치가 일어날 수 있다.
  - 상태와 관계없이 한 번만 실행되어야하는 것이 있다면 해당 컴포넌트에 존재할 이유가 없다.


해결 방법)

1. Stateful dependency
   내부에 접근하는 상태가 있어서 종속성 배열에 추가해야하는 경우, 다음과 같이 setState함수의 콜백함수를 수정하여 해결할 수 있다.
   
   ```jsx
   useEffect(() => {
    setCount(count + 1);
    // eslint-disable-next-line
   }, []);

   // fix
  useEffect(() => {
   setCount((count) => count + 1);
   }, []);
   ```

2. Function dependency

  아래와 같은 상황에서 종속성 배열에 함수를 추가해야한다는 warning이 발생한다.

  ```jsx
  const someFunction = (count) => {
    // do something with count
  };

  useEffect(() => {
    someFunction(count);
  }, [count]);
  ```
  그러나 someFunction을 종속성 배열에 추가하면 무한 루프가 발생하게 된다. 왜냐하면 someFunction함수는 렌더링이 일어날 때마다 서로 다른 값을 참조하기 때문이다.

  이를 해결하기 위해서는 someFunction함수를 useCallback 훅으로 감싸 각각의 렌더링 시에 someFunction 함수의 참조값이 변경되지 않도록 보장해야한다.
  ```jsx
  const someFunction = useCallback((count) => {
    // do something with count
  }, []);

  useEffect(() => {
    someFunction(count);
  }, [count, someFunction]);
  ```

3. 위 방법들로 해결이 안될 경우,,
   useEffect 콜백 함수 내에서 기본적인 비교 구문을 통해 필요한 경우 로직을 early return으로 빠르게 끝내는 방법을 사용할 수 있다.
   ```jsx
   useEffect(() => {
    if (post.id === undefined) {
      return;
    }
    doSomethingWithPost(post)
   }, [post]);
   ```

## 리액트 테스트 라이브러리

> 테스트가 이뤄야할 목표는 애플리케이션이 비즈니스 요구사항을 충족하는지 확인하는 것이다.

- 테스팅: 개발자가 만든 프로그램이 코딩을 한 의도대로 작동하는지 확인하는 일련의 작업
  - 처음에 설계한 대로 프로그램이 작동하는가?
  - 버그를 사전에 방지
  - 잘못된 작동으로 인해 발생하는 비용을 줄일 수 있다.
  - 코드 수정 시 수정된 내용에 예외케이스가 없는지 확인할 수 있다.
- 백엔드의 테스팅
  - 서버나 데이터베이스에서 데이터를 올바르게 가져오는가?
  - 데이터 수정 간 교착상태 발생?
  - 등 화이트박스 테스트.
- 프론트엔드의 테스팅
  - 일반적인 사용자와 동일하거나 유사한 환경에서 수행됨.
  - 블릭박스 형태 테스트
  - 코드와 상관없이 의도대로 작동하는지에 초점이 맞춰짐.
  - 브라우저에서 발생할 수 있는 다양한 시나리오에서 어떻게 작동할 지 최대한 예측해서 확인해야 함.
  
  ### 리액트 테스팅 라이브러리
  - Dom Testing Library > jsdom을 기반으로 구현되어있음.
    - 자바스크립트 환경에서도 HTML을 사용할 수 있음.
  - 어설션(assertion)라이브러리 - 테스트 결과를 확인할 수 있도록 도와주는 라이브러리.
  - 테스팅 프레임워크(ex. Jest, Mocha,,) - 테스트에 대한 결과와 관련 정보를 일목요연하게 확인할 수 있다.
    - 무엇을 테스트했는지, 소요된 시간은 어느정도인지, 무엇이 성공하고 실패했는지,,,
  
- 컴포넌트 테스팅
  - getBy~: 인수의 조건에 맞는 요소를 반환한다.
  - findBy~
  - queryBy~
  - beforeEach
  - describe
  - it
  - testId
  
- fetch() 테스트하기
  - jest.spyOn()을 이용해 fetch를 모킹한다.
  - MSW(Mock Service Worker): 브라우저와 Nodejs 모두에서 사용할 수 있는 모킹 라이브러리.

- react-hooks-testing-library
  - renderHook메서드 내부에서 컴포넌트를 생성하여 훅을 실행하여 테스트한다. 

### 테스트를 작성할 때 고려해야할 점.

- 애플리케이션에서 가장 취약하거나 중요한 부분을 파악한다.
- 테스트 커버리지를 맹신하지 말자.



