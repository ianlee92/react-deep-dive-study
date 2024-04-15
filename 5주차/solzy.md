### 📅 2024년 4월 17일

# 📚 9장 모던 리액트 개발 도구로 개발 및 배포 환경 구축하기
## 9.1 Next.js 로 리액트 개발환경 구축하기
- `create-react-app` : 리액트 애플리케이션을 구축할 수 있는 CLI 도구
  - 유지보수 되지 않을 가능성이 큼.

### 9.1.2 tsconfig.json 작성하기
#### 사용된 옵션
- `strictNullChecks`: 엄격한 널 검사를 활성화함.
  - 이 옵션을 켜두면, null 과 undefined 를 명확하게 구분해 사용할 수 있게 됨.
  - 켜두는 것을 권장

- `strictBindCallApply`: 함수에 대해 사용할 수 있는 call, bind, apply에 대해 정확한 인수를 요구하는 옵션
  - 런타임 에러를 방지하므로 켜두는 것을 권장

- `strictFunctionTypes`: 함수의 인수가 다르다는 에러를 발생
  - 켜두는 것을 권장.

- `useUnknownInCatchVariables`: catch구문에서 잡은 변수에 대해서는 기본적으로 any를 할당한다. 그러나 4.0부터는 unknown을 할당한다.
  - 켜두는 권장

- `noEmit`: 컴파일을 하지 않고, 타입 체크만 한다. Next.js는 swc가 타입스크립트 파일을 컴파일 하므로 굳이 필요가 없음.   
  - 이 옵션이 켜져있으면, 단순히 타입 검사만 하는 역할을 함.

- `jsx`: tsx파일 내부에 있는 JSX를 어떻게 컴파일 할 지 설정한다.
  - preserve: 변환하지 않고 그대로 유지한다.
  - swc가 JSX또한 변환해줌.

- `incremental`: 이 옵션이 활성화 하면 타입스크립트는 마지막 컴파일 정보를 디스크에 저장함.


#### 우리 프로젝트는 어떻게 구성되어 있나..
```json
{
  "compilerOptions": { // 타입스크립트를 자바스크립트로 컴파일 할 때 사용하는 옵션
    "target": "es6", // 타입스크립트가 변환을 목표로 하는 언어의 버전 (es6: 화살표함수 까지 포함..)
    "lib": [ // target에서 해결하지 못하는 것들을 보충해주는..
      "dom", // 브라우저 위주의 API에 대한 명세를 사용할 수 있도록
      "dom.iterable",
      "esnext" // es 최신버전을 의미함.
    ],
    "allowJs": true, // 자바스크립트 파일 또한 컴파일 할 지..
    "skipLibCheck": true, // d.ts 검사 여부
    "strict": true, // 엄격모드
    "forceConsistentCasingInFileNames": true, // 파일이름의 대소문자를 구분하도록 강제
    "noEmit": true, // 컴파일 하지 않도록 타입체크만!
    "esModuleInterop": true, // CommonJS 방식으로 보낸 모듈을 ES모듈 방식의 import로 가져올 수 있게
    "module": "esnext", // 모듈 시스템 : esnext -> import를 사용함
    "moduleResolution": "node", // 모듈을 해석하는 방식 / node_moudules 기준으로 모듈을 해석 / node이 CommonJS일 때만 사용할 수 있다 했는데.. 왜 가능한거임?
    "resolveJsonModule": true, // JSON파일을 Import할 수 있게
    "isolatedModules": true, // import나 export가 없으면 단순 스크립트 파일로 인식하도록
    "jsx": "preserve", // jsx를 변환할 때 변환하지 않고 그대로 유지 / swc를 쓰고있기 때문.
    "incremental": true, // 타입스크립트 마지막 컴파일 정보를 저장
    "baseUrl": ".", // 모듈을 찾을 때 기준이 되는 디렉터리
    "paths": { // 상대경로의 별칭
      "~/*": [
        "./*" // ex. "./components/Box" -> "~/components/Box"
      ]
    },
  },
  "include": [ // 타입스크립트 컴파일 대상에서 포함시킬 파일 목록
    "next-env.d.ts",
    "**/*.ts",
    "**/*.tsx",
    ".next/types/**/*.ts",
    "./jest-setup.ts"
  ],
  "exclude": [ // 타입스크립트에서 컴파일 대상에서 제외시킬 파일목록
    "node_modules",
    ".next",
    "out",
    "cypress.config.ts",
    "cypress/**/*.ts"
  ]
}

```

## 9.2 깃허브 100% 활용하기
### 깃허브 액션의 기본 개념
- runner: 깃허브 액션이 실행되는 서버
- action: 러너에서 실행되는 작업의 단위 : yaml파일로 작성된 내용
- event: 액션을 실행시키는 이벤트
- jobs: 러너에서 실행되는 스텝의 모음
- steps: 잡 내부에서 일어나는 하나하나 작업의 의미


### 9.2.3 깃허브 Dependabot 으로 보안 취약점 해결하기
### 의존성
- dependcies: package.json에서 npm install을 실행하면 설치되는 의존성. 꼭 필요한 의존성.
- devDependencies: package.json에서 npm install을 실행하면 설치되는 의존성. 개발단계에서 필요한 패키지들을 여기에 선언

- react, react-dom, next 는 애플리케이션 실행에 반드시 필요하므로 dependencies
- eslint, jest, typescript등은 실행에는 필요하지 않으나 개발단계에만 필요한 패키지이므로 devDependencies

## 9.3 리액트 애플리케이션 배포하기
### Netlify
### Vercel