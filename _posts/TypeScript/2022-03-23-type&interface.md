---
title: TypeScript &#35;1
date: 2022-03-22 09:00
description: Type & Interface
category: TypeScript
icon: ts
tags: [TypeScript]
---

## Commonality

일반적으로 `Type Alias`와 `Interface` 둘 다 같은 기능을 한다.

```ts
// interface

interface Person {
  name: string;
  age: number;
}

const zerochae: Person = {
  name: "권영채",
  age: "31",
};

// type

type Person = {
  name: string;
  age: number;
};

const zerochae: Person = {
  name: "권영채",
  age: "31",
};
```

## Difference

### 확장하는 방법

```ts
// interface

interface Person {
  name: string;
  age: number;
}

interface Student extends Person {
  school: string;
}

// type

type Person = {
  name: string;
  age: number;
};

type Student = Person & {
  school: string;
};

// class

class Student implements Person {
  constructor(public school: string);
}
```

### Intersection Type & Union Type

객체 타입이나 객체 타입 간의 곱 타입(Intersection Type, 교차 타입), 즉 정적으로 모양을 알 수 있는 객체 타입만 상속 키워드(`extends`,`implements`)를 사용할 수 있다. 합 타입(Union Type, 결합 타입)은 다른 방법을 사용해 관계를 정의한다.

```ts
// 합 타입에 대한  extends , implements 사용

// type

type Person = { name: string } | { age: number };

// interface

interface Student extends Person {
  school: string;
}
// error(TS2312)
// An interface can only extend an object type
// or intersection of object types with statically known members.

// class

class Student implements Person {
  constructor(public school: string);
}
// error(TS2422)
// A class can only implement an object type
// or intersection of object types with statically known members.
```

### Declarative Extension

- `type`은 새로운 속성을 추가하기 위해서 다시 같은 이름으로 선언 할 수 없다
- `interface`는 선언적 확장이 가능하다. (같은 이름으로 다시 선언 할 수 있다.)

```ts
//interface

interface Person {
  name: string;
}

interface Person {
  // name : string; --> 이미 위에서 선언되었기 때문에 자동으로 확장된다.( 선언적 병합이 가능하다.)
  age: number;
}

// type

type Person = {
  name: string;
};

type Person = {
  age: number;
};

// Error: Duplicate identifier 'Person' --> 중복 선언 불가능
```

### TypeScript 팀의 의도

TypeScript팀은 [개방-폐쇄-원칙](https://ko.wikipedia.org/wiki/%EA%B0%9C%EB%B0%A9-%ED%8F%90%EC%87%84_%EC%9B%90%EC%B9%99)에 따라 확장에 열려있는 JavaScript 객체의 동작 방식과 비슷하게 연결하도록 `interface`를 설계했다.

그래서 TypeScript팀은 가능한 `Type Alias`보다 `Interface`를 사용하고, 합 타입 혹은 튜플 타입을 반드시 써야 되는 상황이면 `Type Alias`를 사용하도록 권장하고 있다.
