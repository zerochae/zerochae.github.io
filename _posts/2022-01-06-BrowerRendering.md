---
title: FrontEnd &#35;1
date: 2022-01-06 09:00
description: Browser Rendering
category: FrontEnd
tags: [FrontEnd,Web,Browser]
---

### Structure of Browser

<br>

![브라우저의 구조](/assets/images/post/img-2022-01-06-01.png)

<div style="text-align:center;font-style:oblique;font-weight:600;">

출처: https://d2.naver.com/helloworld/59361

</div>

<br>

**브라우저의 주요 구성 요소는 다음과 같다.**

- 사용자 인터페이스: 주소 표시줄, 이전/다음 버튼, 북마크 메뉴 등 요청한 페이지를 보여주는 창을 재외한 나머지 모든 부분이다.
- 브라우저 엔진: 사용자 인터페이스와 렌더링 엔진 사이의 동작을 제어한다.
- 렌더링 엔진: 요청한 콘텐츠를 표시한다. 예를들어, HTML을 요청하면 HTML과 CSS를 파싱하여 화면에 표시한다.
- 통신: HTTP 요청과 같은 네트워크 호출에 사용된다. 이것은 플랫폼 독립적인 인터페이스이고 각 플랫폼 하부에서 실행된다.
- UI 백엔드: 콤보 박스와 창 같은 기본적인 장치를 그린다. 플랫포에서 명시하지 않은 일반적인 인터페이스로서, OS 사용자 인터페이스 체계를 사용한다.
- 자바스크립트 해석기: 자바스크립트 코드를 해석하고 실행한다.
- 자료 저장소: 자료를 저장하는 계층이다. 쿠키를 저장하는 것과 같이 모든 종류의 자원을 하드 디스크에 저장할 필요가 있다. 

<hr />

### Rendering Engine

#### Rendering Engines

렌더링 엔진의 역할은 요청받은 내용을 브라우저 화면에 나타내는 일이다. HTML, CSS, JavaScript 등의 파일을 브라우저가 화면에 표시할 수 있도록 변환하여 픽셀 단위로 나타낸다.

|Browser|Rendering Engine|
|---|---|
|IE|Trident|
|Edge|EdgeHTML,Blink|
|Chrome|Webkit,Blink|
|Safari|Webkit|
|FireFox|Gecko|

브라우저마다 사용하는 렌더링 엔진들이 다르다. 때문에, 같은 소스가 브라우저마다 다르게 그려지는 크로스 브라우징 이슈가 발생하게 된다.(자바스크립트 엔진이 달라서 발생하기도 한다.)

크롬 브라우저는 사파리 브라우저에서 사용하는 Webkit을 사용하다가 버전 28이후 Webkit 소스를 Fork하여 Blink 엔진을 만들어 사용하고 있다.

웹킷은 리눅스 플랫폼에서 동작하기 위해 제작된 오픈소스 엔진인데, 애플이 맥과 윈도우에서 사파리를 지원하기 위해 수정을 가했다.

#### Work Flow

렌더링 엔진은 통신으로부터 요청한 문서의 내용을 얻는 것으로 시작하는데 문서의 내용은 보통 8KB 단위로 전송된다.

![브라우저의 동작 과정](/assets/images/post/img-2022-01-06-02.png)

렌더링 엔진은 HTML 문서를 파싱하고 "Content Tree" 내부에서 태그를 DOM 노드로 변환한다. 그리고 외부 CSS 파일과 함께 포하된 스타일 요소도 파싱한다. 스타일 정보와 HTML 표시 규칙은 "Render Tree"라고 부르는 또 다른 트리를 생성한다.

렌더 트리 생성이 끝나면 Layout(또는 Reflow)가 시작되는데, 이 과정은 각 노드가 화면의 정확한 위치에 표시하기 위해 위치와 크기를 계산하는 과정을 말한다.

마지막으로 계산된 위치와 크기 등의 스타일들이 실제 픽셀로 표현하는 과정이 시작되는데 이를 Paint(또는 Rasterizing)라고 한다.

렌더링 엔진은 좀 더 나은 사용자 경험을 위해 가능하면 빠르게 내용을 표시하는데 모든 HTML을 파싱할 때까지 기다리지 않고 배치와 그리기 과정을 시작한다. 네트워크로부터 나머지 내용이 전송되기를 기다리는 동시에 받은 내용의 일부를 먼저 화면에 표시하는 것이다.

#### Detail

![Webkit](/assets/images/post/img-2022-01-06-03.png)

<div style="text-align:center;font-style:oblique;font-weight:600;">

출처 : https://beomy.github.io/tech/browser/browser-rendering/

</div>

<br />

위는 Webkit의 렌더링 동작 과정이다.

<style>
  :root 
  {
    --yellow: #FFC000; 
    --blue:   #5B9BD5;
    --gray:   #A5A5A5;
    --green:  #70AD47;
  }
</style>

<!--
<span style="color:var(--yellow)">HTML</span>
<span style="color:var(--blue)">parsing</span>
<span style="color:var(--gray)">DOM</span>
<span style="color:var(--green)">DOM Tree</span>
-->

1. <span style="color:var(--yellow)">HTML</span>을 <span style="color:var(--blue)">parsing</span>하여 <span style="color:var(--gray)">DOM Node</span>를 만든다. 이 <span style="color:var(--gray)">DOM Node</span>들이 병합하여 <span style="color:var(--green)">DOM Tree</span>를 만든다.
2. <span style="color:var(--yellow)">CSS</span>를 <span style="color:var(--blue)">parsing</span>하여 <span style="color:var(--green)">style 규칙</span>을 만든다.
3. <span style="color:var(--green)">DOM Tree</span>와  <span style="color:var(--green)">Style 규칙</span>을 사용하여, <span style="color:var(--blue)">Attachment</span> 라는 과정을 통해 <span style="color:var(--green)">Render Tree</span>를 생성한다.
4. <span style="color:var(--green)">Render Tree</span>를 <span style="color:var(--blue)">배치(layout)</span>한다.
5. <span style="color:var(--green)">Render Tree</span>를 화면에 <span style="color:var(--blue)">그린다(Painting).</span>

![Gecko](/assets/images/post/img-2022-01-06-04.png)

<div style="text-align:center;font-style:oblique;font-weight:600;">

출처 : https://beomy.github.io/tech/browser/browser-rendering/

</div>

<br />

위는 Gecko의 렌더링 동작 과정이다.

Webkit과 Gecko는 용어가 조금 다르지만 렌더링 과정은 유사하다.

|Webkit|Gecko|Description|
|---|---|---|
|Render Tree|Frame Tree|Rendered Node Tree|
|Render Object|Frame|Rendered Node|
|Layout|Reflow|The process of placing the rendered nodes|
|Attachment|Frame Constructor|The process of creating the rendered node tree |
|-|Content Sink|The process of creating the rendered DOM node|

<hr />

### Parser

파싱은 서버로부터 전송받은 문서의 문자열을 브라우저가 이해할 수 있는 구조로 변환하는 과정을 말한다. 파싱 결과는 문서 구조를 나타내는 노드 트리인데, 파싱 트리(parse tree) 또는 문법 트리(syntax tree)라고 한다.

#### DOM(Document Object Model)

```html
 <!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <link href="style.css" rel="stylesheet">
    <title>This is Title</title>
  </head>
  <body>
    <p>Hello <span>My Name</span> is zerochae </p>
    <div><img src="profile.jpg"></div>
  </body>
</html> 
```

위의 HTML 파일은 몇개의 텍스트, 하나의 이미지를 포함하고 있다. 브라우저는 이 페이지를 다음과 같은 과정을 통해 처리한다.

![DOM](/assets/images/post/img-2022-01-06-05.png)

1. 변환(Conversion): 브라우저가 HTML의 원시 바이트를 디스크나 네트워크에서 읽어와서, 해당 파일에 대해 지정된 인코딩에 따라 개별 문자로 변환한다.
2. 토큰화(Tokenizing): 브라우저가 문자열을 [W3C HTML5 표준](https://html.spec.whatwg.org/multipage/)에 지정된 고유 토큰으로 변환한다.
3. 렉싱(Lexing): 토큰을 해당 속성 및 규칙을 정의한 객체(Nodes)로 변환한다.
4. DOM 생성(DOM construction): HTML은 상위-하위 관계로 정의할 수 있어, 트리 구조로 나타낼 수 있다. 렉싱 과정을 거쳐 생성된 노드들을 트리 구조로 변환한다.

![DOM node](/assets/images/post/img-2022-01-06-06.png)

이 전체 프로세스의 최종 출력이 위 DOM이며, 브라우저는 이후 모든 페이지 처리에 이 DOM을 사용한다.

#### CSSOM(CSS Object Model)

![CSSOM](/assets/images/post/img-2022-01-06-07.png)

HTML과 마찬가지로, 수신된 CSS 규칙을 브라우저가 이해하고 처리할 수 잇는 형식으로 변환해야한다. 따라서 HTML 대신 CSS에 대해 HTML 프로세스를 반복한다.

브라우저는 페이지의 DOM을 생성하는 동안 외부 CSS Style Sheet인 style.css를 참초하는 문서의 헤드 섹션에서 링크 태그를 접한다. 페이지 렌더링하는 데 이 리소스가 필요할 것이라고 판단한 브라우저는 이 리소스에 대한 요청을 즉시 발송하고 요청의 결과로 다음 컨텐츠가 반환된다.

```css
body {
  font-size: 16px
}

p{
  font-weight: bold
}

span{
  color: red
}

p span{
  display:none
}

img{
  float: right
}
```

![CSSOM Tree](/assets/images/post/img-2022-01-06-08.png)

CSSOM은 트리 구조를 가진다. 페이지에 있는 객체의 최종 스타일을 계산할 때 브라우저는 해당 노드에 적용 가능한 가장 일반적인 규칙(예: body 요소의 하위인 경우 모든 body 스타일 적용)으로 시작한 후 더욱 구체적인 규칙을 적용하는 방식으로, 즉 '하향식'으로 규칙을 적용하는 방식으로 계산된 스타일을 재귀적으로 세분화한다.

HTML 마크업 내에 직접(인라인) 스타일을 선언할 수 도 있지만 CSS를 HTML과 별도로 유지하면 컨텐츠와 디자인을 별도의 항목으로 처리할 수 있다. (즉, 디자이너는 CSS를 처리하고, 개발자는 HTML에만 집중할 수 있다.)

CSS 바이트가 문자로 변환된 후 차례로 토큰과 노드로 변환되고 마지막으로 `CSS Object Model(CCSOM)`이라는 트리 구조에 링크됩니다.

<hr/>

### Attachment

CSSOM Tree와 DOM Tree를 결합하여, 표시해야 할 순서로 내용을 그려낼 수 있도록 하기 위해 Render Tree를 형성한다. 이 과정을 WebKit에서는 Attachment라고 한다. Render Tree는 화면에 표시되는 각 노드의 위치를 계산하는 Layout에 사용되고 픽셀을 화면에 그리는 Paint 과정에도 사용된다.

![CSSOM Tree](/assets/images/post/img-2022-01-06-09.png)

Render Tree를 생성하려면 브라우저는 3가지 작업을 수행한다.

1. DOM Tree의 Root에서 시작하여 화면에 표시되는 노드 각각을 탐색한다.
  - 화면에 표시되지 않는 일부 노드들 (`script` , `meta` 태그 등)은  Render Tree에 반영되지 않는다.
  - CSS에 의해 화면에서 숨겨지는 노드들은 Render Tree에 반영되지 않는다. 위 예시에서 `span`노드의 경우 `display:none`이 설정되었기 때문에 Render Tree에 반영되지 않는다.
2. 화면에 표시되는 각 노드에 대해 적절하게 일치하는 CSSOM 규칙을 찾아 적용한다.
3. 화면에 표시되는 노드를 컨텐츠 및 계산된 스타일과 함께 내보낸다.

#### DOM Tree & Render Tree

Render Tree 생성에 화면에 표시되지 않는 노드들은 포함되지 않는다. 예를들어, `<head>` 태그와 같은 비시각적 DOM Node는 Render Tree에 추가되지 않는다.

뿐만 아니라 CSS로 인해 `display` 속성에 `none`이 할당된 노드들은 Render Tree에 추가되지 않는다. 하지만. `visibility:hidden`은 Render Tree에 포함된다. `visibility` 속성에 `hidden` 값이 할당된 노드는 화면에 공간을 차지하기 때문에 Render Tree에 포함된다.

<hr />

### Layout

```html
<div style="width:50%">
  <div style="width:50%">Hello World!</div>
</div>
```

![CSSOM Tree](/assets/images/post/img-2022-01-06-10.png)

위 페이지에는 중첩된 div가 포함되어 있다. 첫 번째(상위) div는 노드의 표시 크기를 뷰포트 너비의 50%로 설정하며, 상위 div에 포함된 두번째 div는 해당 너비를 상위 항목 너비의 50%(즉, 뷰포트 너비의 25%)로 설정한다.

Render Tree가 형성되고, 기기의 뷰포트 내에서 Render Tree의 노드가 정확한 위치와 크기를 계산하는 과정을 Layout  이라고 한다. 모든 상대적인 측정값은 화면에서 절대적인 픽셀로 변환된다. 즉 CSS에 상대적인 값인 %로 할당된 값들은 절대적인 값인 px 단위로 변환된다.

<hr />

### Painting

Render Tree의 각 Node를 화면의 실제 픽셀로 나타내는 과정을 Painting 이라고 한다. Painting 과정 후 브라우저 화면에 UI가 나타나게 된다.

이 경우 브라우저가 처리해야 할 작업이 상당히 많으므로 시간이 걸릴 수 도 있다. 즉 문서가 클수록 브라우저가 수행해야 하는 작업도 더 많아지며, 스타일이 복잡할수록 페인팅에 걸리는 시간도 늘어난다.

<hr />

### Summary

> 브라우저의 렌더링 단계를 요약하면 다음과 같다.

1. HTML 마크업을 처리하고 DOM Tree를 빌드한다. (DOM parsing)
2. CSS 마크업을 처리하고 CSSOM Tree를 빌드한다. (CSS parsing)
3. DOM , CSSOM을 결합하여 Rendering Tree를 형성한다. (Attachment)
4. Rendering Tree에서 Layout을 실행하여 각 노드의 기하학적 형태를 계산한다. (Layout)
5. 개별 노드를 화면에 Paint 한다. (Paint)