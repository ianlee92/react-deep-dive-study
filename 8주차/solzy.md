### 📅 2024년 5월 8일

# 📚 14장 웹사이트 보안을 위한 리액트와 웹페이지 보안 이슈
## 14.1 리액트에서 발생하는 크로스 사이트 스크립팅(XSS)
### XSS(Cross-Site Scripting) 이란?
- 제 3자가 웹사이트에 악성 스크립트를 삽입해 실행할 수 있는 취약점

#### 14.1.1 `dangerouslySetInnerHTML` props
`dangerouslySetInnerHTML`
- 특정 브라우저 DOM의 innerHTML을 특정한 내용으로 교체할 수 있는 방법

```typescript
const html = `<span><svg/onload=alert(origin)></span>`
<div dangerousSetInnerHTML={{_html: html}}/>
```
- _html 로 넘긴 문자열을 그대로 DOM에 표시함.
- => **인수로 받는 문자열에 제한이 없다!**

#### 14.1.2 useRef를 활용한 직접 삽입
- useRef를 사용해 직접 DOM에 접근할 수 있으므로 보안에 취약하다.

#### 14.1.3 리액트에서 XSS 문제를 피하는 방법
- HTML을 안전한 HTML코드로 한 번 치환하는 것
- => sanitize (새니타이즈), escape (이스케이프)

**리액트의 JSX 데이터 바인딩**
```
const html = `<span><svg/onload=alert(origin)></span>`
funtion App() {
  return <div>{html}</div>
}
```
- 리액트는 기본적으로 이스케이프 작업을 해줌

## 14.2 getServerSideProps와 서버컴포넌트를 주의하자
- 서버컴포넌트가 클라이언트 컴포넌트에 반환하는 props는 반드시 필요한 값으로만 철저하게 제한되어야 한다.

## 14.3 <a> 태그의 값에 적절한 제한을 둬야 한다.
```typescript
function App() {
  function handleClick() {
    log('HELLO')
  }
  return <a href="javascript:;" onClick={handleClick}>뭐임</a>
}
```
`javascript;;` 를 `href`에 쓰는 것
- a태그의 기본 기능인 href로 선언된 URL로  페이지를 이동하는 것을 막고 onClick 이벤트와 같이 별도 이벤트 핸들러만 작동시키기 위한 용도로 사용

## 14.4 HTTP 보안 헤더 설정하기

#### 14.4.1 Strict-Trasport-Security
모든 사이트가 HTTPS를 통해 접근해야 하며, 만약 HTTP로 접근하는 경우 모든 시도는 HTTPS로 변경하게 한다.

#### 14.4.2 X-XSS-Protection
사파리와 구형 브라우저에서만 제공
- XSS취약점이 발견되면 페이지 로딩을 중단함.

#### 14.4.3 X-Frame-Options
네이버와 비슷한 주소를 가진 페이지가 있고, 이 페이지에서 네이버를 iframe으로 렌더링한다고 가정해볼 때,
- 사용자는 이 페이지를 진짜 네이버로 오해할 수 있음.
- => 이처럼 외부에서 자신의 페이지를 위와 같은 방식으로 삽입되는 것을 막아줌

#### 14.4.4 Permissions-Poliy
웹사이트에서 사용할 수 있는 기능과 사용할 수 없는 기능을 명시적으로 선언하는 헤더.
- 여기서 말하는 기능이란, 카메라나 GPS와 같이 브라우저가 제공하는 기능을 말함.

#### 14.4.5 X-Content-Type-Options
- MIME: Content-type의 값으로 사용
X-Content-Type-Options란, Content-type 헤더에서 제공하는 MIME유형이 브라우저에 의해 임의로 변경되지 않게 하는 헤더

#### 14.4.6 Referrer-Policy
HTTP에는 Referer라는 헤더가 존재하는데, 이 헤더에는 현재 요청을 보낸 페이지의 주소가 나타난다.
이 헤더는 사용자가 어디서 와서 방문 중인지를 인식할 수 있지만, 사용자의 입장에서는 원치 않는 정보가 노출될 위험도 존재한다.

#### 14.4.7 Content-Security-Policy
XSS 공격이나 데이터 삽입 공격과 같은 다양한 보안 위협을 막기 위해 설계됨.




# 📚 15장 마치며
