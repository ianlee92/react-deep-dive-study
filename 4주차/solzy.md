### 📅 2024년 4월 3일

# 📚 4장 서버 사이드 렌더링
## 4.1 서버 사이드 렌더링이란?
서버사이드 렌더링 애플리케이션과 반대되는 개념인 **싱글 페이지 애플리케이션** 에 대해 알아보자.

### SPA (Single Page Application)
- 렌더링과 라우팅에 필요한 대부분의 기능을 브라우저의 **자바스크립트에 의존하는 방식**
- 페이지를 불러온 이후에는 서버에서 HTML을 내려받지 않고, 하나의 페이지에서 모든 작업을 처리한다.
   - 그래서, 싱글 페이지 애플리케이션 이라고 한다.

- 사이트 렌더링에 필요한 모든 정보들을 자바스크립트 코드로 삽입하기 때문에, 실제로 html코드를 보면 `<body/>` 내부엔 아무것도 없다.
- 최초에 서버에 최대한 데이터를 불러온 이후 부터는 이미 가지고 있는 자바스크립트 리소스와 브라우저 API기반으로 동작

### 그에 반해, 전통적인 웹 애플리케이션은..
- 페이지 전환이 발생할 때 마다 새롭게 페이지를 서버에 요청하고, HTML 페이지를 다운로드해 파싱하는 작업을 거친다.
- 서버에서 html을 다시 그리기 때문에, 네트워크 상황이 느리면 그려지기 전 까지 흰화면이 보여질 수 있다.

### 서버사이드 렌더링이란?
- 최초에 사용자에게 보여줄 페이지를 서버에서 렌더링해 빠르게 사용자에게 화면을 제공하는 방식
- 싱글 페이지 애플리케이션과 서버 사이드 렌더링의 차이는 웹페이지 **렌더링의 책임을 어디에 두느냐**

**[장점]**
- 최초 페이지 진입이 비교적 빠르다
- 검색엔진과 SNS공유 등 메타데이터 제공이 쉽다.
- 누적 레이아웃 이동이 적다
  - 사용자에게 페이지를 보여준 이후 뒤늦게 어떤 HTML정보가 추가되거나 삭제되어 마치 화면이 덜컥거리는 것과 같은 부정적인 사용자 경험
- 사용자의 디바이스 성능에 비교적 자유롭다
- 보안에 좀 더 안전하다

**[단점]**
- 소스코드를 작성할 때 항상 서버를 고려해야 한다
  - `window`, `sessionStorage` 접근시 주의 !
- 적절한 서버가 구축되어 있어야 한다.
- 서비스 지연에 따른 문제

### SPA / SSR 을 모두 알아야 하는 이유
- 서버사이드 렌더링은 만능이 아니다.
- 웹페이지에서 사용자에게 제공하고 싶은 내용이 무엇인지,
- 또 어떤 우선순위에 따라 페이지 내용을 보여줄 지 잘 설계하는 것이 중요 !

----
## 4.2 서버 사이드 렌더링을 위한 리액트 API 살펴보기

### renderToString
- 리액트 컴포넌트를 렌더링해 HTML문자열 로 반환하는 함수

```typescript jsx
const html = renderToString(<MyIcon />);
console.log(html); // 예를 들어, "<svg>...</svg>"
```

- hook이나 이벤트 핸들러는 결과물에 포함되지 않는다.
- 왜냐면, 빠르게 브라우저가 렌더링 할 수 있는 HTML을 제공하는 데 목적이 있는 함수이기 때문.
- 먼저 완성된 HTML을 서버에 제공할 수 있으므로, 초기 렌더링에서 뛰어난 성능을 보일 것이다.

### renderToStaticMarkup
- `renderToString` 과 유사하지만, 리액트에서만 사용하는 추가적인 DOM 속성을 만들지 않는다.

### renderToNodeStream
- `renderToString` 과 결과물이 완전히 동일하지만 두가지 차이점이 있다.
  - 1. `renderToNodeStream` 은 브라우저에서 사용하는 것이 불가능하다.
  - 2. `renderToNodeStream` 의 결과물은 Node.js 의 `ReadableStream`이다.

**어떨때 사용할까?**
- 생성해야 하는 HTML 의 크기가 클 때

### renderToStaticNodeStream
- `renderToNodeStream` 과 결과물은 동일하지만, 자바스크립트에 필요한 리액트 속성이 추가되지 않는다.

### hydrate
- `renderToString` , `renderToNodeStream` 으로 생성된 HTML에 자바스크립트 핸들러나 이벤트를 붙이는 역할
- 정적으로 생성된 html 에 이벤트와 핸들러를 붙일 수 있다.
  - 이와 유사하게, React render 함수가 있다.

```typescript jsx
const rootElement = document.getElementById('root')
ReactDom.render(<App/>, rootElement)
```

**hydrate**
- 기본적으로 이미 렌더링 된 HTML이 있다는 가정하에 작업이 수행
- 단순히 이벤트나 핸들러를 추가하는 것 이외에도 렌더링을 한번 수행하면서 hydrate가 수행한 렌더링 결과물 HTML 과 인수로 넘겨받은 HTML 을 비교하는 작업을 수행한다. 

### 4.2.6 서버 사이드 렌더링 예제 프로젝트
> https://github.com/wikibook/react-deep-dive-example/tree/main/chapter4/ssr-example

**index.tsx**
```typescript jsx
async function main() {
  const result = await fetchTodo()

  const app = <App todos={result} />
  const el = document.getElementById('root')

  hydrate(app, el)
}

main()
```

- 서버로 부터 받은 HTML을 hydrate를 통해 웹 애플리케이션으로 만든다.

**index.html**
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>SSR Example</title>
  </head>
  <body>
    __placeholder__ // 서버에서 리액트 컴포넌트를 기반으로 만든 HTML 코드를 삽입하는 자리
    <script src="https://unpkg.com/react@17.0.2/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@17.0.2/umd/react-dom.development.js"></script>
    <script src="/browser.js"></script> // 클라이언트 리액트 애플리케이션 코드를 번들링했을 때 제공되는 리액트 자바스크립트 코드
  </body>
</html>
```
- 서버사이드 렌더링을 수행할 때 기본이 되는 HTML 템플릿

**server.ts**

1.createServer
```typescript
  createServer(serverHandler).listen(PORT, () => {
    console.log(`Server has been started ${PORT}...`) // eslint-disable-line no-console
  })
```
- http 모듈을 이용해 간단한 서버를 만들 수 있는 노드 기본 라이브러리
- 3000 번 포트를 이용하는 서버를 만들었다고 생각하면 된다.

2. serverHandler
```typescript
async function serverHandler(req: IncomingMessage, res: ServerResponse) {
  const { url } = req

  switch (url) {
    case '/': { ....
```
- HTTP 서버가 라우트별로 어떻게 작동할 지 정의하는 함수

3. server.ts의 root router

```typescript
 case '/': {
      const result = await fetchTodo()

      const rootElement = createElement(
        'div',
        { id: 'root' },
        createElement(App, { todos: result }),
      )
      const renderResult = renderToString(rootElement)

      const htmlResult = html.replace('__placeholder__', renderResult)

      res.setHeader('Content-Type', 'text/html')
      res.write(htmlResult)
      res.end()
      return
    }
```

- 사용자가 `/` 로 접근했을 때 실행되는 코드
- `renderToString` 으로 리액트 컴포넌트를 html로 만들었고,
- 앞서 언급한 `__placeholder__`를 대상으로 실행해 서버 응답으로 제공했다.


**webpack.config.js**
```typescript

/** @type WebpackConfig[] */
const configs = [
  {
    entry: {
      browser: './src/index.tsx', // 브라우저의 경우 entry => 시작점
    },
    output: {
      path: path.join(__dirname, '/dist'), // 결과물 저장경로
      filename: '[name].js',
    },
    resolve: {
      extensions: ['.ts', '.tsx'],
    },
    devtool: 'source-map',
    module: {
      rules: [
        {
          test: /\.tsx?$/,
          loader: 'ts-loader', // 필요한 파일 로더
        },
      ],
    },
    externals: {
      react: 'React',
      'react-dom': 'ReactDOM', // 제외할 내용
    },
  },
  {
    entry: {
      server: './src/server.ts',
    },
    output: {
      path: path.join(__dirname, '/dist'),
      filename: '[name].js',
    },
    resolve: {
      extensions: ['.ts', '.tsx'],
    },
    devtool: 'source-map',
    module: {
      rules: [
        {
          test: /\.tsx?$/,
          loader: 'ts-loader',
        },
        {
          test: /\.html$/,
          use: 'raw-loader',
        },
      ],
    },
    target: 'node',
    externals: [nodeExternals()],
  },
]

module.exports = configs
```

- `create-react-app` 으로 만들면 이러한 설정을 내부에서 모두 대신해줌

## 4.3 Next.js 톺아보기

```shell
npx create-next-app@latest --ts
```

### **package.json**
- 프로젝트 구동에 필요한 모든 명령어 및 의존성이 포함되어 있음.
```json
{
  "name": "deep-dive-next-js-part",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
  "dependencies": {
    "react": "^18",
    "react-dom": "^18",
    "next": "14.1.4" // 책과 다르게 14버전 ㅠ
  },
  "devDependencies": {
    "typescript": "^5",
    "@types/node": "^20",
    "@types/react": "^18",
    "@types/react-dom": "^18",
    "eslint": "^8",
    "eslint-config-next": "14.1.4" // next.js 기반 프로젝트에서 사용하도록 만들어진 ESLint 설정
  }
}

```

### next.config.js
- 난 왜 확장자가 `.mjs` 일까..?
> mjs 와 .js는 다양한 유형의 JavaScript 파일을 나타내는 데 사용되는 파일 확장자
> 1. js (Javascript)
>  - .js 확장자는 JavaScript 파일의 가장 일반적인 파일 확장자 
>  - .js 의 의므는 파일에 JavaScript 코드가 포함되어 있음을 나타내는데 사용이 되고 이는 웹 브라우저, 서버 및 기타 다양한 JavaScript 환경에서 실행할 수 있음
>  2. .mjs(ECMAScript Modules)
>   - MJS는 정석대로 ECMAScript 모듈(ESM) 사양을 준수하는 JavaScript 파일에 사용되는 확장자
>   - ECMAScript 모듈은 JavaScript 언어 표준의 일부고 모듈을 정의하는 보다 현대적이고 표준화된 방법을 제공.

=> 그냥 가장 현대적인 방법인 .mjs 로 생성된것 뿐...


```typescript
/** @type {import('next').NextConfig} */ => 자바스크립트 파일에 타입스크립트의 타입을 도움받기 위해 추가된 코드
const nextConfig = {
          reactStrictMode: true, // 리액트의 엄격 모드 
          swcMinify: true // SWC는 번들링과 컴파일을 더욱 빠르게 수행하기 위해 만들어짐. 바벨의 대안. 왜 빠르냐면, 러스트로 작성되어 있고, 병렬로 작업을 처리.
                          // 즉, swc를 기반으로 코드 최소화 작업을 할 것인지 여부를 설정하는 속성
        };

export default nextConfig;

```

### _app.tsx
```typescript jsx
import type { AppProps } from 'next/app'

export default function App({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />
}
```
- 애플리케이션의 전체 페이지 시작점
  - 에러 바운더리를 사용해 전역에서 발생하는 에러 처리
  - reset.css 같은 전역 css선언
  - 모든 페이지에서 공통으로 사용 혹은 제공해야 하는 데이터 제공
- 최초에는 서버 사이드 렌더링을, 이후에는 클라이언트에서 _app.tsx의 렌더링이 실행된다.

### _document.tsx
```typescript jsx
import { Html, Head, Main, NextScript } from 'next/document'

export default function Document() {
  return (
    <Html lang="en">
      <Head />
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  )
}
```
- html을 초기화하는 곳
- html , body 에 dom속성을 추가하고 싶을 때
- 무조건 서버에서 실행됨
- head에는 SEO에 필요한 정보나 title 등을 담을 수 있음.
- `<Head/>` 에는 `<title/>` 을 사용할 수 없음.

- app.tsx 는 next.js를 초기화하는 파일. next.js의 설정과 관련된 코드를 모아둠. 그래서 서버와 클라이언트 모두에서 렌더링될 수 있다.
- _document.tsx 는 Next.js로 만드는 웹사이트의 뼈대가 되는 HTML 설정과 관련된 코드를 추가하는 곳이며 반드시 서버에서만 렌더링된다.

### _error.tsx
- 에러를 처리할 목적
- 프로덕션 빌드로 확인해봐야함.
- 
### 404.tsx
- 404페이지 정의

### 500.tsx
- 서버에서 발생하는 에러 핸들링
- error 와 500페이지 둘다 있다면, 500페이지 먼저 실행


# 📚 8장 좋은 리액트 코드 작성을 위한 환경 구축하기
