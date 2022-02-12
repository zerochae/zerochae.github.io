---
title: Issue &#35;1
description: Peer Dependency
date: 2022-02-11 09:00
category: Issue
tags: [Issue]
---

## unable to resolve dependency tree

![Error](https://user-images.githubusercontent.com/84373490/153573779-801d3bb0-4fe0-4e9c-b04b-86b86ed67254.jpg)

프로젝트 초기설정을 위해 `craco`를 설치하던 중 에러를 만났다.

에러 메시지를 읽어보니 `craco` 설치를 위해서는 `react-scripts ^4.0.0`이 필요한데, 현재 프로젝트는 `react-scripts 5.0.0` 라는 버전 에러이다. 그리고 `--force`나 `--legacy-peer-deps` 키워드를 사용하면 해결할 수 있지만 잠재적으로 깨질 수 있지만 해결할 수 있다고 한다.

[What does npm install --legacy-peer-deps do exactly?🚀](https://stackoverflow.com/questions/66239691/what-does-npm-install-legacy-peer-deps-do-exactly-when-is-it-recommended-wh)

[When to use --force and --legacy-peer-deps?🚀](https://stackoverflow.com/questions/66020820/npm-when-to-use-force-and-legacy-peer-deps)

위 문서의 요약은 다음과 같다.

우리가 라이브러리를 설치하면 `package.json`의 `dependencies` , `devDependencies` 항목에 기록이 된다. 그리고 여기에 기록되지 않는 `peerDependencies` 라는 항목이 있다. 이 항목은 해당 라이브러리의 실행을 위해서 필요한 `dependencies`의 정보이다. 이 항목은 `package-lock.json`에 기록되어 있다.

![peer](https://user-images.githubusercontent.com/84373490/153626757-acc2be23-6b51-4bc5-b2fa-ee9b71709303.jpg)

`react-scripts`의 버전 정보가 `^4.0.0` 으로 표기 되어있다. `caret(^)` 기호는 숫자의 개수, 0의 처음 위치에 따라 그 뜻이 달라지는데 여기서는 4.0.0 <= version < 5.0.0 사이의 버전(`4.*.*`)을 의미한다.  

`dependency`는 내가 만든 모듈에서 사용하는 패키지를 지정하는 반면, `peerDependencies`는 반대로 내가 만든 모듈이 다른 모듈과 함께 동작할 수 있다는 호환성을 표시하는 것이다. 이런 것을 플러그인이라고 한다.

말이 어렵지만, 밑을 계속 보면 쉽게 이해할 수 있다.

우리는 `craco`를 설치하려고 했을 때 `carco`의 `peerDependencies` 항목의 `react-scripts ^4.0.0`이 필요하다고 했지만 프로젝트에는 `react-scripts 5.0.0`이기 때문에 오류가 발생한 것 이다.

`npm VER.4 ~ 6` 까지는 `peerDependencies` 관련 이슈가 발생하면 오류가 아닌 경고를 출력했지만, 21월 2월 `npm VER.7`이 출시되면서(현재는 `VER.8`) 오류가 발생하는것으로 변경되었다. 

### force

여기서 `--force` 키워드를 사용하면 필요한 `peerDependencies`를 추가 후, 설치를 진행한다.

![force](https://user-images.githubusercontent.com/84373490/153630787-d53ebd82-7829-4038-babf-bee1b9c3766e.jpg)

위 사진에서는 `react-scripts ^4.0.0`을 설치하려했지만 충돌이 발생하여 정상 완료 되지 않았는데, 이상하게 `craco`는 설치가 되었고 **프로젝트를 실행**시킬 수 있었다.여기서 만약 설치에 실패한다면 
```
npm ERR! Fix the upstream dependency conflict, or retry
npm ERR! this command with --force, or --legacy-peer-deps
npm ERR! to accept an incorrect (and potentially broken) dependency resolution.
```
라는 오류 메시지를 출력한다.

자세한 결과를 보고 싶으면 `npm audit`을 입력하라는 메시지가 있길래 입력해보았다.

![audit1](https://user-images.githubusercontent.com/84373490/153698744-7354d2c4-b6eb-415a-a8ec-c715c4d41cfd.jpg)

`Will install react-scripts@3.0.1, which is a breaking change` 라는 말이 보인다.

`react-scripts@3.0.1`를 설치하는 것이 주요 변경 사항에 등록되어 있다. 음.. `3.0.1`버전은 위에서 말한 사이의 버전과 맞지 않는데 왜 갑자기 등장하였을까? 그 이유는 `craco`의 버젼도 같이 `5.9.0`로 내려갔기 때문이다.

![craco 5 9 0](https://user-images.githubusercontent.com/84373490/153703526-8588bb0d-284c-45ca-a8d9-8c1aafc1f178.jpg)

`craco@5.9.0`은 `react-scripts`의 모든 버전과 호환된다. 

음.. 처음부터 `craco@5.9.0` 을 설치했다면 모든게 해결 되었을 것 이다. 

어쨋든 등록되었으니, `npm audit fix --force` 를 입력하여 변경 사항을 실행시켜보자.(불안하다.) 

![fix1](https://user-images.githubusercontent.com/84373490/153698869-a34621ff-b125-486d-9b00-c3c9977f491b.jpg)

처음엔 똑같이 충돌했다는 메시지가 나오더니 무언가 바뀌기 시작한다.

![result1](https://user-images.githubusercontent.com/84373490/153698885-ddb3aea8-d499-4e64-b2dd-63471d582722.jpg)

음... 나는 `react-script` 하나를 바꾸고 싶었던 것 뿐인데.. 830개의 패키지가 추가되고 308개가 제거되고 369개가 바뀌었다고 한다. 

그리고 다시 `npm audit`을 입력해보면 react-script의 `5.0.0`을 주요 변경 사항에 등록해놓았는데 이는 `5.0.0`버전을 `peer Dependency`로 하는 라이브러리가 있기 때문이다.

![result3](https://user-images.githubusercontent.com/84373490/153700848-2280accf-6f1c-457c-b084-926349e0ea38.jpg)

어쨋든.. 의도한 대로(?) 버전이 바뀌어있다. 이제 실행하면 해보면

![실행안돼](https://user-images.githubusercontent.com/84373490/153699591-8c8863d2-de91-44cc-a793-34129239edb9.jpg)
.
**안된다.** 앞서 말한것처럼 다른부분에서 `react-script 5.0.0`을 필요로 하기 때문이다. 

어떻게 해야 하나 고민을 하다가 공식문서에서 해답을 찾을 수 있었다

맨 처음 `craco`를 `--force` 키워드를 사용해 설치하고, `peerDependency`의 버전이 맞지 않았는데 run이 된 이유는 `craco`에서 react-script의 버전 커스텀을 지원하기 때문이였다. 이 기능은 `craco`가 버전업되면서 추가된 기능인것 같다. 그냥 `5.0.0` 버전에서도 사용 가능 하다는 말이다. 

![cracoconfig](https://user-images.githubusercontent.com/84373490/153699678-5d4a61b7-e869-462e-aeba-85b80ad500e6.jpg)

역시.. 공식문서를 잘 읽는 습관을 들여야 한다는걸 다시 한번 느꼈다.

### legacy-peer-deps

`--legacy-peer-deps` 키워드를 사용하면 `peerDependencies` 설치 없이, 설치를 진행한다.

![legacy](https://user-images.githubusercontent.com/84373490/153620989-6b802011-9255-4e0a-85bb-33b29e6a1d32.jpg)

필요한 `peerDependency`를 설치하려는 시도를 하지 않고 해당 이슈를 무시한다. 그리고 위와 마찬가지로 실행이 되는 이유는 버전 커스텀을 지원하기 때문이다.

## 결론

처음 해당 이슈로 구글링을 하였을때 `craco`설치 시 `--legacy-peer-deps`를 입력하라는 글을 먼저 발견하였다. 그 이유는 위에서 말한 대로 버전 커스텀 덕분에 추가로 `dependency`를 설치할 필요가 없기 때문이다. 하지만 만약 다른 라이브러리 사용 시 해당 이슈를 만난다면 `--force` 키워드 사용을 추천한다. 버전을 고정시키려는 시도가 있기 때문에 `--legacy-peer-deps` 키워드보다 엄격하다고 볼 수 있기 때문이다.