# TypeScript Config for Browser 2021

초판: 2021-10-23

## 들어가는 글

TypeScript 로 브라우저에서 돌릴 코드를 만드는 방법에 대한 설명입니다.

## 브라우저용 설정

TypeScript 설정에 관한 중요한 내용들은 Node.js 글에 적어놨습니다.\

[TypeScript Config for Node.js 2021](typescript-config-node-2021.md)

브라우저용 `tsconfig.json` 에서 달라지는 부분은 `target` 과 `lib` 정도입니다.

    {
      "compilerOptions": {
        "target": "es2017", // async, await
        "lib": ["dom", "esnext"]
      }  
    }

`target`은 지원할 브라우저에 범위에 따라 `es5`, `es6`, `es2016` 등으로 변경하면 되겠습니다. 

`lib`엔 `dom`을 추가합니다. 브라우저에서 기본 제공하는 DOM 라이브러리 타입이 문법 체크에 사용됩니다.

## 번들러

브라우저에서 돌릴 JS 코드를 만드는 작업은 Node.js 보다 약간 복잡합니다.
개발할 때는 코드를 여러 파일에 나누어 코딩합니다.
이 파일들을 브라우저로 보내려면 하나로 뭉쳐야 합니다.
이 작업을 번들러로 합니다.

브라우저용 패키지를 만드는 절차는 직접 TypeScript 코드를 컴파일하고 번들러로 링크하는 식이 아니라,
애초에 번들러에게 일을 시키면 번들러가 TypeScript 컴파일러를 호출하는 식입니다.
해서 브라우저용 코드를 개발하는 과정은 TypeScript 설정보다 번들러 선택과 설정이 더 큰 부분을 차지합니다.

번들러는 종류가 많은데 2021 현재 전 Rollup 을 사용하고 있습니다. TypeScript 번들링만 하기에는 간단하고 괜찮은 것 같습니다. 
아래 링크에 Rollup 에 대한 설명을 적어놨습니다.

[Rollup 2021](rollup-2021.md)
