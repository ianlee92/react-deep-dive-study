### 📅 2024년 4월 3일

# 📚 4장 서버 사이드 렌더링

### p.263

- 싱글 페이지 애플리케이션과 서버에서 페이지를 빌드하는 서버 사이드 렌더링의 차이는 웹페이지 렌더링의 책임을 어디에 두느냐다.
- 싱글 페이지 애플리케이션은 사용자에게 제공되는 타입스크립트 번들에서 렌더링을 담당한다.
- 서버 사이드 방식을 채택하면 렌더링에 필요한 작업을 모두 서버에서 수행한다.

### p.264~267

## 서버 사이드 렌더링의 장점

- 최초 페이지 진입이 비교적 빠르다.
  - 일반적으로 서버에서 HTTP 요청을 수행하는 것이 더 빠르고, 또 HTML을 그리는 작업도 서버에서 해당 HTML을 문자열로 미리 그려서 내려주는 것이 클라이언트에서 기존 HTML에 삽입하는 것보다 더 빠르기 때문이다.
- 검색 엔진과 SNS 공유 등 메타데이터 제공이 쉽다.
  - 서버 사이드 렌더링은 최초의 렌더링 작업이 서버에서 일어난다. 즉, 검색 엔진에 제공할 정보를 서버에서 가공해서 HTML 응답으로 제공할 수 있으므로 검색 엔진 최적화에 대응하기가 매우 용이하다.
- 누적 레이아웃 이동이 적다.
  - 누적 레이아웃 이동이란 사용자에게 페이지를 보여준 이후에 뒤늦게 어떤 HTML 정보가 추가되거나 삭제되어 마치 화면이 덜컥거리는 것과 같은 부정적인 사용자 경험을 말한다.
- 사용자의 디바이스 성능에 비교적 자유롭다.
  - 서버 사이드 렌더링을 수행하면 부담을 서버에 나눌 수 있기 때문이다.
- 보안에 좀 더 안전하다.
  - 서버 사이드 렌더링의 경우 인증 혹은 민감한 작업을 서버에서 수행하고 그 결과만 브라우저에 제공해 이러한 보안 위협을 피할 수 있다는 장점이 있다.

## 서버 사이드 렌더링의 단점

- 소스코드를 작성할 때 항상 서버를 고려해야 한다.
- 적절한 서버가 구축돼 있어야 한다.
- 서비스 지연에 따른 문제.

### p.270~278

## 서버 사이드 렌더링을 위한 리액트 API 살펴보기

### renderToString

- 인수로 넘겨받은 리액트 컴포넌트를 렌더링해 HTML 문자열로 반환하는 함수다.
- 클라이언트에서 실행되지 않고 일단 먼저 완성된 HTML을 서버에서 제공할 수 있으므로 초기 렌더링에서 뛰어난 성능을 보인다.

### renderToStaticMarkup

- renderToString과 유사한 함수로 두 함수 모두 리액트 컴포넌트를 기준으로 HTML 문자열을 만든다는 점에서 동일하다.
- 리액트의 이벤트 리스너가 필요 없는 완전히 순수한 HTML을 만들 때만 사용된다.

### renderToNodeStream

- renderToString과 결과물이 완전히 동일하지만 두 가지 차이점이 있다.
- 위 두 API는 브라우저에서도 실행할 수 있지만 renderToNodeStream은 브라우저에서 사용하는 것이 완전히 불가능하다.
- renderToString의 결과물은 string인 문자열이지만, renderToNodeStream의 결과물은 Node.js의 ReadableStream이다. utf-8로 인코딩된 바이트 스트림으로 Node.js나 Deno, Bun 같은 서버 환경에서만 사용할 수 있다.
- renderToString이 생성하는 HTML 결과물의 크기가 매우 크다면 이렇게 크기가 큰 문자열을 한번에 메모리에 올려두고 응답을 수행해야 해서 Node.js가 실행되는 서버에 큰 부담이 될 수 있는데 스트림을 활용하면 큰 크기의 데이터를 청크(chunk, 작은 단위)로 분리해 순차적으로 처리할 수 있다는 장점이 있다.
- 대부분의 널리 알려진 리액트 서버 사이드 렌더링 프레임워크는 모두 renderToString 대신 renderToNodeStream을 채택하고 있다.

### renderToStaticNodeStream

- renderToStaticMarkup과 마찬가지로 리액트 자바스크립트에 필요한 리액트 속성이 제공되지 않는다.
- 마찬가지로 hydrate를 할 필요가 없는 순수 HTML 결과물이 필요할 때 사용하는 메서드다.

### hydrate

- renderToString과 renderToNodeStream으로 생성된 HTML 콘텐츠에 자바스크립트 핸들러나 이벤트를 붙이는 역할을 한다.
- render와의 차이점은 기본적으로 이미 렌더링된 HTML이 있다는 가정하에 작업이 수행되고, 이 렌더링된 HTML을 기준으로 이벤트르 붙이는 작업만 실행한다.

### p.299

## \_app.tsx와 \_document.tsx의 차이점

- \_app.tsx가 애플리케이션 페이지 전체를 초기화하는 곳이라면, \_document.tsx는 애플리케이션의 HTML을 초기화하는 곳이다.
- \_app.tsx는 렌더링이나 라우팅에 따라 서버나 클라이언트에서 실행될 수 있지만, \_document는 무조건 서버에서실행된다. 따라서 \_document 파일에서 onClick과 같은 이벤트 핸들러를 추가하는 것은 불가능하다. 이벤트를 추가하는 것은 클라이언트에서 실행되는 hydrate의 몫이기 때문이다.
- getServerSideProps, getStaticProps 등 서버에서 사용 가능한 데이터 불러오기 함수는 사용할 수 없다.

### p.307

- Next.js는 서버 사이드 렌더링의 장점, 즉 사용자가 빠르게 볼 수 있는 최초 페이지를 제공한다는 점과 싱글 페이지 애플리케이션의 장점인 자연스러운 라우팅이라는 두 가지 장점을 갖고있다.

### p.310~319

## getStaticPaths와 getStaticProps

- 두 함수는 어떻나 페이지를 CMS(Contents Management System)나 블로그, 게시판과 같은 사용자와 관계없이 정적으로 결정된 페이지를 보여주고자 할 때 사용되는 함수다.
- 두 함수는 반드시 함께 있어야 사용할 수 있다.

```tsx
import { GetStaticPaths, GetStaticProps } from "next";

export const getStaticPaths: GetStaticPaths = async () => {
  return {
    // 이 페이지는 /post/1과 /post/2만 접근 가능하며 이외의 페이지는 404를 반환한다.
    paths: [{ params: { id: "1" } }, { params: { id: "2" } }],
    fallback: false,
  };
};

export const getStaticProps: GetStaticProps = async ({ params }) => {
  const { id } = params;

  const post = await fetchPost(id);

  // 예제에서는 id가 각각 1과 2로 제한돼 있기 때문에 fetchPost(1), fetchPost(2)를 기준으로 각각 함수의 응답 결과를 변수로 가져와 props의 { post }로 반환하게 된다.

  return {
    props: { post },
  };
};

export default function Post({ post }: { post: Post }) {
  // post로 페이지를 렌더링한다.
}
```

- getStaticPaths는 접근 가능한 주소를 정의하는 함수다.
- getStaticProps는 앞에서 정의한 페이지를 기준으로 해당 페이지로 요청이 왔을 때 제공할 props를 반환하는 함수다.

## getServerSideProps

- 앞선 두 함수가 정적인 페이지 제공을 위해 사용된다면, getServerSideProps는 서버에서 실행되는 함수이며 해당 함수가 있다면 무조건 페이지 진입 전에 이 함수를 실행한다.
- 이 함수가 있다면 Next.js는 꼭 서버에서 실행해야 하는 페이지로 분류해 빌드 시에도 서버용 자바스크립트 파일을 별도로 만든다.
- 이 함수는 사용자가 매 페이지를 호출할 때마다 실행되고, 이 실행이 끝나기 전까지는 사용자에게 어떠한 HTML도 보여줄 수 없다. 따라서 getServerSideProps 내부에서 실행하는 내용은 최대한 간결하게 작성하기 위해 꼭 최초에 보여줘야 하는 데이터가 아니라면 클라이언트에서 호출하는 것이 더 유리하다.
- getServerSideProps에는 반드시 해당 페이지를 렌더링하는 데 있어 중요한 역할을 하는 데이터만 가져오는 것이 좋다.
- 어떤 조건에 따라 다른 페이지로 보내고 싶다면 redirect를 사용할 수 있다.

# 📚 8장 좋은 리액트 코드 작성을 위한 환경 구축하기
