# TypeScript Config for Node.js 2021

초판: 2021-10-23

## 들어가는 글

TypeScript 로 Node.js 에서 돌릴 코드를 만드는 방법에 대한 설명입니다.
브라우저용은 따로 적겠습니다.

## TypeScript Install

TypeScript 는 프로젝트 로컬에 설치하는 것을 추천합니다.

    $ npm i -D typescript

아래와 같은 의미입니다.

    $ npm install --save-dev typescript

## @types/node

TypeScript 를 사용하려면 많은 타입 정보들이 필요합니다.
TypeScript 컴파일러 패키지에는 JS 기본 라이브러리에 대한 타입 정보만 있습니다.
Node.js 에서 쓰려면 Node.js 기본 라이브러리에 대한 타입 패키지를 추가로 설치해야 합니다.

    $ npm i -D @types/node

이후 패키지를 설치할 때마다 타입 패키지를 함께 설치해야 합니다.
예로 express 를 쓴다면 두 패키지를 같이 설치해야 합니다.

    $ npm i express
    $ npm i -D @types/express

많은 타입 패키지들이 `@types/...` 식으로 만들어져 있습니다.

npm 패키지 이름에서 `@...` 은 네임스페이스같은 존재입니다.
패키지 이름의 쫑을 피하기 위해 도입되었습니다.
기관마다 하나씩 가지고 있습니다.

참고로 `npm` 패키지 인스톨시 `--save` 옵션은 이제 넣지 않아도 됩니다.
기본이 되었습니다.

## CommonJS vs ESM

현재 Node.js 에는 두 종류의 코드 포멧이 존재합니다.
require 를 쓰는 옛날 방식 CommonJS 와
import 를 쓰는 새로운 방식 ECMAScript Module (이후 ESM) 입니다.

순수 JavaScript 코딩을 한다면 ESM 방식을 추천합니다.
내 코드가 ESM 이라도 CommonJS 패키지들을 임포트할 수 있습니다.
Node 가 적당히 처리합니다.

TypeScript 코드도 ESM 형식으로 만들면 됩니다.
한데 TypeScript 컴파일러는 CommonJS 와 EMS 코드를 다 출력할 수 있어서
사용자가 사용할 포멧을 하나 선택해야 합니다.

2021년 기준, Node.js TypeScript 프로젝트라면 거의 무조건 CommonJS 방식으로 출력해야 합니다.
ESM 방식으로 출력하면 수많은 문제를 만나게 됩니다.

요약하면 TypeScript 코딩은 ESM 방식으로 하고, 
출력은 CommonJS 로 하며, Node 가 CommonJS 코드를 실행하는 식이 됩니다.

구체적인 설정 방법은 아래에서 천천히 설명하겠습니다.

## package.json

Node.js 는 `*.js` 파일을 기본적으로 CommonJS 파일로 인식합니다.
TypeScript 가 CommonJS 를 출력하도록 할 예정이기 때문에 Node.js 추가 설정은 필요없습니다.

이를 명시적으로 적고 싶다면 `package.json` 파일에 아래 내용을 넣으면 됩니다.

    {
        "type": "commonjs",
    }

참고로 `*.js` 를 ESM 으로 인식시키려면 아래와 같이 설정하면 됩니다.

    {
        "type": "module",
    }

## tsconfig.json

TypeScript 설정은 `tsconfig.json` 에 적습니다.
옵션이 생각보다 많고 복잡합니다.

<https://www.typescriptlang.org/tsconfig>

환경에 따라 써야할 컴파일 옵션들의 차이가 심합니다.
아래 리포에서 권장 옵션셋을 구경하실 수 있습니다.

<https://github.com/tsconfig/bases/>

## tsconfig.json 위치

`tsconfig.json` 파일은 보통 프로젝트 루트에 두게 됩니다.

    ./tsconfig.json

따로 컴파일해야 하는 모듈들이 한 프로젝트에 들어있다면 각 모듈의 루트마다 `tsconfig.json` 을 두어야 합니다.

    ./src/client/tsconfig.json
    ./src/server/tsconfig.json

## tsconfig.json 예

제가 현재 사용하고 있는 Node.js 용 `tsconfig.json` 은 아래와 비슷합니다.

    { 
      "compilerOptions": {
        "outDir": "build", // "dist" 등 취향에 따라
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

아래에서 하나하나 설명하도록 하겠습니다.

## tsconfig.json 기본 구조

`tsconfig.json` 파일의 기본 구조는 아래와 같습니다.

    {
      "compilerOptions": { ... },
      "include": [ ... ],
      "exclude": [ ... ],
      "references": [ ... ],
    }

### include 

`include` 는 컴파일러가 읽어들일 파일들을 와일드카드로 표시합니다.
`tsconfig.json` 파일이 있는 디렉토리가 기준입니다.

    {
      "include": ["src/**/*", "tests/**/*"]
    }

와일드카드를 쓴다고 해서 모두 읽어들이는 것은 아닙니다.
`*.ts`, `*.tsx` 등 TypeScript 가 인식하는 파일만 읽습니다.

### references

TypeScript 코드는 기본적으로 `tsconfig.json` 디렉토리 밖의 파일을 임포트할 수가 없습니다.
이걸 하려면 레퍼런스를 써야합니다.
레퍼런스는 이 글의 범위를 벗어나므로 추후 다른 글에 적겠습니다.

아래 이어지는 내용은 모두 `compilerOptions` 에 관계된 내용입니다.

## outDir

일단 출력에 사용할 디렉토리를 설정해야 합니다.

    "compilerOptions": {
      "outDir": "build",  // 취향에 따라 "dist" 등 선택.
      "rootDir": "src",
    }

위 두 옵션은 쌍으로 쓰입니다.
이름만으로 `rootDir` 의 기능을 유추하기가 힘든데 이게 필요한 이유는 아래와 같습니다.

아래과 같은 소스코드가 있다면,

    src/abc.ts

`rootDir` 지정 없이 컴파일 하면 아래와 같은 파일이 생깁니다.

    build/src/abc.js

느낌이 오시겠지만 `rootDir` 은 출력 파일의 불필요한 패스를 없애기 위해 사용합니다.
`rootDir` 에 `src` 를 주고 컴파일 하면 아래와 같은 파일이 생깁니다.

    build/abc.js

`rootDir`은 오직 위 용도로만 사용됩니다.

## target

TypeScript 코드를 컴파일해서 어떤 포멧의 JavaScript 를 만들지 결정해야 합니다.
Node.js 환경이라면 `esnext`로 하면 무난합니다.
특별한 이유가 있다면 `es6`, `es2016`, `es2017` 등으로 변경할 수 있습니다.

모듈 방식은 거의 `commonjs` 를 써야합니다.
2021년 기준, `es6` 등 ESM 으로 하면 호환 문제가 끊없이 발생합니다.

    "compilerOptions": {
      "target": "esnext",
      "module": "commonjs",
    }

## lib

위에서 `@types/node`, `@type/express` 등 타입 패키지를 추가로 설치하는 모습을 보셨습니다.
이와는 별도로 TypeScript 컴파일러 패키지에도 기본적인 타입 정보가 있습니다.
이 내장된 타입셋중 어떤 것을 활성화 할지를 `lib` 옵션이 결정합니다.

Node.js 환경이라면 다음과 같이 하면 무난합니다.
JS 기본 라이브러리만 활성화합니다.

    "compilerOptions": {
        "lib": ["esnext"],
    }

참고로 브라우저에서 실행할 프로젝트라면 아래처럼 `dom` 타입도 활성화해야 합니다.

    "compilerOptions": {
        "lib": ["dom", "esnext"],
    }

## sourceMap 등

아래는 거의 고정으로 켜야하는 옵션들입니다.

    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "sourceMap": true

이 기능들이 켜져 있어야 컴파일러가 상식적으로 작동합니다.
과거엔 없었던 기능이기 때문에 구 코드와 호환을 위해 모두 꺼져있습니다.

옵션을 하나하나 설명하지면 길어질 것 같습니다.
TypeScript 에 입문할 때는 그대로 복사 붙이기 해서 저 상태로 시작하면 무난합니다.
구체적인 내용은 레퍼런스를 참고하시면 되겠습니다.

## Compile

TypeScript 코드는 `tsc` 명령으로 컴파일합니다.
`tsc` 는 인자에 따라서 행동이 무척 달라집니다.
현재 디렉토리 `tsconfig.json` 을 사용해 컴파일한다면 아무 인자 없이 `tsc`만 실행하면 됩니다.

    $ npx tsc

`npx` 는 `npm` 류 명령인데 로컬에 설치한 패키지의 코맨드를 실행합니다.
`npx` 쓰기 싫으면 아래처럼 직접 실행해도 됩니다.

    $ node_modules/.bin/tsc

컴파일된 결과는 `node` 로 실행하면 됩니다.

    $ node build/abc.js

## ts-node

일단 `tsc`로 컴파일은 해보는데,
실제 작업이 시작되면 `tsc` 대신 `ts-node` 를 사용하는 것이 좋습니다.
관련 글은 따로 적겠습니다.

[ts-node 2021](ts-node-2021.md)

