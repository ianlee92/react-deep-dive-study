### 📅 2024년 4월 17일

# 📚 9장 모던 리액트 개발 도구로 개발 및 배포 환경 구축하기

## create-next-app 없이 애플리케이션 구축하기

1. package.json 파일 만들기

```shell
npm init
```

2. 라이브러리 설치

```shell
npm install react react-dom next
```

```shell
npm install @types/node @types/react @types/react-dom eslint eslint-config-next typescript react-dom next --save-dev
```

3. tsconfig.json 작성

```json
{
    // 해당 json파일이 무엇을 의미하는지, 어떤 키와 어떤 값이 들어갈 수 있는지 알려준다.
    $schema: "https://json.schemastore.org/tsconfig.json",
}
```

- complierOptions: 타입스크립트를 자바스크립트로 컴파일할 때 사용하는 옵션
  - target: 타입스크립트가 변환을 목표로 하는 언어의 버전
  - lib: target의 버전이 낮더라도 esnext를 추가하면 신규 기능에 대한 API 정보를 확인할 수 있음. dom을 추가하여 window, document 등 브라우저 위주의 API에 대한 명세를 사용할 수 있음.
  - jsx: jsx 컴파일 방법을 지정한다.
    - react-jsx: 리액트 17에서 등장한 방식으로 React.createElement를 사용하지 않아 상단에 import React from 'react'를 사용하지 않아도 된다.
  - path: 폴더의 상대 경로를 지정할 수 있다.

4. next.config.js 작성

5. ESLint와 Prettier 설정하기

## Github 활용하기

- CI: 코드의 변화를 모으고 관리하는 코드 중앙 저장소에서 여러 기여자가 기여한 코드를 지속적으로 빌드하고 테스트해 코드의 정합성을 확인하는 과정. 이 작업은 자동으로 실행되어야한다.
- 테스트, 빌드, 정적 분석, 보안 취약점 분석
- 깃허브 액션: 깃허브를 둘러싼 이벤트를 기반으로 깃허브에서 제공하는 가상 환셩에서 사용자가 원하는 작업을 수행할 수 있도록 도와주는 서비스

- 러너: 깃허브 액션이 실행되는 서버
- 액션: 러너에서 실행되는 하나의 작업 단위
- 이벤트: 깃허브 액션의 실행을 일으키는 이벤트
  - pull_request, issues, push, schedule
- jobs: 하나의 러너에서 실행되는 여러 스텝의 모음
- step: 잡에서 일어나는 하나 하나의 작업

### calibreapp/image-actions - 저장소 내 이미지 최적화

- 이미지 저장소를 내부에 두고 관리하는 경우, 저장소에 포함돼 있는 이미지를 최적화하는 액션
- PR로 올라온 이미지를 sharp패키지를 이용해 무손실 압축

### lirantal/is-website-vulnerable - 웹사이트 취약점 진단

- 특정 웹사이트를 방문해 해당 웹사이트에 라이브러리 취약점이 존재하는지 확인하는 액션
- npx 명령어로도 실행 가능

```shell
npx is-website-vulnerable https://www.netflix.com/kr/
```

### package.json dependencies

- **버전**
  - react@16.0.0: 정확히 해당 버전에 대해 의존하고있다.
  - react@^16.0.0: 16.0.0과 호환되는 버전. 즉 16.0.0부터 17.0.0미만의 모든 버전과 호환
  - react@~16.0.0: 패치버전에 대해서만 호환됨. 즉 16.0.0부터 16.1.0미만의 모든 버전과 호환

가능한 한 내재화할 수 있는 모듈을 내재화하고, 의존성을 최소한으로 유지한다.
가능한 한 널리 알려져있고 많은 사람들이 사용하는, 그리고 활발하게 유지보수되는 패키지를 사용해야 한다.


## 리액트 어플리케이션 배포하기

1. Netlify
2. Vercel
3. Digit Ocean

## 리액트 애플리케이션 도커라이즈 하기

- 도커: 서비스 운영에 필요한 애플리케이션을 격리해 컨테이너로 만드는 데 이용하는 소프트웨어
- 도커라이즈: 애플리케이션을 도커 이미지로 만드는 과정





