---
title: "any와 unknown 타입에 대해서"
author: saemii
categories:
  - Typescript
tags:
  - [Typescript, Next.js]
date: 2025-12-09
last_modified_at: 2025-12-09
pin: true
---

# 🍊any와 unknown

개발을 하던 중 다음과 같은 오류를 만났다.

> unknown에 할당할 수 없습니다.

해당 값의 타입을 any로 지정해둔 상태로 unknown 타입 변수에 넣으려고 하니 오류가 발생한 것이었다.
사실 unknown이나 any나 둘 다 모든 타입을 받을 수 있는 거 아닌가? 왜 오류가 발생했지? 라는 의문이 들었고,
한 번 의식하고 보니 이름부터 다른 타입인데 당연한 건가? 싶으면서도,
정확히 어떤 차이가 있는지 궁금해졌다.

## 💛any

any는 일종의 타입스크립트 **탈출**구문이다.
즉, 코드 일부를 자바스크립처럼 동작하고 싶을 때, 타입의 제한을 받고 싶지 않을 때 any를 사용할 수 있다.

즉, any를 사용하게 되면 타입스크립트는 더 이상 해당 코드의 오류를 잡아내지 못한다.
예를 들어 보자.

```typescript
const data: any = { fruit: "banana" }

console.log(data.fish)
```

data에는 fish라는 key가 없으므로 에러가 발생해야 옳지만 any 타입을 주었기 때문에 타입스크립트는 더이상
타입을 검사하지 않게 되고, 자연스럽게 해당 `console.log` 에서도 에러를 잡아내지 못한다.

## 💚unknown

unknown은 TypeScript 3.0부터 도입된 top type(최상위 타입)으로 기존 any를 대체한다.
핵심은 다음과 같다.

1. 무엇이든 `unknown` 타입에 할당할 수 있다.

```typescript
let u: unknown

u = 123 // ⭕
u = "hi" // ⭕
u = {} // ⭕
```

2. 하지만 `unknown`은 아무 타입에나 할당할 수 없다.

```typescript
let u: unknown

let a: any = u // ⭕
let b: unknown = u // ⭕
let c: number = u // ❌
```

unknown 변수에는 안전한 타입 `unknown`, `any`만 넣을 수 있다.

## 😨 unknown 변수에 any도 넣을 수 있다고?

그런데 의문이 생겼다. unknown 변수에 any를 받을 수 있다면 내 코드에서는 왜 에러가 생긴걸까?

일단 아래와 같은 코드를 보자

```typescript
let u: unknown
let a: any
u = a
```

unknown인 u에 any인 a 값을 넣었을 때 에러는 발생하지 않는다.
다만 typescript가 판단하기에 그 값이 정말 `any`인지 확신하지 못할 경우 에러를 발생시키는 것이다.

## 💖 unknown을 사용해보자!

unknown이 자주 사용되는 경우는 다음과 같다.

- API 응답
- json.parse() 결과

즉 해당 경우는 현재 타입을 100% 확신할 수 없는 값이다.

unknown은 받는 건 자유지만 쓰는건 제한 되기 때문에, 사용 시에는 반드시 타입을 좁혀주어야 한다.

```typescript
let fruit: unknown = getFruitList()
fruit.toUpperCase() //오류! 아직 fruit가 string인지 모르기 때문
```

### 📌 타입을 좁혀보자!

1. `typeof` 사용하기

```typescript
if (typeof fruit === "string") {
  fruit.toUpperCase()
} else if (typeof fruit === "number") {
  console.log(fruit)
}
```

2. 배열: `Array.isArray()` 사용하기
   `Array.isArray()` 는 전달된 값이 Array인지 판단한다.

   예를 들어, 아래와 같이 사용해 Array인지 판단할 수 있다.

```typescript
console.log(Array.isArray([1, 3, 5])) // true
```

```typescript
if (Array.isArray(fruit)) {
  console.log(fruit.length)
}
```

3. 객체: `in` 사용하기

`in`은 객체에 특정 프로퍼티(키)가 존재하는지 확인하는 연산자다.

```typescript
const fruit = { price: 100 }
console.log("price" in fruit) // true
```

```typescript
if (typeof value === "object" && value !== null && "price" in value) {
  console.log(value.price) // true
}
```

이런 방법을 통해 unknown을 안전하게 사용할 수 있다.

- <https://www.typescriptlang.org/ko/play/?#example/any>
- <https://www.typescriptlang.org/ko/play/?#example/unknown-and-never>
- <https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-0.html#new-unknown-top-type>
- <https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray>
