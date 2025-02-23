# `ts-enum` 개발기록

## 📌 1. 왜 만들었을까? 🤔  
나는 TypeScript를 사용하면서, **Java의 Enum** 같은 기능이 필요했다.  
기본적인 `enum` 타입은 제공되지만, 다음과 같은 단점이 있었다.  

### ✅ **TS의 기본 `enum` 단점**
1️⃣ 런타임에 사라진다  → 객체처럼 사용할 수 없다.  
2️⃣ 메서드를 추가할 수 없다  → `enum`에 `isActive()` 같은 함수를 넣을 수 없다.  
3️⃣ 타입 안전성이 부족하다  → `enum`의 값이 예상치 못한 문자열/숫자로 처리될 가능성이 있다.  

그래서 Java의 **객체형 Enum** 스타일을 그대로 TS에서 구현해보고 싶었다.  
> **"TypeScript에서도 Java 같은 Enum을 사용할 수 없을까?"**  

이 고민 끝에 **객체 기반의 Enum 라이브러리**를 직접 만들기로 했다! 🎉  

---

## 📌 2. 어떻게 만들었을까? ⚙️  

처음부터 직접 구현하려 했던 것은 아니었다.  
기존에 [`class-enum`](https://github.com/banlife/class-enum)이라는 라이브러리가 있었고,  
이걸 사용하면 TypeScript에서도 Java 스타일의 Enum을 사용할 수 있었다.  

하지만, 몇 가지 불편한 점이 있었다.  

### ✅ 기존 `class-enum` 라이브러리의 단점
1️⃣ **제네릭이 강제** → 타입이 제대로 추론되지 않아, 제네릭을 사용할 때가 많았다.
 - 타입 추론을 더 강화하도록 결정

2️⃣ **필요 없는 기능이 많음**  → 가볍게 사용할 수 있도록 정리하고 싶었음  
 - function의 기본 속성인 call, apply 등을 가지고 있다. 
 - value, name() 등과 같이 중복 속성이 있다.

그래서 **`class-enum`의 코드 구조를 참고하면서, 필요 없는 부분을 제거하고,  
사용하기 편한 형태로 다듬어서 `ts-enum`을 만들게 되었다.** 

---

## 📌 3. 객체 기반 Enum

기존 TypeScript의 `enum`은 런타임에서 사라지기 때문에,  
객체처럼 사용할 수 없다는 점이 가장 큰 단점이었다.

하지만 **객체 기반 Enum**을 만들면, 다음과 같은 장점이 있다.

### ✅ **객체 기반 Enum의 장점**
✔ **실제 값(value) 기반으로 동작** → 타입 안전성이 높아짐  
✔ **런타임에서 유지됨** → 객체이므로 `console.log()`로 확인 가능  
✔ **메서드 추가 가능** → `isActive()`, `toString()` 같은 기능 추가 가능  

즉, `enum`의 간편함과 `class`의 유연함을 결합한 **최적의 방식**이라고 볼 수 있다.  

---

## 📌 4. Base Enum class 만들기

공통 기능을 담고 있는 추상 클래스 Enum을 만들었다.

```ts 
export default abstract class Enum {
  private readonly value: string;

  protected constructor(value: string) {
    if (!value) throw new Error("Invalid Enum Value");
    this.value = value;
  }

  static values<T extends typeof Enum>(this: T): T[] {
    return Object.values(this).filter(value => value instanceof this) as T[];
  }

  static valueOf<T extends typeof Enum>(this: T, value: string): T | undefined {
    return this.values().find(enumInstance => enumInstance.toString() === value);
  }

  toString() {
    return this.value;
  }
}

// usage

class UserStatus extends Enum {
  static readonly ACTIVE = new UserStatus("ACTIVE");
  static readonly INACTIVE = new UserStatus("INACTIVE");
}

console.log(UserStatus.values()); // ✅ [UserStatus.ACTIVE, UserStatus.INACTIVE]
console.log(UserStatus.valueOf("ACTIVE")); // ✅ UserStatus.ACTIVE
```

## 📌 5. class를 객체로 변환하는 `createEnum` 만들기  

TypeScript에서 **클래스(Class)** 는 **Function 타입** 이기 때문에,  
불필요한 함수(`apply`, `call`, `bind`, `prototype` 등)를 기본적으로 포함하고 있었다.  

하지만 **내가 원하는 건 "순수한 데이터 객체"로 Enum을 관리하는 것이었다.**  
즉, `Enum`이 **실제 값만 가지도록 하고, 불필요한 메서드를 제거하고 싶었다.**  

그래서 **`createEnum()` 함수를 만들어서, 클래스에서 불필요한 기능을 제거하고,  
순수한 데이터 객체로 관리할 수 있도록 개선했다.**  

---

### ✅ `createEnum()`의 역할  
✔ **클래스를 객체로 변환하여, `apply`, `call`, `bind` 같은 불필요한 기능을 제거**  
✔ **`values()`와 `valueOf()` 같은 유틸 함수는 유지하면서, 데이터만 보존**  
✔ **런타임에서도 안전하게 Enum을 유지**  

```ts
export function createEnum<T extends typeof Enum>(EnumClass: T) {
  const instanceMap: Record<string, Enum> = {};

  for (const key of Object.keys(EnumClass)) {
    if (EnumClass[key] instanceof Enum) {
      instanceMap[key] = EnumClass[key] as Enum;
    }
  }

  return Object.freeze({
    ...instanceMap,
    values: () => Object.values(instanceMap),
    valueOf: (value: string) => instanceMap[value],
  });
}
```

```ts
const NativeUserStatus = class UserStatus extends Enum {
  static readonly ACTIVE = new UserStatus("ACTIVE");
  static readonly INACTIVE = new UserStatus("INACTIVE");
};

const TransformedUserStatus = createEnum(NativeUserStatus);

console.log(typeof NativeUserStatus); 
// ✅ "function" -> apply, call 등 불필요 프로퍼티를 가지고 있다.

console.log(typeof TransformedUserStatus); 
// ✅ "object" -> 순수 데이터 형태로 캐스팅된다.
```

## 📌 6. 기본 `enum` vs `class` vs `createEnum` 비교

TypeScript에서 `enum`을 사용할 때, 여러 가지 방식이 가능하다.  
각각의 차이점을 명확히 비교해보자.

| 방법 | 값 확인 (`console.log`) | 메서드 추가 가능 | 런타임 유지 | 타입 안전성 | 불필요 속성 |
|------|-----------------|--------------|------------|------------|------------|
| `enum` | ❌ 사라짐 | ❌ 불가능 | ❌ 사라짐 | ⚠️ 약함 | - |
| `class` | ✅ 유지됨 | ✅ 가능 | ✅ 유지됨 | ✅ 강함 | ❌ 제거 불가|
| `createEnum` | ✅ 유지됨 | ✅ 가능 | ✅ 유지됨 | ✅ 강함 | ✅ 제거 가능 |

---

✅ 결론

- 기본 enum은 런타임에서 사라지므로 한계가 있음.
- 클래스 기반 Enum은 유지되지만, apply, call 등의 불필요한 기능을 가짐.
- createEnum()을 사용하면, 순수 데이터 객체로 변환되어 최적의 Enum 사용 가능
