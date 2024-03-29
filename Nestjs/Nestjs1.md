# Nestjs 설치

## Nodejs 설치
NestJS는 Node.js를 기반으로 하므로 우선 [Node.js 공식 페이지](https://nodejs.org/ko/download/)에서 적당한 버전을 설치합니다. 

## yarn global 설정
`@nestjs/cli`는 전역 설치해주어야하므로 `yarn global` 설정을 해줍니다.  

1. 저장위치 설정하기
```bash
yarn config set prefix ~/.yarn-global
yarn cofig get prefix
```

`yarn config get prefix` 명령어를 통해 변경된 저장 위치를 확인할 수 있습니다.

2. PATH 추가하기  
OS에 맞게 환경변수를 추가합니다. 저는 mac os를 사용하고 zsh를 사용하므로 .zshrc에 다음 줄을 추가했습니다.  
```bash
export PATH="$(yarn global bin):$PATH"
```

## nest 프로젝트 생성

NestJS 서버를 구성하기 위해 먼저 @nestjs/cli를 설치합니다. 
```bash
yarn global add @nestjs/cli
```

>위의 글로벌 설정을 안하면 명령어 오류가 납니다

설치가 끝나면 프로젝트를 생성합니다.
```bash
nest new nest-project
```
저는 nest-project라는 이름으로 프로젝트를 생성했습니다.

> [https://github.com/nestjs/typescript-starter](https://github.com/nestjs/typescript-starter)에서 시작해도 좋습니다. nestjs/typescript-starter가 더 최신 버전의 라이브러리들로 구성해줍니다.

## nest 서버 실행시키기
`yarn run start:dev`명령어로 서버를 실행시킵니다. start:dev로 실행시킬 경우 --watch 옵션으로 소스코드 변경을 감지하여 코드를 저장할 때 마다 서버를 다시 구동시켜줍니다. (nodemon과 유사합니다)

```bash
> yarn run start:dev
[오후 5:31:57] Starting compilation in watch mode...

[오후 5:31:58] Found 0 errors. Watching for file changes.

[Nest] 17675  - 2022. 02. 19. 오후 5:31:58     LOG [NestFactory] Starting Nest application...
[Nest] 17675  - 2022. 02. 19. 오후 5:31:58     LOG [InstanceLoader] AppModule dependencies initialized +17ms
[Nest] 17675  - 2022. 02. 19. 오후 5:31:58     LOG [RoutesResolver] AppController {/}: +3ms
[Nest] 17675  - 2022. 02. 19. 오후 5:31:58     LOG [RouterExplorer] Mapped {/, GET} route +1ms
[Nest] 17675  - 2022. 02. 19. 오후 5:31:58     LOG [NestApplication] Nest application successfully started +1ms
```