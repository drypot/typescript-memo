# Rollup 2021

초판: 2021-10-23

<https://rollupjs.org>

## 들어가는 글

TypeScript 코드들을 브라우저에서 실행하려면 컴파일하고 파일 하나로 묶어야 합니다.
Rollup 은 이 작업을 해주는 툴입니다.

Rollup 은 WebPack 보다 설정이 간단합니다.

## Node.js 설정

왠만하면 Node.js 설정을 CommonJS 방식으로 놔두는 것을 추천합니다.
2021년 기준, Node.js 기본 설정을 ESM 으로 바꾸시면 여러가지 문제가 발생합니다.
    
## Rollup Config

Rollup 환경 설정 파일 `rollup.config.js` 는 보통 프로젝트 루트에 둡니다.
파일 이름은 사용자 지정이 가능합니다.

모양은 대강 이렇습니다.

    //import nodeResolve from '@rollup/plugin-node-resolve';
    import typescript from '@rollup/plugin-typescript'
    import { defineConfig } from 'rollup'
    
    export default defineConfig({
      input: 'src/client/ts/_all.ts',
      output: {
        file: 'public/build/client-rollup.js',
        format: 'iife',
        name: 'app',
      },
      watch: {
        include: [
          "src/client/ts/**/*"
        ]
      },
      plugins: [
        typescript({
          tsconfig: "src/client/tsconfig.json",
          "sourceMap": true,
        }),
        //nodeResolve(),
      ]
    })

## Input File

번들링의 입구가 되는 최초의 파일 한 개를 지정해야 합니다.
이 파일만 지정하면 여기서 임포트하는 모든 파일을 Rollup 이 자동으로 묶어줍니다.
제 설정에선 아래 파일이 되겠습니다.

    input: 'src/client/ts/_all.ts',

## Output File

출력 파일을 지정합니다.
연관된 JS 파일들이 이 파일 하나로 묶여서 떨어집니다.

    output: {
      file: 'public/build/client-rollup.js',
      format: 'iife',
      name: 'app',
    },

파일 포멧은 익숙하게 보던 CommonJS 나 ESM 가 아닙니다.
이런 형식의 파일은 브라우저에서 막 쓰기에 난감합니다.
브라우저에서 실행할 코드는 `iife` 형식으로 묶으시면 됩니다.

`name` 은 `export` 한 펑션에 접근하기 위한 임의의 오브젝트 이름입니다.
예로 TypeScript 코드에서 아래와 같은 펑션을 export 했다면,

    export function helloWorld() { ... }

브라우저 페이지에서 아래와 같이 불러 쓸 수 있습니다.

    <script>
        app.helloWorld()
    </script>

## Watch

Rollup 은 소스 코드 변경을 확인하고 자동으로 출력 파일을 계속 만들 수 있습니다.
변경을 감지할 영역은 `watch` 프로퍼티로 지정합니다.

## TypeScript

TypeScript 도 지원합니다. 플러그인 설정을 해두면 Rollup 이 알아서 컴파일하고 번들링합니다.

    import typescript from '@rollup/plugin-typescript'
    ...

    plugins: [
      typescript({
        tsconfig: "src/client/tsconfig.json",
        "sourceMap": true,
      })
    ]

## NPM Modules

브라우저에서 사용할 코드인데 npm 모듈로 배포되는 경우가 많습니다.
이런 코드도 같이 묶을 수 있습니다.

일반적인 npm 모듈처럼 설치하고,

    $ npm install ...

일반 Node.js 모듈 쓰는 것 처럼 임포트 한 다음,

    import ... from '...'

Rollup 을 돌리면 내 코드와 npm 패키지 코드가 같이 묶입니다.

단 npm 코드를 길어 올리는 플러그인 설정을 해야합니다.

    import nodeResolve from '@rollup/plugin-node-resolve';
    ...

    plugins: [
      nodeResolve(),
    ]

## Rollup

위에 정도 설정하고 Rollup 하면 출력 파일이 떨어집니다.

    $ npx rollup -c

watch 모드로 돌리려면,

    $ npx rollup -c -w

## Page Link

출력 파일은 HTML 페이지에 적당히 링크합니다.

    <script src="/build/client-rollup.js?v=1.0.0"></script>
