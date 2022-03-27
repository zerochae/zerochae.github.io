---
title: JavaScript &#35;5
date: 2022-03-27 09:00
description: JS Array.filter(Boolean) 으로 배열 관리하기
category: JavaScript
icon: js
tags: [JavaScript]
---

JavaScript에서는 falsy라는 개념이 있다.

[Falsy - 거짓 같은 값](https://developer.mozilla.org/ko/docs/Glossary/Falsy)

다음은 falsy에 해당하는 값 들이다.

```js
const falsy = [false, null, undefined, 0, -0, 0n, NaN, ""];
```

우리는 배열을 활용하며 falsy에 해당하는 값을 만나면 종종 예상하지 못한 오류에 직면하게 되는데, 이를 다음으로 예방할 수 있다.

```js
const test = ["a", 1, null, undefined, {}, [], [null], 0];

test.filter(Boolean); // [ "a", 1, {}, [], [null] ]
```

주의해야 할 점은 `{}`,`[]`,`[...falsy]` 는 falsy가 아닌 객체이다.

`filter(Boolean)` 메소드로 falsy 값을 판별할 수 있는 원리는 다음과 같다.

```js
// Boolean(param) 의 출력값

Boolean(null); // false
Boolean(0); // false
Boolean(1); // true

// test.filter(Boolean)는 test.filter(val=> Boolean(val)) 과 같다.

const test = [1, null, {}];

test.filter((value) => {
  // value === 1 -> Boolean(1) : true
  // value === null -> Boolean(null) : false
  // value === {} -> Boolean({}) : true

  return Boolean(value);
}); // result -> [1, {}]
```

지금까지 보통 falsy 값을 해결하기 위해

```js
arr.map((val) => {
  if (val) return val;
});
```

과 같이 `if`를 사용하여 체크했지만, 

`filter(Boolean)` 이라는 더 심플한 방법으로 오류를 예방 할 수 있다.
