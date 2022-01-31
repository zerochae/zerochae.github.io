---
title: Essay &#35;2
description: 원티드 프리온보딩 프론트엔드 과정 1주차 회고
date: 2022-01-30 09:00
category: Essay
tags: [Think]
---
학원에서는 백엔드 과정을 공부하였다. 학원에서 프로젝트를 진행하면서 화면을 맡아줄 팀원이 필요했는데, 모두들 자신 없어 했다. 교육받은 내용은 대부분 `Java`이고 `JavaScript`은 아주 조금 `html`,`css`는 거의 하지 않았기 때문이였다. 누군가는 해야했기 때문에, 내가 맡았다. 내가 맡게된 첫 프론트엔드 역할이였다. 백엔드를 공부할때보다 훨씬 흥미를 느꼈다. 프로젝트가 끝나고 인터넷 강의를 구매했다. 다음 프로젝트때 프론트 프레임워크를 사용하기 싶었기 때문이다. 선택지는 `Vue.js`와 `React`가 있었다. 상대적으로 러닝커브가 낮은 `Vue.js`를 공부하기 시작했고, 다음 프로젝트때 팀원들에게 알려줄 수 있을정도로 사용할 수 있었다. 

## 원티드 프리온보딩 프론트엔드 과정

학원을 수료하고 동기들은 대부분, 학원에서 연계해주는 기업(주로 공공기관 프로젝트를 하는 SI)으로 가거나 스스로 원티드나 사람인 같은 사이트에서 스타트업이나 백엔드 부서로 지원하였다. 나의 관심은 프론트엔드였고, 프론트엔드 담당 직무로 지원하였다. 면접 기회도 몇 번 가졌는데 기술적, 경험적 부족함을 느꼈다. 프리온보딩 프론트엔드 과정에 참여하게 된 이유이다. 나의 상황에 맞는 좋은 기회라고 생각했다. 지원하기 위한 선발과제를 준비해야 했다. 과제는 원티드 랜딩 페이지의 일부를 라이브러리 없이 기능을 만들어 리액트로 클론 코딩하는 것 이였다. 

## 대망의 1주차 시작

선발 과제를 통과하고 첫 수업이 시작 됐다. 첫 날에는 오리엔테이션과 팀 배정을 했다. 수업이 끝나고 팀원들과 Slack에서 처음 소통을 했을 때, 감회가 새로웠다. 과제에 대한 대화로 몇 시간을 회의했는데, `ZOOM`은 무료라이센스는 40분의 제한시간이 있기 때문에 제한시간이 없는 `Gather Town`으로 플랫폼을 이동하기로 결정하였다. 

![GatherIMG](/assets/images/post/img-2022-01-30-01.jpg "화상회의, 화면 공유 등 회의에 필요한 기능이 있다.") 


결과는 대 만족이였다. 아바타를 만들어 이동시킬 수 도 있고, 실제로 만나서 회의를 하는 기분이였다.(정말 그런 기분이 든다.)


### 토론 과제

[GITHUB REPO🚀](https://github.com/zerochae/1stWeek_interview-task)

3가지의 주제가 있다.

- CRA Project Structure: 디렉토리 구조를 어떻게 나눌 것인지?
- How to Write CSS: 어떤 CSS를 선택할 것인지? (SASS, CSS Modules 등)
- Commit Message: Git Commit Message의 포맷을 어떻게 정할것인지?

회의를 하면 할수록 몰랐던 지식을 알 수 있었기 때문에, 나에게 정말 중요한 시간이였다. 서로 조사한 내용을 발표한 다음, 나는 CSS 부분을 문서화 하기로 하였는데 과제가 끝난 뒤, 내가 작성한 우리팀의 과제가 잘된 예시로 선정되어서 기분이 정말 좋았다.

### 첫번째 과제 - 환율 계산기

[GITHUB REPO🚀](https://github.com/zerochae/1stweek-wirebarley-red-brick-task)

#### SetInterval in React

이 이슈에 관해서 유명한 포스트가 있다.

[Dan abramov - Making setInterval Declarative with React Hooks](https://overreacted.io/making-setinterval-declarative-with-react-hooks/)

[번역 - 리액트 훅스 컴포넌트에서 setInterval 사용 시의 문제점](https://velog.io/@jakeseo_me/%EB%B2%88%EC%97%AD-%EB%A6%AC%EC%95%A1%ED%8A%B8-%ED%9B%85%EC%8A%A4-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8%EC%97%90%EC%84%9C-setInterval-%EC%82%AC%EC%9A%A9-%EC%8B%9C%EC%9D%98-%EB%AC%B8%EC%A0%9C%EC%A0%90)

[정리가 잘 된 포스트 - setTimeout과 setInterval을 이용한 호출 스케줄링](https://ko.javascript.info/settimeout-setinterval)

앞서 선발 과제에서 라이브러리없이 이미지 슬라이더를 구현하기 위해 setInterval을 사용하여 일정 시간 간격으로 이미지가 옆으로 슬라이드 되도록 만들었지만, 제대로 작동하지 않았던 경험이 있었다. 팀원들 모두가 그 경험을 했고, 해결책을 알고 있었던 팀원이 커스텀 훅을 만들어 프로젝트에 사용하였다.

```jsx
import React, { useState, useEffect, useRef } from 'react';

function useInterval(callback, delay) {
  const savedCallback = useRef();

  // Remember the latest callback.
  useEffect(() => {
    savedCallback.current = callback;
  }, [callback]);

  // Set up the interval.
  useEffect(() => {
    function tick() {
      savedCallback.current();
    }
    if (delay !== null) {
      let id = setInterval(tick, delay);
      return () => clearInterval(id);
    }
  }, [delay]);
}
```

### 두번째 과제 - 상품 등록 페이지

[GITHUB REPO🚀](https://github.com/zerochae/1stweek-sirloin-task)

#### Context API

Global State Control을 위해 Context API를 사용하였다. 코드를 계속 추가해 나가다 보니, 심각한 오류를 발견하게 되었는데, Global State가 Update되면 하위 컴포넌트가 전부 렌더링 되는 것이였다. `Context.Provider`는 저장된 값이 변경되면 `UseContext(Context)`를 사용하는 컴포넌트도 같이 렌더링을 하기 때문이였다. 

```jsx
// 사용한 전역 state store / PDdata.js

const PDdata = (props) => {
  const [OptionSetList, setOptionLIst] = useState([]);
  const [ProductInfoList, setProductInfoLIst] = useState(PIData);
  const [ProductInfoOrder, setProductInfoOrder] = useState([PIOrder]);
  const [PDMileage, setPDMileage] = useState(true);
  const [ProductImage, setProductImage] = useState(PDImage);
  const [ETC, setETC] = useState(false);
  const [PScontentBox, setPScontentBox] = useState(ProductSalesperiods);
  const [PspState, setPspSate] = useState(PSPInfo);
  const [ProductDelivery, setProductDelibery] = useState(PDInfo);

  const PDdata = {
    OptionSetData: {
      state: OptionSetList,
      setstate: setOptionLIst,
    },
    PIData: {
      state: ProductInfoList,
      setState: setProductInfoLIst,
    },
    PIOrder: {
      state: ProductInfoOrder,
      setState: setProductInfoOrder,
    },
    PDMileage: {
      state: PDMileage,
      setState: setPDMileage,
    },
    PDImage: {
      state: ProductImage,
      setState: setProductImage,
    },
    ETC: {
      state: ETC,
      setState: setETC,
    },
    PScontentBox: {
      state: PScontentBox,
      setState: setPScontentBox,
    },
    PspInfoState: {
      state: PspState,
      setState: setPspSate,
    },
    PDInfo: {
      state: ProductDelivery,
      setState: setProductDelibery,
    },
  };

  return (
    <PDcontext.Provider value={PDdata}>{props.children}</PDcontext.Provider>
  );
};

// App / index.js

const App = () => {
  return (
    <div className="App">
      <GlobalStyle />
      <Nav />
      <Main>
        <PDdata>
          <Topbar/>
          <ProductSalesperiod />
          <ProductInfo />
          <PDOption/>
          <ProductImage/>
          <ProductInfoNotice/>
          <ProductDelivery/>
          <PDMileage/>
          <ETC/>
        </PDdata>
     </Main>
    </div>
  );
};
export default App;
```

이런 경험을 통해 Context API를 사용하기 위한 좋은 환경은 전역 State에서 Static한 Data를 하위 컴포넌트에 전달해줘야 할 때라고 생각했다.