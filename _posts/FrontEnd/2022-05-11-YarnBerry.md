---
title: FrontEnd &#35;2
date: 2022-05-10 09:00
description: Yarn Berry
category: FrontEnd
tags: [front-end, Web, yarn, yarn-berry]
---

## yarn

### `npm`의 문제점

- 속도 (performance)
- 안정성 (stability)
- 보안성(security)

이를 보안하기 위해 `yarn` 이 등장하였다

1. 속도

yarn은 다운받은 패키지 데이터를 캐시(cache)에 저장하여, 중복된 데이터는 다운로드 하지 않고, 캐시에 저장된 파일을 활용함으로써 이론적으로 npm에 비해 패키지 설치속도가 매우 빠르다. 또한 여러개의 패키지를 설치할 때 병렬로 처리하기 때문에 performance와 speed가 증가 된다.

2. 안정성 / 보안성

npm은 패키지가 설치될 때 자동으로 코드와 의존성을 실행할 수 있도록 허용했다. 이 특징은 편리한 기능이지만 안정성을 위협할 수 있다. 특히 보장된 정책 없이 등록한 패키지가 존재 할 수 있다는 점에서 더욱 위험도가 높다.

- yarn.lock 파일 자동 생성, checksum을 통해 어느 환경에서든 동일한 패키지 설치
- 의존성 트리 이슈는 lock파일과 설치 알고리즘을 통해 개선
- 패키지 병렬 설치를 통한 성능 개선
- cache directory를 통한 오프라인 미러링과 [left-pad](https://www.bloter.net/newsView/blt201604040002) 이슈 방지

### `npm`과 `yarn`의 공통 문제점

1. node_modules의 거대함과 깊은 depth

![node_hole](/assets/images/post/img-2022-05-11-01.png)

간단한 패키지를 몇 개만 설치 해도 node_modules의 depth와 용량은 엄청나케 비대해진다.
특히 next.js 등 프레임워크나 거대한 라이브러리를 사용한다면 최소 몇백메가에서 기가 단위의 디스크 용량을 차지하게 된다. 게다가 점점 depth가 짚어짐에 따라 설치,삭제,수정,실행 등의 속도에 영향을 끼치게 되고 패키지의 내용은 확인하지 못하고 기본적인 의존성 트리만 검증하게 된다.

2. Phantom Dependency

![phantom](/assets/images/post/img-2022-05-11-02.png)

왼쪽의 `package-1`에서 <span color="#73ff05"> B(1.0)패키지</span> 를 가져다 쓸 수 없는 구조이지만 오른쪽에서는 Hoisted가 되면서 `package-1`이 <span color="#73ff05"> B(1.0)패키지</span>를 설치한 적이 없지만 사용할 수 있다. 이런 현상을 Phantom Dependency(유령 의존성) 이라고 한다.
<br/>
위와 같은 이슈가 발생시에는 package.json을 통해 설치 되지 않은 패키지를 사용할 수도 있고, 삭제할 때도 잘 사용하던 패키지를 갑자기 사용할 수 없게 되기도 한다. 
<br/>

<hr/>

## Yarn Berry

`yarn berry`는 `pnp`라는 개념을 도입하여 아래와 같은 문제점을 해결했다.

> Node.js의 기본적인 모듈 처리 매커니즘으로 인한 속도 저하
- Node.js는 어떤 모듈을 사용하려고 하면, 그 모듈을 찾기 위해 계속 거슬러 올라가서 node_modules 폴더를 찾는 방식을 사용한다. 해당 과정에서 시스템 함수를 호출함에 따라서 시간이 걸리며, 이는 런타임에 시작 속도 저하를 유발한다.

> Yarn의 설치 메커니즘으로 인한 속도 저하
- Yarn은 패키지를 설치할 때, 원격 저장소에서 패키지를 찾아 오프라인 미러에 저장하고 패키지에 캐시를 푼다. 그 다음 캐시에 있는 패키지를 node_modules로 복사하는 방식을 사용한다. 이 과정에서 무거운 I/O 연산으로 인한 속도 저하가 발생한다.

> node_modules 자체가 실용적으로 설계되지 않았다.
- node_modules에 설치되는 패키지들의 중복제거를 하지 못한다. 최적화를 통해 호이스팅을 하기도 하지만 유령 의존성의 문제를 유발했다. 개발할 때는 동작을 했지만 프로덕션에서 의존성에 명시되어 있지 않기 때문에 문제가 발생할 수 도 있다.

`yarn berry`는 `.pnp.js` 파일을 생성해서 위와 같은 문제를 해결한다. 이 파일에는

- 의존성 트리에서 사용할 수 있는 패키지
- 패키지들의 연결 관계
- 디스크의 저장된 위치
<br/>
의 정보가 담겨있다.

또한 node_modules를 생성하지 않고 기본 캐시 폴더인 `.yarn/cache` 에 패키지를 zip 파일로 저장해서 이 경로를 `.pnp.js` 에 명시하는 방식을 사용한다. 이 방법으로 기존 방식보다 훨씬 빠르게 어플리케이션의 패키지들을 설치 및 실행할 수 있다. 또한, 모든 패키지들이 명시되어 있기 때문에 각 패키지들도 최상단으로 끌어올려저서 완전한 플랫 한 구조를 갖는다.

- zero-install(node_modules를 사용하지 않음)방식을 사용하기 때문에, repository에 `.pnp.js`를 커밋한다. (yarn install)을 할 필요가 없다.

### Usage

```shell
yarn set version berry
yarn --version
3.X.X # 최신버전이 표기됨
```

- `package-lock.json` 파일이 존재한다면 삭제
- `node_modules`  삭제

Pnp 방식이 아닌 기존의 `node_modules` 방식을 사용하려면 다음을 추가한다.

```shell
# .yarnrc.yml
nodeLinker: node-modules
```

위를 완료한뒤 아래의 명령어로 설치한다.

```shell
yarn install or yarn
```

### Use With Typescript 

yarn berry의 PnP 기능을 사용할 때, Typescript가 작동하도록 추가적인 구성이 필요하다. 안전상의 이유로 VSCode에서는 사용자 지정 Typescript 설정을 명시적으로 활성화해야 한다.
<br/>
패키지들이 zip 아카이브로 관리되기 때문에 기존의 방식으로는 정상적으로 타입이 불러와지지 않는다.
<br/>
Editor SDK 설정을 하기 전에 먼저 VSCode에서  `zipfs`를 먼저 설치해줘야 한다.

이 extension은 zip 아카이브에서 직접 파일을 읽을 수 있도록 VSCode 에 지원을 추가한다. 예를 들어 이 extension을 추가하지 않으면 import된 React와 같은 설치한 의존성 파일을 열어볼 수 없다.

Editor SDK 설정은 Terminal에 다음과 같이 입력 사용할 수 있다.

```shell
yarn dlx @yarnpkg/sdks vscode
```
<br/>

그 후 `.vscode`폴더와 `.yarn/sdks` 에 추가된 SDK를 확인할 수 있다. 이 때 `Zero-installs`를 사용하기 때문에 `.vscode`는 `.gitignore`에 추가하면 안 된다.

<br/>

추가적으로 `typescript plugin`을 import 시켜준다. 이 플로그인은 자체 types를 포함하지 않는 패키지를 추가할 때 `@types/` 패키지를 `package.json` 폴더에 종속성으로 자동으로 추가해준다. 이 플러그인 설치는 선택사항이지만 매우 유용하다.

```shell
yarn plugin import typescript
```

설치 후에는 `.yarn/plugins/@yarnpkg` 폴더 아래에 `plugin-typescript.cjs` 파일이 생성된다. 

<br/>

마지막으로 `command + shift + p`를 눌러 `typescript 버전 선택..` 을 검색해 `Use WorkSpace Version`을 선택해 Workspace의 Typescript sdk로 변경해준다. 