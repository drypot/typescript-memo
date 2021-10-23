# TypeScript Config for Node 2021

초판: 2021-10-23

## 들어가는 글

TypeScript 설정이 간단한 일이 아닙니다.\
해서 입문자들을 위해 간단히 메모를 남겨놓습니다.

Node.js 환경에서 TypeScript 를 사용한다는 전제하에 적겠습니다.\
브라우저용 TypeScript 세팅은 추후 따로 적겠습니다.

## TypeScript Install

TypeScript 는 프로젝트마다 로컬에 설치하시는 것을 추천합니다.

    $ npm i -D typescript

길게 다 쓰면,

    $ npm install --save-dev typescript

## @types/node Install

TypeScript 패키지와 별도로 Node.js 기본 패키지에 관한 타입 정보를 추가로 설치해둬야 합니다.
Node.js 환경에선 거의 필수 절차입니다.

    $ npm i -D @types/node

이후 설치해 사용하실 Node.js 패키지 마다 타입 패키지를 함께 설치해야 합니다.
예로 express 를 쓴다면 두 패키지를 같이 설치해야 에디터 도우미들이 작동합니다.

    $ npm i express
    $ npm i -D @types/express

참고로 이제 npm 패키지 인스톨시 `--save` 옵션은 넣지 않으셔도 됩니다.
기본이 되었습니다.

npm 패키지 이름에서 `@...` 은 네임스페이스같은 존재입니다.
패키지 이름의 쫑을 피하기 위해 도입되었습니다.
기관마다 하나씩 가지고 있습니다.

## CommonJS vs ESM

현재 Node.js 에는 두 종류의 소스 포멧이 존재합니다.
옛날 방식 CommonJS 와 새로운 방식 ECMAScript Module 입니다.

순수 JavaScript 코딩을 한다면 ESM 방식을 추천합니다.
내 코드가 ESM 이라도 CommonJS 패키지들을 임포트할 수 있습니다.
Node 가 적당히 처리합니다.

TypeScript 로 코딩을 한다면 코드는 ESM 방식으로 만들게 됩니다.
한데 TypeScript 는 컴파일 결과를 두 가지 포멧으로 만들 수 있어서 출력 포멧을 선택해야 합니다.
2021년 기준, Node.js 프로젝트라면 무조건 CommonJS 방식으로 출력해야 한다고 보시면 됩니다.
ESM 방식으로 출력하면 수많은 문제를 만나게 됩니다.

요약하면 TypeScript 코딩은 ESM 방식으로 하고, 
출력은 CommonJS 코드로 하며, Node 가 CommonJS 코드를 실행하도록 해야 합니다.

Node.js 는 `*.js` 파일을 기본적으로 CommonJS 파일로 인식하기 때문에 설정은 따로 필요없습니다.
이를 명시적으로 설정한다면 `package.json` 에 아래 내용을 넣으면 됩니다.

    {
        "type": "commonjs",
    }

권장하지 않지만 `*.js` 를 ESM 으로 인식시키려면 아래와 같이 설정하면 됩니다.

    {
        "type": "module",
    }

Node.js 관련 내용은 끝났습니다.
TypeScript 설정으로 넘어가겠습니다.

## tsconfig.json

TypeScript 컴파일 설정은 `tsconfig.json` 에 적습니다.
옵션이 생각보다 엄청 많고 복잡합니다.

<https://www.typescriptlang.org/tsconfig>

`tsconfig.json` 파일은 보통 프로젝트 루트에 두게 됩니다.

    ./tsconfig.json

혹시 따로 컴파일 되어야 하는 여러 모듈이 한 프로젝트에 들어있다면 각 모듈의 루트마다 `tsconfig.json` 을 두어야 합니다.

    ./client/tsconfig.json
    ./server/tsconfig.json

환경에 따라 써야할 옵션들의 차이가 심합니다.
아래에 계속 설명하긴 하겠지만 아래 리포에서 권장 옵션셋을 구경하실 수 있습니다.

<https://github.com/tsconfig/bases/>

## tsconfig.json 기본 구조

`tsconfig.json` 파일의 기본 구조는 아래와 같습니다.

    {
        "compilerOptions": { ... },
        "include": [ ... ],
        "exclude": [ ... ],
        "references": [ ... ],
    }

`include` 는 컴파일러가 읽어들일 파일들을 표시합니다.
와일드카드를 쓴다고 해서 모두 읽어들이는 것은 아니고
`*.ts`, `*.tsx` 등 TypeScript 가 인식하는 파일만 읽습니다.
현재 `tsconfig.json` 파일이 있는 디렉토리가 기준입니다.

    {
        "include": ["src/**/*", "tests/**/*"]
    }

`references` 는 다른 디렉토리 모듈을 임포트해야 할 때 설정합니다.
이게 왜 필요하냐면 TypeScript 코드는 `tsconfig.json` 이 위치한 디렉토리 밖에 있는 파일에 접근하지 못하기 때문입니다.
레퍼런스는 이 글의 범위를 벗어나므로 더 적진 않겠습니다.

아래 이어지는 내용은 모두 `compilerOptions` 에 관계된 내용입니다.

## outDir

일단 출력 결과에 사용할 디렉토리를 설정해야 합니다.

    "compilerOptions": {
        "outDir": "build",
        "rootDir": "src",
    }

위 두 옵션은 항상 쌍으로 써야합니다.
이름만으로 `rootDir` 의 기능을 유추하기는 힘든데 이게 필요한 이유는 아래와 같습니다.

아래과 같은 소스코드가 있다면,

    src/abc.ts

`rootDir` 지정 없이 컴파일 하면 아래와 같은 파일이 생깁니다.

    build/src/abc.js

느낌이 오시겠지만 `rootDir` 은 출력 코드 만들 때 불필요한 패스를 없애기 위해 사용합니다.
`rootDir` 에 `src` 를 주고 컴파일 하면 아래와 같은 파일이 생깁니다.

    build/abc.js

## target

TypeScript 코드를 컴파일해서 어떤 포멧의 JavaScript 를 만들지 결정해야 합니다.
Node.js 환경이라면 아래처럼 하면 무난합니다.
특별한 이유가 있다면 `target` 포멧을 `es6`, `es2016`, `es2017` 식으로 변경할 수 있습니다.
출력 모듈 방식은 거의 `commonjs` 를 써야합니다.
2021년 기준, `es6` 등으로 출력하면 호환 문제가 끊없이 발생합니다.

    "compilerOptions": {
        "target": "esnext",
        "module": "commonjs",
    }

## lib

각 JavaScript 플렛폼에서는 여러 영역에서 라이브러리가 제공됩니다.
JavaScript 기본 라이브러리가 있고,
브라우저에서 제공하는 라이브러리, Node.js 에서 제공하는 라이브러리,
추가 설치한 패키지에서 제공하는 라이브러리가 있습니다.

TypeScript 컴파일 시에는 위 라이브러리들에 대한 타입 정보를 제공해야 합니다.

Node.js 나 NPM 패키지 라이브러리에 대한 타입은 `npm install @type/...` 으로 제공합니다.

JavaScript 기본 라이브러리나 브라우저 기본 라이브러리에 대한 타입은 TypeScript 가 내장하고 있습니다.
이 내장된 타입중 어떤 것을 활성화할지를 `lib` 옵션이 결정합니다.

Node.js 환경이라면 다음과 같이 설정하면 무난합니다.

    "compilerOptions": {
        "lib": ["esnext"],
    }

참고로 브라우저에서 실행할 코드를 컴파일 한다면 아래처럼 'dom' 타입도 활성화합니다.

    "compilerOptions": {
        "lib": ["dom", "esnext"],
    }

## sourceMap 등

기타 거의 고정으로 켜야하는 옵션들입니다.

    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "sourceMap": true

왜 이것들이 기본으로 켜져있지 않는가 하면,
옵션들의 기본값은 권장 값이 아니라 과거의 기본값이기 때문입니다.

예로 source map 출력 기능이 없었을 때는 `sourceMap` 이 `false` 상태와 같았을 겁니다.
이젠 이 옵션을 거의 켜고 살아야 하지만 과거 행동을 유지하기 위해 기본 `false`로 되어 있습니다.
해서 이런 것들을 모두 명시적으로 켜줘야 합니다.

하나하나 설명하지면 너무 길어질 것 같습니다.
구체적인 내용이 궁금하시면 레퍼런스를 참고하시면 되겠습니다.

요컨데 상식적인 작동을 위해 위 옵션들은 거의 항상 켜져있어야 하지만
구버전과 호환성을 위해 기본으론 꺼져 있으니
명시적으로 켜줘야 한다라고 보시면 되겠습니다.

## Compile

`tsc` 는 인자에 따라서 행동이 무척 달라집니다.
현재 디렉토리에 있는 `tsconfig.json`에 따라서 컴파일 한다면 아무 인자 없이 `tsc`만 실행하면 됩니다.

    $ npx tsc

`npx` 는 npm 류의 명령인데 로컬에 설치한 npm 패키지의 코맨드라인 명령을 실행합니다.
`npx` 덧붙이기 싫으시면 아래처럼 직접 실행하셔도 됩니다.

    $ node_modules/.bin/tsc

## 설정 요약

전체적으로 보면 `tsconfig.json` 은 아래와 비슷한 모양이 되실 겁니다.

    { 
        "compilerOptions": {
            "outDir": "build", // 또는 "dist' 취향에 따라
            "rootDir": "src",
        
            "target": "esnext",
            "module": "commonjs",
            "lib": ["esnext"],
        
            "strict": true,
            "esModuleInterop": true,
            "skipLibCheck": true,
            "forceConsistentCasingInFileNames": true,
            "sourceMap": true
        },
        "include": [
            "src/**/*"
        ]
    }
