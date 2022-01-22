---
title: Execution Context
date: 2022-01-22 09:00
description: 실행 컨텍스트
category: JavaScript
icon: js
tags: [JavaScript]
---

# 실행 컨텍스트

**실행 컨텍스트(Execution Context)**는 scope,hoisting,this,function,closure 등의 동작원리를 담고 있는 JavaScript의 핵심원리이다.

실행 컨텍스트는 실행할 코드에 제공할 환경 정보들을 모아놓은 객체이며, JavaScript의의 동적 언어로서의 성격을 가장 잘 파악할 수 있는 개념. ECMAScript 스펙에 따르면 실행 컨텍스트는 **실행 가능한 코드가 실행 되기 위해 필요한 환경을 형상화하고 구분하는 추상적인 개념**이라고 정의하고 있다. 다시 말하면 실행 컨텍스트는 **실행 가능한 코드가 실행되기 위해 필요한 환경**이라고 할 수 있다. 여기서 말하는 실행 가능한 코드는 다음과 같다.

- 전역 코드 : 전역에 존재하는 코드
- 함수 코드 : 함수 내에 존재하는 코드
- Eval 코드 : eval 함수로 실행되는 코드

일반적으로 실행 가능한 코드는 전역 코드와 함수 내 코드이다.

자바스크립트 엔진은 코드를 실행하기 위하여 실행에 필요한 여러가지 정보를 알고 있어야 한다. 그 정보는 다음과 같다.

- 변수 : 전역변수, 지역변수, 매개변수, 객체의 프로퍼티
- 함수 선언
- 변수의 유효범위(Scope)
- this

이와 같이 실행에 필요한 정보를 형상화하고 구분하기 위해 자바스크립트 엔진은 실행 컨텍스트를 물리적 객체의 형태로 관리한다.

실행 컨텍스트는 다음과 같은 조건에서 실행된다.

- 전역공간에서 anonymous function() 실행
- eval() 사용
- 함수 사용
- {} block 사용

일반적으로 함수를 이용한 실행 컨텍스트를 사용한다.

실행 컨텍스트가 실행되면 콜 스택(call stack)에 쌓이게 된다.

```js
var gnar = "신사 나르"; // 전역 컨텍스트
function outer() {
  // outer 컨텍스트
  function inner() {
    // inner 컨텍스트
    console.log(gnar); // undefined
    var gnar = "우주비행사 나르";
    console.log(gnar); // '우주비행사 나르'
  }
  inner();
  console.log(gnar); // '신사 나르'
}
outer();
console.log(gnar); // '신사 나르'
```

위와 같은 코드가 있을때, 실행 컨텍스트의 스택은 다음과 같은 순서로 실행된다.

- 프로그램 실행: `[전역컨텍스트]`
- outer 실행: `[전역컨텍스트,outer]`
- inner 실행: `[전역컨텍스트,outer,inner]`
- inner 종료: `[전역컨텍스트,outer]`
- outer 종료: `[전역컨텍스트]`

`outer()` 가 실행중에 `inner()`를 만났고 `inner()`가 종료 후에 `outer()`가 종료된 것을 확인할 수 있다. 이 처럼 현재 실행 중인 컨텍스트에서 이 컨텍스트와 관련없는 코드(다른 함수)가 실행되면 새로운 컨텍스트가 생성된다. 이 컨텍스트는 스택에 쌓이게 되고 컨트롤(제어권)이 이동한다.

정리하자면

1. 전역 코드로 컨트롤이 진입하면 전역 실행 컨텍스트가 생성되고 실행 컨텍스트 스택이 쌓인다. 전역 실행 컨텍스트는 application이 종료될 때까지 유지된다.
2. 함수를 호출하면 해당 함수의 실행 컨텍스트가 생성되며 직전에 실행된 코드 블록의 실행 컨텍스트 위에 쌓인다.
3. 함수 실행이 끝나면 해당 함수의 실행 컨텍스트를 파기하고 직전의 실행 컨텍스트에 컨트롤을 반환한다.

## 실행 컨텍스트 생성 로직

실행컨텍스트는

1. Creation Phase
2. Execution Phase

두 단계를 거쳐 생성된다.

### Creation Phase

이 단계에서는 두가지 일이 일어난다.

- Lexical Environment 구성 요소가 생성된다.
- Variable Environment 구성 요소가 생성된다.

실행 컨텍스트는 개념적으로 아래와 같이 나타낼 수 있다.

```js
ExecutionContext = {
  LexicalEnvironment = <ref. to LexicalEnvironment in memory>
  VariableEnvironment = <ref. to VariableEnvironment in memory>
}
```

#### Lexical Environment

Identifier-Variable Mapping이 이루어진다. 다시 말하면 Identifier Resolution 식별자 해결을 하기위한 공간이다.

참조 대상 식발자인 identifier는 함수와 변수의 이름과 같이 식발자로 Unique하다. 자바스크립트는 이 규칙대로 식별자를 찾는다. 즉 변수와 해당 변수에 대입된 값이 매핑되는 곳이 Lexical Environment라 할 수 있다.

```js
// code snipet
var gnar = "나르";
var teemo = "티모";

function myChamp() {
  console.log("fizz");
}
```

```js
// Lexical Environment
lexicalEnvironment = {
  ganr: '나르',
  teemo: '티모',
  myChamp: <ref. to myChamp function>
}
```

Lexical Environment에는 세 가지 구성요소가 있다.

1. Environment Records
2. Reference to the outer Environment
3. This Binding

**Environment Records**

Lexical Environment안에 함수와 변수선언을 저장하는 곳이다. 여기서 호이스팅이라는 개념이 사용된다. 호이스팅은 코드 해석을 좀 더 수월하게 하기 위해 EnvironmentRecord의 수집 과정을 추상화한 개념이다.

- Declarative Environment Record
  변수와 함수 선언을 저장한다.
- Object Environment Record
  전역코드의 Lexical Environment는 객체 환경 레코드(Object Environment Record)를 포함한다. 변수와 함수의 선언과는 다르게 전역 객체(window)도 저장한다.

**Reference to the outer Environment**

외부 환경에 대한 참조는 외부에 있는 Lexical Environment환경으로 접근할 수 있다는 의미이다. 즉, JavaScript 엔진은 현재의 Lexical Environment에서 변수를 찾지 못했다면 외부 환경에서 해당 변수를 찾아 볼 수 있다는 뜻이다. 이는 스코프가 형성되었다는 말이고, 스코프 체인을 통해 상위 컨텍스트에 접근할 수 있다는 말이다.

**This Binding**

`this`의 값 할당이 여기서 결정된다. 전역 실행 컨텍스트(Global Execution Context)에서 `this`는 전역객체이다. (`window`)

함수 실행 컨텍스트에서는 `this` 함수가 호출되는 방식에 따라 다른데 함수가 객체 참조에 의해서 호출되면 해당객체로 설정되고 그렇지 않을 경우 전역 객체(`window`)를 가르키거나 strict mode 에서는 `undefined`를 가르킨다.

```js
let rage = 100;

const gnar = {
  name: "gnar",
  rage: 0,
  q: function () {
    console.log("부메랑 던지기");
    this.rage += 50;
  },
  getRage: function () {
    console.log(this.rage);
  },
};

gnar.q(); // '부메랑 던지기'

gnar.getRage(); // 50

const getGnarRage = gnar.getRage;
getGnarRage(); // undefined
```

`getRage()`는 gnar를 참조객체로 호출했기 때문에 여기서 this는 gnar를 가르키게 되어 `this.rage`는 전역변수 rage가 아닌 gnar의 rage로 적용되고, 50이 출력된다.

`getGnarRage()`는 객체참조값이 없다. 앞에 EntityGraph의 탐색을 시작하기 `.`도 없기 때문에 전역 객체를 참조하는 것으로 가정하는데, `let` 변수는 `window` 객체에 저장되지 않기 때문에 `undefined`를 출력한다.

```js
var rage = 100;

const gnar = {
  name: "gnar",
  rage: 0,
  q: function () {
    console.log("부메랑 던지기");
    this.rage += 50;
  },
  getRage: function () {
    console.log(this.rage);
  },
};

gnar.q(); // '부메랑 던지기'

gnar.getRage(); // 50

const getGnarRage = gnar.getRage;
getGnarRage(); // 100
```

위 코드와 차이점은 전역변수 rage를 var 변수를 사용해 선언해줬다는 것이다. 그리고 마지막 함수에서 위와 다른 결과를 보이는데 `var` 변수는 `window`객체에 저장하고 있기 대문에 `window.rage`로 접근이 가능해지고, `100`을 출력하게 된다.

#### VariableEnvironment

VariableEnvironment 또한 LexicalEnvironment의 모든 프로퍼티와 요소를 가진다.

ES6에서 한가지 차이점이 생기는데, VariableEnvironment는 변수 `var`만 저장하고 LexicalEnvironment는 함수 선언과 변수 `let`,`const` 바인딩을 저장한다.

실행 컨텍스트를 생성할 때 VariableEnvironment에 정보를 먼저 담은 다음, 이를 복사해서 LexicalEnvironment를 만든다.

주로 활용하는 것은 LexicalEnvironment의 스냅샷 유지이다.

### Execution Phase

이 페이즈에서는 모든 변수에 대한 값 할당이 완료되고 최종적으로 코드들이 실행된다.

```js
let gnar = "나르";
const fizz = "피즈";
var callMyChamp;

function myChamp(champ1, champ2) {
  var veigar = "베이가";
  return console.log(`${champ1},${champ2},${veigar}`);
}

callMyChamp = myChamp("나르", "피즈");
```

위 코드가 실행될 때 자바스크립트 엔진은 전역 실행 컨텍스트를 만들고 전역 변수 중 `함수`와 `let`,`const`는 LexicalEnvironment로 `var`는 VariableEnvironment로 key/value 매핑된다.

```js
GlobalExecutionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      //Identifier bindings go here
      gnar: < uninitialized >,
      fizz: < uninitialized >,
      myChamp: < func >
    },
    outer: <null>,
    ThisBinding: <Global Object>
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // Identifier bindings go here
      callMyChamp: undefined,
    },
    outer: <null>,
    ThisBinding: <Global Object>
  }
}
```

Execution Phase가 실행되는 동안 각각의 변수 할당이 수행된다.

```js
GlobalExecutionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      //Identifier bindings go here
      gnar: '나르',
      fizz: '피즈',
      myChamp: < func >
    },
    outer: <null>,
    ThisBinding: <Global Object>
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Object",
      // Identifier bindings go here
      callMyChamp: undefined,
    },
    outer: <null>,
    ThisBinding: <Global Object>
  }
}
```

`myChamp("나르", "피즈");` 함수가 호출되는 시점에서 새로운 함수 실행 컨텍스트는 함수코드를 실행한다. 함수 실행 컨텍스트는 생성 단계(Creation Phase)와 유사한 형태이다.

```js
FunctionExecutionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
      Arguments: {0: '나르', 1: '피즈', length: 2},
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>,
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
      veigar: undefined
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>
  }
}
```

다음, 실행 컨텍스트는 함수 내부의 변수들에 할당하는 실행단계를 거친다. 따라서 함수 실행 컨텍스트는 아래와 같은 모습을 보인다.

```js
FunctionExecutionContext = {
  LexicalEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
      Arguments: {0: '나르', 1: '피즈', length: 2},
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>,
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      Type: "Declarative",
      // Identifier bindings go here
      veigar: '베이가',
    },
    outer: <GlobalLexicalEnvironment>,
    ThisBinding: <Global Object or undefined>
  }
}
```

위와 같이 함수가 완료된 이후 반환된 값을 내부에 저장한다. 그 후 Global Lexical Environment가 업데이트 된 다음 전역 코드가 완료된다.

### 정리...

1. 실행 컨텍스트는 실행할 코드에 제공할 환경 정보들을 모아놓은 객체이다.
- 전역 공간에서 자동으로 생성되는 전역 컨텍스트
- eval()
- func()

2. 실행 컨텍스트 객체는 활성화 되는 시점에 `VariableEnvironment`, `LexicalEnvironment`, `ThisBinding` 세 가지 정보를 수집한다.

3. 실행 컨텍스트를 생성할 때 `VariableEnvironment`와 `LexicalEnvironment`가 동일한 내용으로 구성된다.

4. `LexicalEnvironment`는 함수 실행 도중에 변경되는 사항이 즉시 반영된다.

5. `LexicalEnvironment`와 `VariableEnvironment`는 `environmentRecord`와 `outerEnvironmentReference`로 구성 되어 있다.
- `environmentRecord`는 매개변수 식별자, 변수 식별자, 선언한 함수의 식별자 등을 수집한다.
  - 여기서 호이스팅이 사용된다.
  - 호이스팅은 코드 해석을 좀 더 수월하기 하기 위해 `environmentRecord`의 수집 과정을 추상화한 개념이다.
  - 변수 선언부와 함수 선언문에 호이스팅이 발생한다.
  - 함수 표현식을 사용할 경우 함수의 선언부만 호이스팅이 발생한다.
- `outerEnvironmentReference`는 상위(직전) 컨텍스트의 `lexicalEnvironment` 정보를 참조한다.
  - 여기서 스코프가 형성된다. 스코프 체인을 통해 상위 컨텍스트에 접근할 수 있다.
  - 스코프는 변수의 유효범위를 말한다.



**어려워!**