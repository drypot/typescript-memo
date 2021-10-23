# ts-node 2021

초판: 2021-10-23

<https://github.com/TypeStrong/ts-node>

## 들어가는 글

TypeScript 코드는 한번 컴파일해야 `node` 명령으로 실행할 수 있습니다.
`ts-node` 는 TS 컴파일을 자동으로 해서 일반 JS 코드 느낌으로 실행할 수 있게 해줍니다.

## 꼭 써야하는가

처음엔 부가적인 툴을 왠만하면 쓰지 않으려고 TS를 직접 빌드하고 실행했습니다.
한데 나중에 다른 node 플러그인들을 쓰게 되면서 그중의 하나인 ts-node 만 기피할 이유가 없어졌습니다.
써보니 편하고 좋은 것 같습니다;
그냥 처음부터 사용하시길 추천합니다.

## ts-node

`ts-node` 를 설치하고,

    $ npm i -D ts-node

ts 코드를 실행하면 됩니다.

    $ npx ts-node src/hello.ts

아니면 노드 외부 모듈 스타일로 쓸 수도 있습니다.

    $ node -r ts-node/register src/hello.ts

## Run Script

사실 제 경우 아래와 같은 스크립트를 만들어 실행하고 있습니다.

    #!/usr/bin/env bash

    export NODE_OPTIONS='-r ts-node/register'
    export TS_NODE_PROJECT=src/server/tsconfig.json
    
    node "${@}"

환경변수로 `node` 옵션을 주고 있습니다.
위 내용에선 빠진 다른 환경변수들도 쓰고 있어서 환경변수 지정방법을 선호하는 중입니다.

    export NODE_OPTIONS='-r ts-node/register'

`tsconfig.json` 파일이 현재 디렉토리에 없다면 환경변수로 `ts-node`에 알려줘야 합니다.
`tsconfig.json`을 찾지 못하면 실행중 이상한 에러를 냅니다.

    export TS_NODE_PROJECT=src/server/tsconfig.json

## Config

`ts-node` 설정들은 `tsconfig.json` 설정 하단에 꼽사리로 끼우는 식입니다.

    {
        "compilerOptions": { ... },
        "include": [ ... ],
        "exclude": [ ... ],
        "references": [ ... ],

        "ts-node": {
            "transpileOnly": true
        },
    }

`transpileOnly` 옵션은 켜는 것이 좋은데 ts 파일 실행 속도가 매우 빨라집니다.
