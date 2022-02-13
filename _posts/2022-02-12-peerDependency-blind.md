---
title: Issue &#35;2
description: npm audit
date: 2022-02-13 09:00
category: Issue
tags: [Issue]
---

npm audit를 입력하면 보안 취약점을 검사해준다.

![audit1](https://user-images.githubusercontent.com/84373490/153698744-7354d2c4-b6eb-415a-a8ec-c715c4d41cfd.jpg)

`Will install react-scripts@3.0.1, which is a breaking change` 라는 말이 보인다.

현재의 버전(`5.0.0`)에 보안 취약점이 발견되어 npm이 안전하다고 판단하는 버전으로 다운그레이드 시키는 것이다.

`npm audit fix --force` 를 입력하여 변경 사항을 실행시켜보자.

![fix1](https://user-images.githubusercontent.com/84373490/153698869-a34621ff-b125-486d-9b00-c3c9977f491b.jpg)

처음엔 충돌했다는 메시지가 나오더니 무언가 바뀌기 시작한다.

![result1](https://user-images.githubusercontent.com/84373490/153698885-ddb3aea8-d499-4e64-b2dd-63471d582722.jpg)

음... 830개의 패키지가 추가되고 308개가 제거되고 369개가 바뀌었다고 한다. 

![result3](https://user-images.githubusercontent.com/84373490/153700848-2280accf-6f1c-457c-b084-926349e0ea38.jpg)

다운그레이드 되어있다.(`react-script` 뿐만 아니라 의존성이 연결되어 있는 모듈도 다운 그레이드 되어있다.) 

<hr />

구글링 해보니 `npm audit`기능을 사용하지 말라는 의견이 많았다. 다운 그레이드된 node module의 의존성의 의존성 중 어디에선가에 반드시 보얀 취약점이 존재하기 때문이라고 한다. 그리고 다시 `npm audit fix --force`를 입력해도 위 과정이 반복될뿐 해결 되지 않는다고 한다.

## solution

- build output에 포함되지 않는 node module은 모두 `devDependencies`로 설정한다.
- `npm audit -production` 으로 점검한다.

위 명령은 `devDependencies`에 설치된 node module은 취약점 검사를 하지 않는다.

## 결론

- `npm audit`는 `devDependencies`의 취약점까지 체크하는데 이는 불필요하다.
- `react-script` , `craco`, `storybook` 등 build tool은 반드시 `devDependencies`로 설정한다.