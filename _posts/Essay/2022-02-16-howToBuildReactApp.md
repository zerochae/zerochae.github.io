---
title: Essay &#35;4
description: How To Build React App
date: 2022-02-16 09:00
category: Essay
tags: [Think]
---

# How to Build React App

`Perl` 언어의 창시자 [Larry Wall](https://ko.wikipedia.org/wiki/%EB%9E%98%EB%A6%AC_%EC%9B%94)은 개발자의 덕목으로 다음 3가지를 언급했습니다.

- 게으름 (Laziness)
- 초조함 (Impatience)
- 자만심 (Hubris)

이 중 게으름에 대해 다음과 같이 설명합니다.

> _The quality that makes you go to great effort to reduce overall energy expenditure. It makes you write labor-saving programs that other people will find useful, and document what you wrote so you don’t have to answer so many questions about it. Hence , the first great virtue of a programmer._ 전체적인 에너지 소모를 줄이기 위해 끊임없이 노력하는 성격. 이런 성격의 사람은, 다른 사람들에게도 유용하다고 생각하는, 노동력을 절감하는 프로그램을 만들고, 같은 질문에 몇번이나 대답 하지 않아도 될 수 있게 문서를 작성한다. 그렇기 때문에, 나태함은 프로그래머의 첫 번째 덕목이라고 할 수 있다.

위 글에서 제가 생각한 **keyword**는 다음과 같습니다.

- 구조화
- 재사용
- 가독성(직관성)

## 구조화

[React 공식 문서(File Structure)](https://ko.reactjs.org/docs/faq-structure.html)에서 규정한 표준 프로젝트 구조는 없지만, 일반적으로 많이 사용하고 있는 디자인 패턴이 있다고 합니다. 이 중 "기능"에 의한 분류를 살펴 보겠습니다.

```
profile/
  index.js
  Profile.js
  ProfileHeader.js
  ProfileAPI.js
board/
  index.js
  Board.js
  BoardHeader.js
  BoardBody.js
```

`profile`,`board`를 기준으로 연관된 컴포넌트들이 같은 폴더에 존재하기 때문에, 폴더 이름만 보고 직관적으로 판단하기 좋은 구조입니다. 하지만 프로젝트 규모가 커지면서 폴더들이 많아 진다면

```
// 예를들기 위해 실용적이지 않은 컴포넌트를 추가했습니다.

profile/
  index.js
  Profile.js
  ProfileHeader.js
  ProfileAPI.js
  ProfileBody.js
  ProfileFooter.js
  ProfileImg.js
board/
  index.js
  Board.js
  BoardHeader.js
  BoardBody.js
  BoardFooter.js
  BoardImg.js
```

파일의 Depth가 깊어지면서 오히려 직관성을 헤치게 되고, 파일의 수만 늘어나 관리가 더욱 힘들게 됩니다. 여기서 재사용할 수 있는 부분을 나눌 수 있습니다.

```
common/
  avatar/
    Avatar.js
    ...
profile/
  index.js
  ...
board/
  index.js
  ...
```

## 재사용

`board`와 `profile`에 공통적으로 들어가는 `~Img`컴포넌트를 `Avatar`라는 공통된 컴포넌트로 리펙토링 하였습니다. 이제 유저이미지가 필요한 컴포넌트에서는 `Avatar`컴포넌트를 재사용할 수 있습니다.

이렇듯 첫 째로 고려해야 할 부분은 재사용성입니다. 재사용되는 컴포넌트를 얼마나 만드냐에 따라 업무의 효율성이 결정 될 수 있습니다. 재사용성과 범용성을 가지기 위해서는 [관심사 분리](https://ko.wikipedia.org/wiki/%EA%B4%80%EC%8B%AC%EC%82%AC_%EB%B6%84%EB%A6%AC#:~:text=%EC%BB%B4%ED%93%A8%ED%84%B0%20%EA%B3%BC%ED%95%99%EC%97%90%EC%84%9C%20%EA%B4%80%EC%8B%AC%EC%82%AC%20%EB%B6%84%EB%A6%AC,%EC%9D%98%20%EA%B4%80%EC%8B%AC%EC%82%AC%EB%A5%BC%20%ED%95%B4%EA%B2%B0%ED%95%9C%EB%8B%A4.&text=%EA%B4%80%EC%8B%AC%EC%82%AC%20%EB%B6%84%EB%A6%AC%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EB%A9%B4,%EC%A0%95%EB%8F%84%EC%9D%98%20%EC%9E%90%EC%9C%A0%EA%B0%80%20%EC%83%9D%EA%B8%B4%EB%8B%A4.)에 따라 한 가지의 역할만 수행할 수 있도록 해야 합니다. 이는 `Pure Function`의 개념과 일치합니다. 컴포넌트를 `Pure Function`하게 만들수록 재사용성이 극대화되고, 비즈니스 로직이 분리되어 있기 때문에 다른 사람이 코드를 볼 경우 이해하기도 수월합니다. 

페이지를 나눌 수 없을때 까지 쪼개어 분리를 극대화한 아토믹 디자인 패턴이 있습니다. 재사용성에 있어서는 분명 좋은 디자인이지만, 고려해야 할 부분이 있습니다. 

1. 모든 컴포넌트들이 재사용될까?
아토믹 디자인은 최소한의 컴포넌트(`Atom`)로 분리하여 이를 점차 조립하는 방식입니다. 디자인 패턴 상 일단 `Atom`단위로 분리해야 할텐데 재사용되지 컴포넌트가 존재한다면, 이는 의미가 없는(비효율적인) 작업입니다.

2. 사용중이던 컴포넌트의 일부를 수정해야 하는 상황에서는?
헤더 컴포넌트의 일부를 `Anchor`로 동작하게 해달라는 요청을 받아서, 해당 기능을 하는 `Atom`을 생성하였습니다. 이제 `Atom`에는 `Anchor`기능을 하는 헤더와 아닌 헤더 두개가 존재합니다. 이런 요청이 계속 들어온다면 컴포넌트의 파편화가 계속되고, 난잡해지는 결과가 생깁니다. 결국 수 많은 Atom 컴포넌트들이 각각 어떤 기능을 하는지 명확히 구분하기 어려워집니다.

## 가독성(직관성)

가독성 있는 코드는 곧 유지 보수하기 좋은 코드로 연결됩니다. 저는 가독성의 첫번째 원칙을 `Naming`으로 뽑습니다. `fileName` 혹은 `componentName`만 보고 어떤 역할을 할 지, 예측할 수 있어야 합니다. 혼자만 알 수 있는 네이밍을 한다면, 동료는 이해하기 어려울 수 있기 때문에, 이는 곧 협업을 위함입니다. 

### 결론 

1. 재사용할 수 있는 컴포넌트를 첫째로 고려해야 합니다.
2. 관심사의 분리를 통해 컴포넌트의 역할을 이해한다면, 재사용할 수 있는 컴포넌트를 만들 수 있는 설계를 할 수 있습니다.